# Shortest Path from Src to All other nodes in Graph 
There are 2 Algo to find the shortest path from source to all nodes in graph 
- 1. Simple Src to All nodes shortest path algo.
- 2. Dijkstra's ALgo 
   - Version 1 (to find only shortest path distance)
   - Version 2 (using Parent Vector to find shortest path from src to desti as well)
 -------------------------------------------
 note : the above 2 algos we talked about finds the shortest distance on the basis of the weights
 -------------------------------------------
 
## Shortest Path Source to All Nodes (Algo) 

idea : in this we apply the BFS on topological sort to with `minDist` array to store min dist from source to other node 
        alternatively we can use BFS order in place of TopoSort as well .
        
Source : 1 
![graph (2)](https://github.com/yashasviyadav1/Graphs/assets/124666305/3de6f71e-9f75-45ab-ba25-17f195a03348)

output : 
```
Enter source node : 1

shortest dist from src to 1 is 0
shortest dist from src to 2 is 0
shortest dist from src to 3 is 2
shortest dist from src to 4 is 5
shortest dist from src to 5 is 6
shortest dist from src to 6 is 2
```
```
Time : O(E+V)
Space : O(E+V)
```
```cpp
#include<iostream>
#include<unordered_map>
#include<vector>
#include<list>
#include<stack>
using namespace std;

class Graph{
    public:
        unordered_map<int,list<pair<int,int>>> AdjList;
    void insertEdge(int vertex1, int vertex2, int weight){
        AdjList[vertex1].push_back({vertex2,weight}); // directed weighted graph
    }

    // -------------
    void topoSortHelper(stack<int> &topoStk, unordered_map<int,bool> &visited, int node){
        visited[node] = true;
        
        for(pair<int,int> neighPair:AdjList[node]){
            int neigh = neighPair.first;
            if(!visited[neigh])
                topoSortHelper(topoStk, visited, neigh);
        }

        // ones all neigh for a node are explored, and we are returning back , just make sure to store the 'node' into Stk
        topoStk.push(node);
    }

    stack<int> topoSortFinder(){
        
        stack<int> topoStk;
        unordered_map<int,bool> visited;

        for(auto block:AdjList){
            int node = block.first;
            if(!visited[node])
                topoSortHelper(topoStk, visited, node);
        }

        return topoStk;
    }

    // ------------------
    vector<int> shortestDistAllNodes(stack<int> &topoStk, int src){
        
        vector<int> srcToNodeDist(AdjList.size() + 1, INT_MAX); // initially dis from src to all nodes is INFINITY
        srcToNodeDist[src] = 0; // dist from node to itself is always 0
        

        // applying bfs on topoStk
        while(!topoStk.empty()){
            int topNode = topoStk.top();
            topoStk.pop();

            //IMP, if we can not reach the topNode from src then skip this iteration
            if(srcToNodeDist[topNode] == INT_MAX)
                continue;

            // if we can reach the topNode from src, then check all neighs of topNode and update the dist if a shorter one is found 
            for(pair<int,int> neighPair:AdjList[topNode]){

                int neigh = neighPair.first;
                int weight = neighPair.second;

                if(srcToNodeDist[topNode] + weight < srcToNodeDist[neigh]) // curr path has shorter dist to reach neigh
                    srcToNodeDist[neigh] = srcToNodeDist[topNode] + weight;
            }
        }

        return srcToNodeDist;
    }
};

int main(){

    // Graph should be DAG
    Graph g;

    g.insertEdge(1,3,2);  
    g.insertEdge(1,2,0);
    g.insertEdge(3,6,1);
    g.insertEdge(3,4,3);
    g.insertEdge(2,6,2);
    g.insertEdge(4,5,1);
    g.insertEdge(5,6,0);

    // step-1 : find out the toposort of this DAG
    stack<int> topoStk = g.topoSortFinder();

    // step-2 : now call another fun that would calculate teh shortest distance of all nodes from the 'src' using this topoStk only
    int src;
    cout << "Enter source node : ";
    cin >> src;

    vector<int> srcToNodeDist = g.shortestDistAllNodes(topoStk, src);

    // step-3 : printing all shortest dist from src to all nodes
    for(int i=1; i < srcToNodeDist.size(); i++){ // nodes start from 1
        int dist = srcToNodeDist[i];

        if(dist == INT_MAX)
            cout << "shortest dist from src to " << i << " is ♾️" << endl;
        else 
            cout << "shortest dist from src to " << i << " is " << dist << endl;
    } 
    
}

```

---------------------
## Dijkstra's Algo 

![graph (3)](https://github.com/yashasviyadav1/Graphs/assets/124666305/9d340e9e-2381-4563-996f-f3f7ef2472ef)

### (Version-1) Finding shortest distance from src to all nodes 

`Source : 1`
`desti : 6`

```
Input : 
Enter src : 1

Output : 

min dist from 1 to 1 is : 0
min dist from 1 to 2 is : 1
min dist from 1 to 3 is : 2
min dist from 1 to 4 is : 3
min dist from 1 to 5 is : 2
min dist from 1 to 6 is : 2
```

```cpp
#include<iostream>
#include<unordered_map>
#include<set>
#include<list>
#include<algorithm>
using namespace std;

// Dijkstras Algo (for shortest path distance from src to all nodes)

/* imp notes :- 

    1) here we are not using a visitd array and still not getting inside a infinite loop but why so?
       if we observe carefully, we are not inserting all neigh nodes of a 'topNode' we are only inserting whenever a shorter distance to reach that neigh is found, and 
       there can only be single shortest path to reach a 'neigh' and thats why we will not need any visited array 

    2) to find the shortest distance we can simply apply Dijkstra's Algo, but in case we want to know the shortest path from src to a node as well then in that case, 
       every time we find a shorter distance path we will need to make links backward, and ones our dijkstra is done, we can just trace our route backward to the src.
       -> for this we need a parent array. 

*/
class Graph{

    public:
        unordered_map<int,list<pair<int,int>>> AdjList;

        void insertEdge(int vertex1, int vertex2, int weight){
            AdjList[vertex1].push_back({vertex2, weight}); // weighted undirected graph
            AdjList[vertex2].push_back({vertex1, weight});
        }

        // ---------------

        vector<int> dijkstraAlgo(int src){

            int V = AdjList.size();
            vector<int> minDist(V + 1, INT_MAX);
            // we need a minHeap or ordered_set for this 
            set<pair<int,int>> set;
            set.insert({src,0});
            minDist[src] = 0;

            while(!set.empty()){

                auto frontPair = set.begin();
                set.erase(set.begin());
                int frontNode = frontPair -> first;
                int frontWeight = frontPair -> second;

                // explore all neigh of the front
                for(pair<int,int> neighPair:AdjList[frontNode]){
                    int neigh = neighPair.first;
                    int weight = neighPair.second;

                    // if dis via 'frontNode' to 'neigh' is lesser then neigh's prv distance, then update both the 'minDist' and 'set'
                    if(minDist[frontNode] + weight < minDist[neigh]){

                        // for 'set', if a prv entry of the 'neigh' is present then del it
                        auto prvEntry = set.find({neigh,minDist[neigh]});
                        if(prvEntry != set.end()) // entry is present already
                            set.erase(prvEntry);
                        
                        minDist[neigh] = minDist[frontNode] + weight; //updating distance with a new shorter one
                        set.insert({neigh,minDist[neigh]});// insert new entry with new minDist for 'neigh'   
                    }
                }
            }
            return minDist;

        }
};

int main(){
    Graph g;
    g.insertEdge(1, 2, 1);
    g.insertEdge(1, 3, 3);
    g.insertEdge(2, 3, 1);
    g.insertEdge(1, 6, 2);
    g.insertEdge(3, 4, 2);
    g.insertEdge(6, 4, 2);
    g.insertEdge(6, 5, 0);
    g.insertEdge(5, 4, 1);

    // dijkstras algo
    int src;
    cout << "Enter src : ";
    cin >> src;
    vector<int> minDist = g.dijkstraAlgo(src);

    for(int i=1; i < minDist.size(); i++){
        cout << "min dist from " << src << " to " << i << " is : " << minDist[i] << endl;
    }
}


```

### (Version-2) finding shortest path to all nodes via dijkstra 
![graph (3)](https://github.com/yashasviyadav1/Graphs/assets/124666305/9c78d7b9-c31c-4788-b31c-eb9280ce261e)

`source : 1, desti : 6` 
```
input :
Enter src : 1
Enter destination : 6

Output : 

min dist from 1 to 1 is : 0
min dist from 1 to 2 is : 1
min dist from 1 to 3 is : 2
min dist from 1 to 4 is : 2
min dist from 1 to 5 is : 2
min dist from 1 to 6 is : 3

shortest path from 1 to 6 is : 1 -> 3 -> 5 -> 6
```

Code : 
```cpp
#include<iostream>
#include<unordered_map>
#include<set>
#include<list>
#include<algorithm>
using namespace std;

// Dijkstras Algo (for Shortest Path printing from src to desti)

class Graph{

    public:
        unordered_map<int,list<pair<int,int>>> AdjList;

        void insertEdge(int vertex1, int vertex2, int weight){
            AdjList[vertex1].push_back({vertex2, weight}); // weighted undirected graph
            AdjList[vertex2].push_back({vertex1, weight});
        }

        // ---------------

        void dijkstraAlgo(vector<int> &minDist, vector<int> &nodeToParent, int src){ // this fun will fill the 'minDist' and 'nodeToParent' vector

            int V = AdjList.size();

            // we need a minHeap or ordered_set for this 
            set<pair<int,int>> set;
            set.insert({src,0});
            minDist[src] = 0;
            nodeToParent[src] = -1; // src is the starting node

            while(!set.empty()){

                auto frontPair = set.begin();
                set.erase(set.begin());
                int frontNode = frontPair -> first;
                int frontWeight = frontPair -> second;

                // explore all neigh of the front
                for(pair<int,int> neighPair:AdjList[frontNode]){
                    int neigh = neighPair.first;
                    int weight = neighPair.second;

                    // if dis via 'frontNode' to 'neigh' is lesser then neigh's prv distance, then update both the 'minDist' and 'set'
                    if(minDist[frontNode] + weight < minDist[neigh]){

                        // for 'set', if a prv entry of the 'neigh' is present then del it
                        auto prvEntry = set.find({neigh,minDist[neigh]});
                        if(prvEntry != set.end()) // entry is present already
                            set.erase(prvEntry);
                        
                        nodeToParent[neigh] = frontNode;// now every time we get a shorter path parent for neigh, update it in vector
                        minDist[neigh] = minDist[frontNode] + weight; //updating distance with a new shorter one
                        set.insert({neigh,minDist[neigh]});// insert new entry with new minDist for 'neigh'   
                    }
                }
            }
        }
};

int main(){

    cout << "-----------------------------------------------" << endl;

    Graph g;
    g.insertEdge(1, 2, 1);
    g.insertEdge(2, 4, 1);
    g.insertEdge(1, 4, 3);
    g.insertEdge(1, 3, 2);
    g.insertEdge(3, 6, 2);
    g.insertEdge(3, 5, 0);
    g.insertEdge(5, 6, 1);
    g.insertEdge(4, 6, 2);
    

    // dijkstras algo
    int src;
    cout << "Enter src : ";
    cin >> src;

    int V = g.AdjList.size();
    vector<int> minDist(V+1, INT_MAX); // nodes are from 1 to V
    vector<int> nodeToParent(V+1); // node to parent will be used to trace our shortest way back from every node to the src
    g.dijkstraAlgo(minDist, nodeToParent, src);

    for(int i=1; i < minDist.size(); i++){
        cout << "min dist from " << src << " to " << i << " is : " << minDist[i] << endl;
    }

    // lets trace our way back from src to desti 
    int desti;
    cout << "Enter destination : ";
    cin >> desti;

    int parent = nodeToParent[desti];
    int node = desti;
    vector<int> shrtstPathSrcToDesti;
    while(parent != -1) //loop till we reach src
    {
        shrtstPathSrcToDesti.push_back(node);
        //update
        node = parent;   
        parent = nodeToParent[node];
    }
    shrtstPathSrcToDesti.push_back(node); // including source

    // printing shortest path 
    reverse(shrtstPathSrcToDesti.begin(), shrtstPathSrcToDesti.end()); // reverse the path 

    cout << "shortest path from " << src << " to " << desti << " is : "; 
    for(int i=0; i < shrtstPathSrcToDesti.size(); i++){
        if(i != shrtstPathSrcToDesti.size()-1) //not last index
            cout << shrtstPathSrcToDesti[i] << " -> ";
        else cout << shrtstPathSrcToDesti[i] << endl; // last index
    }

    cout << "-----------------------------------------------" << endl;

}


```






