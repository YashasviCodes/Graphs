# Cycle Detection
- 1. Cycle Detection in Undirected Graphs 
- 2. Cycle Detection in Directed Graphs 

-----
## Cycle Detection in Undirected Graphs 
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

