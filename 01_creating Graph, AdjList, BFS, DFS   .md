# Graphs :

## 1. Undirected Unweighted Graphs 
![graph](https://user-images.githubusercontent.com/124666305/236156069-f06f3842-5687-44fa-8227-cb3207ca33d1.png)

lets create this above graph from scratch :- 

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

## 2.Directed Unweighted Graph :- 
![directed-graph](https://user-images.githubusercontent.com/124666305/236154616-0e6ddd0a-e2c8-4341-9d71-990687baedb7.png)

lets create this above graph from scratch.

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

    void printAdj(){
        for(auto block:AdjList){
            int node = block.first;
            cout << node << " -> ";
            for(auto neigh:AdjList[node])
                cout << neigh << " ";
            cout << endl;
        }
    }

    // Breadth First Search
    void BFSHelper(unordered_map<int,bool> &visited, int src){
        queue<int> q;
        q.push(src);
        visited[src] = true;

        while(!q.empty()){
            int front = q.front();
            q.pop();
            cout << front << " ";

            for(auto neigh:AdjList[front]){
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
            int node = block.first;
            if(!visited[node])  
                BFSHelper(visited, node);
        }
    }

    // Depth First search 
    void DFSHelper(unordered_map<int,bool> &visited, int src){
        
        visited[src] = true;
        cout << src << " ";

        for(auto neigh:AdjList[src])
            if(!visited[neigh])
                DFSHelper(visited, neigh);

    }
    void DFS(){

        unordered_map<int,bool> visited;
        for(auto block:AdjList){
            int node = block.first;
            if(!visited[node])
                DFSHelper(visited, node);
        }
    }
};

int main(){

    Graph g;
    g.insertEdge(2,1);
    g.insertEdge(2,3);
    g.insertEdge(4,1);
    g.insertEdge(3,4);
    g.insertEdge(2,5);
    g.insertEdge(5,6);
    g.insertEdge(7,5);

    // print graph
    g.printAdj(); 

    cout << "BFS Traversal : ";
    g.BFS();
    cout << endl;

    cout << "DFS Traversal : ";
    g.DFS();
    cout << endl;

    return 0;
}

/*
AdjList :- 
1 -> 
2 -> 1 3 5 
3 -> 4 
4 -> 1 
5 -> 6 
6 -> 
7 -> 5 
BFS Traversal : 1 2 3 5 4 6 7
DFS Traversal : 1 2 3 4 5 6 7
*/

```

## 3. Undirected Weighted Graphs :- 
![graph (1)](https://user-images.githubusercontent.com/124666305/236163089-73509bc9-7101-4947-98f3-06a3f0d472b8.png)

lets create the above graph from scratch :- 
```cpp
#include<iostream>
#include<unordered_map>
#include<map>
#include<list>
#include<queue>
using namespace std;

class Graph{
    public:
        map<int,list<pair<int,int>>> AdjList;
    
    // graph edge inserter 
    void insertEdge(int vertex1, int vertex2, int weight){
        AdjList[vertex1].push_back({vertex2, weight});  // undirected, unweighted graph
        AdjList[vertex2].push_back({vertex1, weight});
    }

    void insertEdge(int vertex1){ // when there are different components of a graph 
        AdjList[vertex1];
    }

    //print graph
    void printAdj(){

        for(auto block:AdjList){
            int node1 = block.first;
            cout << node1 << " -> ";
            for(auto neighBlock:AdjList[node1]){
                int neigh = neighBlock.first;
                int weight = neighBlock.second;
                cout << "{" << neigh<< "," << weight << "} ";
            }

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

            for(auto neighBlock :AdjList[front]){
                int neigh = neighBlock.first;
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
        for(auto neighBlock :AdjList[src]){
            int neigh = neighBlock.first;
            if(!visited[neigh])
                DFSHelper(visited, neigh);
        }
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

    g1.insertEdge(1,2,3);
    g1.insertEdge(1,3,2);
    g1.insertEdge(3,2,2);
    g1.insertEdge(2,5,1);
    g1.insertEdge(3,5,1);
    g1.insertEdge(4,5,2);

    // print the Adj List 
    cout << "AdjList :- " << endl;
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

AdjList :- 
1 -> {2,3} {3,2}       
2 -> {1,3} {3,2} {5,1} 
3 -> {1,2} {2,2} {5,1} 
4 -> {5,2} 
5 -> {2,1} {3,1} {4,2} 

BFS Traversal : 1 2 3 5 4 
DFS Traversal : 1 2 3 5 4 

note : [in the AdjList above, 1->{2,3} means 1 has neigh 2 with an edge between them of weight 3 i,e something like this 

            3 
        1------>2
]


*/
```
## 4.Directed Weighted Graph 
```
        (0) ----1----> (1) ----4---> (2) -----1----> (3) -----3----> (4)
         |                                            ^
         |                                            |
         |------------2-------------------------------|
```

```cpp
#include<iostream>
#include<list>
#include<map>
#include<unordered_map>
#include<queue>
using namespace std;

class Graph{

    public:
        map<int,list<pair<int,int>>> AdjList;

    // inserting directed edge 
    void insertEdge(int vertex1, int vertex2, int weight){
        AdjList[vertex1].push_back({vertex2,weight});
        AdjList[vertex2];
    }

    void insertEdge(int vertex1){ // when there are multiple components of a graph
        AdjList[vertex1];
    }

    void printAdjList(){
        for(auto block:AdjList){
            int node = block.first;
            cout << node << " -> ";
            for(auto neighPair:AdjList[node]){
                int neigh = neighPair.first;
                int weight = neighPair.second;

                cout << "{" << neigh << "," << weight << "}" << " ";
            }
            cout << endl;
        }
    }

    // Breadth First Search 
    void BFSHelper(unordered_map<int,bool> &visited, int node){
        queue<int> q;
        q.push(node);
        visited[node] = true;

        while(!q.empty()){

            int front = q.front();
            q.pop();
            cout << front << " ";

            for(auto neighPair:AdjList[front]){
                int neigh = neighPair.first;
                int weight = neighPair.second;
                if(!visited[neigh]){
                    q.push(neigh);
                    visited[neigh] = true;
                }
            }
        }
    }

    void BFS(){
        unordered_map<int,bool> visited;
        for(auto i:AdjList){
            int node = i.first;
            if(!visited[node])
                BFSHelper(visited, node);
        }
    }

    // Depth First Search 
    void DFSHelper(unordered_map<int,bool> &visited, int node){

        visited[node] = true;
        cout << node << " ";

        for(auto neighPair:AdjList[node]){
            int neigh = neighPair.first;
            int weight = neighPair.second;
            if(!visited[neigh])
                DFSHelper(visited, neigh);
        }

    }

    void DFS(){
        unordered_map<int,bool> visited;
        for(auto i:AdjList){
            int node = i.first;
            if(!visited[node])
                DFSHelper(visited, node);
        }
    }
};

int main(){

    Graph g;
    
    g.insertEdge(0,1,1);
    g.insertEdge(1,2,4);
    g.insertEdge(2,3,1);
    g.insertEdge(0,3,2);
    g.insertEdge(3,4,3);

    cout << "AdjList :- " << endl;
    g.printAdjList();

    cout << "BFS :- ";
    g.BFS();
    cout << endl;

    cout << "DFS :- ";
    g.DFS();
    cout << endl;


    return 0;
}
/*
Graph :- 

        (0) ----1----> (1) ----4---> (2) -----1----> (3) -----3----> (4)
         |                                            ^
         |                                            |
         |------------2-------------------------------|

*/

/*
AdjList :- 
0 -> {1,1} {3,2} 
1 -> {2,4} 
2 -> {3,1} 
3 -> {4,3} 
4 -> 

*/

```
