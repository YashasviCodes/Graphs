# Shortest Path from Src to All other nodes in Graph 
There are 2 Algo to find the shortest path from source to all nodes in graph 
- 1. Simple Src to All nodes shortest path algo.
- 2. Dijkstra's ALgo 
   - Version 1 (to find only shortest path distance)
   - Version 2 (using Parent Vector to find shortest path from src to desti as well)
 -------------------------------------------
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
