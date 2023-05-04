# Graphs :

### 1. Undirected Unweighted Graphs 
```cpp
#include<iostream>
#include<unordered_map>
#include<map>
#include<list>
#include<queue>
using namespace std;

class Graph{
    public:
        map<int,list<int>> AdjList;
    
    // graph edge inserter 
    void insertEdge(int vertex1, int vertex2){
        AdjList[vertex1].push_back(vertex2);  // undirected, unweighted graph
        AdjList[vertex2].push_back(vertex1);
    }

    void insertEdge(int vertex1){ // when there are different components of a graph 
        AdjList[vertex1];
    }

    //print graph
    void printAdj(){

        for(auto block:AdjList){
            int node1 = block.first;
            cout << node1 << " -> ";
            for(auto neigh:AdjList[node1])
                cout << neigh << " ";
            cout << endl;
        }
    }

    // BFS

    void BFSHelper(unordered_map<int,bool> &visited, int src){
        queue<int> q;
        q.push(src);
        visited[src] = true;

        while(!q.empty()){

            int front = q.front();
            q.pop();
            cout << front << " ";

            for(auto neigh :AdjList[front]){
                if(!visited[neigh]){
                    q.push(neigh);
                    visited[neigh] = true;
                }
            }
        }
    }

    void BFS(){
        unordered_map<int,bool> visited;
        for(auto block:AdjList){
            int node1 = block.first;
            if(!visited[node1]){
                BFSHelper(visited, node1);
            }
        }
    }

    // DFS
    void DFSHelper(unordered_map<int,bool> &visited, int src){
        visited[src] = true;
        cout << src << " ";
        for(auto neigh :AdjList[src])
            if(!visited[neigh])
                DFSHelper(visited, neigh);
        
    }

    void DFS(){
        unordered_map<int,bool> visited;
        for(auto block:AdjList){
            int node1 = block.first;
            if(!visited[node1])
                DFSHelper(visited, node1);
        }
    }
};

int main(){

    //graph creation
    Graph g1;

    g1.insertEdge(1,2);
    g1.insertEdge(1,3);
    g1.insertEdge(1,4);
    g1.insertEdge(2,4);
    g1.insertEdge(3,4);
    /*
        1----3     Our graph looks like this.
        |\   |
        | \  |
        |  \ |   
        2----4
    */

    // print graph
    g1.printAdj();

    // BFS
    cout << "BFS Traversal : ";
    g1.BFS();
    cout << endl;

    // DFS
    cout << "DFS Traversal : ";
    g1.DFS();
    cout << endl;

}
/*

Output 

1 -> 2 3 4 
2 -> 1 4 
3 -> 1 4 
4 -> 1 2 3 
BFS Traversal : 1 2 3 4 
DFS Traversal : 1 2 4 3 

*/




```
