# Cycle Detection
- 1. Cycle Detection in Undirected Graphs 
     - (Method:1) Using 'parent' logic
- 2. Cycle Detection in Directed Graphs 
     - (Method:1) Using 'visited' + 'visitedDFS' logic 
- 3. Cycle Detection Using BFS Toposort (Kahn's Algo)  

-----
## Cycle Detection (Undirected Graphs) 
Example 1 

![graph](https://github.com/YashasviCodes/Graphs/assets/124666305/33280eef-f491-4c39-9f7a-59a23a987a50)

`Output : No Cycle`

Example 2 

![graph (1)](https://github.com/YashasviCodes/Graphs/assets/124666305/fe4f66aa-4889-46ad-8658-3715c5051d8d)

`Output : Cycle Present`

Code : (cycle detection in undirected graphs)

```cpp
#include<iostream>
#include<unordered_map>
#include<list>
using namespace std;

//note :- we can use the 'parent' D.S but do not need that.
// Detect Cycle in Undirected Graph 

class Graph{

    public:
        unordered_map<int,list<int>> AdjList;
    
    void insertEdge(int node1, int node2){
        AdjList[node1].push_back(node2); // undirected graph
        AdjList[node2].push_back(node1);  
    }

    // check for cycle in this directed graph
    bool DFS(unordered_map<int,bool> &visited,int parentOfSrc, int src){
        
        visited[src] = true; // visit node and set the parent of it

        for(int neigh:AdjList[src]){

            // if neigh is visited then.
            //1. for no cycle - we must have reached neigh via src  (i.e src's parent should be neigh)
            //2. if above cond is false that means we reached the neigh not via src but some other node
            if(visited[neigh] && parentOfSrc != neigh)
                return true; // CYCLE 
            else if(!visited[neigh]){
                bool hasCycle = DFS(visited, src, neigh);
                if(hasCycle)
                    return true; // CYCLE
            }
        }

        // reached here means no cycle was found, everything went fine
        return false;
    }

    bool containsCycle(){
        unordered_map<int,bool> visited;

        int V = AdjList.size();
        for(int node = 1; node <= V; node++){
            if(!visited[node]){
                int parent = -1; // for starting node of each component the parent is -1 
                bool hasCycle = DFS(visited, parent, node);
                if(hasCycle)
                    return true; // cycle found
            }
        }

        // reached here means no component had any cycle 
        return false;       
    }
};

int main(){

    Graph g;
/*
    //Graph has cycle.
    g.insertEdge(1,2);
    g.insertEdge(2,3);
    g.insertEdge(3,1);
*/

    // graph doesnt have any cycle 
    g.insertEdge(1,2);
    g.insertEdge(2,3);
    g.insertEdge(3,4);
    g.insertEdge(4,6);
    g.insertEdge(6,5);


/*
    // Graph has cycle.
    g.insertEdge(1,2);
    g.insertEdge(2,5);
    g.insertEdge(2,3);
    g.insertEdge(3,4);
    g.insertEdge(4,5);
    g.insertEdge(5,6);
*/
/*
    // Graph does not have any cycle.
   g.insertEdge(1,2);
   g.insertEdge(2,3);
   g.insertEdge(3,4);
   g.insertEdge(4,5);
*/

   bool hasCycle = g.containsCycle();

    if(hasCycle)
        cout << "Graph has cycle." << endl;
    else cout << "Graph does not have any cycle" << endl;
    
}
```

```cpp
VIP Note:-

what if we used this condition instead of the original :- 
if( visited[neigh] && nodeToParent[neigh] != src)
    return "CYCLE FOUND"
    
 then in that case we would get stuck, on examples like this :- 
 1---2---3

```

-------------
## Cycle Detection (Directed Graphs)

Example 1:- 

![graph (2)](https://github.com/YashasviCodes/Graphs/assets/124666305/16849301-bd51-4458-9398-2ca162789476)

`Output : cycle found`

Example 2 :- 

![graph (3)](https://github.com/YashasviCodes/Graphs/assets/124666305/a3e78d41-ca5a-4350-bb82-b0e9673b94e9)

`Output : cycle not found`
```
note : in the below approach we used 2 maps 1) visited 2) dfsVisited 
        why so ?
        because visited is maintained so that if we visited a path and no cycle was found, then we never visit it again because that will be just waste of time 
        so using 'visited' makes the approach efficient 
        and for 'dfsVisited' it is used to maintain the curr path that we are on .
```
```cpp
// lets create a directed graph 
#include<iostream>
#include<list>
#include<queue>
#include<map>
#include<unordered_map>
using namespace std;

class Graph{
    public:
        map<int,list<int>> AdjList;

    void insertEdge(int vertex1, int vertex2){ // this will create v1 --> v2
        AdjList[vertex1].push_back(vertex2);
        AdjList[vertex2];
    }

    // check for cycle in graph 
    bool DFS(unordered_map<int,bool> &visited, unordered_map<int,bool> &dfsVisited, int src){
        
        visited[src] = true;
        dfsVisited[src] = true;

        for(int neigh:AdjList[src]){

            // when a node is visited, as well as DFSvisited, that means that we reached 'neigh' through neigh (CYCLE FOUND)
            if(visited[neigh] && dfsVisited[neigh])  
                return true;

            // else call DFS only if we have not already visited that not 
            else if(!visited[neigh]){
                bool hasCycle = DFS(visited, dfsVisited, neigh);
                if(hasCycle)
                    return true; // CYCLE found 
            }
        }

        // when all the neigh are visited for a node, that means cycle was not found, so unvisit the dfsVisited map, because we need to search for some other path 
        dfsVisited[src] = false;

        return false; // CYCLE not found 
    }
    bool cyclePresent(){

        unordered_map<int,bool> visited;
        unordered_map<int,bool> dfsVisited;
        int V = AdjList.size();

        // DFS for all components 
        for(int node=1; node < V; node++){
            if(!visited[node]){
                bool hasCycle = DFS(visited, dfsVisited,node);
                if(hasCycle) 
                    return true; // cycle present 
            }
        }
        return false; // cycle not found
    }
};

int main(){

    Graph g;

    // // TestCase 1 - graph with cycle present 
    g.insertEdge(3,4);
    g.insertEdge(3,2);
    g.insertEdge(4,2);
    g.insertEdge(4,5);
    g.insertEdge(2,1);
    g.insertEdge(5,6);
    g.insertEdge(6,1);
    g.insertEdge(6,3);

    // //TestCase 2 - graph does'nt has cycle 
    // g.insertEdge(1,3);
    // g.insertEdge(1,4);
    // g.insertEdge(3,4);
    // g.insertEdge(4,5);


    bool hasCycle = g.cyclePresent();

    if(hasCycle)
        cout << "Cycle is present." << endl;
    else cout << "Cycle is not present." << endl;

    return 0;
}

```

--------
## Cycle Detection using BFS Toposort (Kahn's Algo)

Example 1 : (No cycke)
![graph](https://github.com/YashasviCodes/Graphs/assets/124666305/62ad9bd2-188b-4f4d-a496-16345254dec8)

Example 2 : (Cycle)
![graph (1)](https://github.com/YashasviCodes/Graphs/assets/124666305/f5dea503-44ec-4b6a-b421-e698a4955812)

```cpp
// lets create a directed graph 
#include<iostream>
#include<list>
#include<queue>
#include<map>
#include<unordered_map>
using namespace std;

class Graph{
    public:
        map<int,list<int>> AdjList;

    void insertEdge(int vertex1, int vertex2){ // this will create v1 --> v2
        AdjList[vertex1].push_back(vertex2);
        AdjList[vertex2];
    }

    // loop detection using BFS toposort (Kahn's Algo)

    bool loopDetectionTopoSort(){

        unordered_map<int,int> nodeToIndegree;
        queue<int> q;
        vector<int> topoSort;

        for(auto block:AdjList){ // find the indegree of each node
            int node = block.first;
            nodeToIndegree[node]; // creating block so every node has a block in 'nodeToInd' map
            for(int neigh:block.second ){
                nodeToIndegree[neigh]++;
            }
        }

        for(auto block:nodeToIndegree)// pushh the 0 indegree nodes into q 
        {
            int node = block.first;
            if(nodeToIndegree[node] == 0)
                q.push(node);
        }

        // BFS
        while(!q.empty()){
            int front = q.front(); q.pop();
            topoSort.push_back(front);

            for(int neigh:AdjList[front]){// we are virtually removing the 'front' so decrement its neigh's indegree by 1
                nodeToIndegree[neigh]--;
                if(nodeToIndegree[neigh] == 0) // if neigh's indegree becomes 0 then push it into the queue as well
                    q.push(neigh);
            }
        }

        // now just check if the number of nodes in graph == topposort size (if yes, that means cycle not found)
        if(topoSort.size() == AdjList.size()){
            return false; // No cycle
        }
        return true; // cycle
    }

};

int main(){

    Graph g;
    
    // // Test Case - 1 (no cycle)
    // g.insertEdge(2,1);
    // g.insertEdge(2,3);
    // g.insertEdge(4,1);
    // g.insertEdge(3,4);
    // g.insertEdge(2,5);
    // g.insertEdge(5,6);
    // g.insertEdge(7,5);

    // Test case - 2 (cyclic)
    g.insertEdge(1,4);
    g.insertEdge(1,2);
    g.insertEdge(4,3);
    g.insertEdge(2,3);
    g.insertEdge(3,5);
    g.insertEdge(5,2);

    
    bool isCyclicGraph = g.loopDetectionTopoSort();
    
    if(isCyclicGraph)
        cout << "Cycle Found";
    else cout << "no Cycle exists";

}

```
