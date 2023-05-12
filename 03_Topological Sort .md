# Topological Sort / Toposort

Topological Sort is always applied on the directed acyclic graph
There are 2 ways to apply toposort :- 
- 1.DFS toposort
   - in case of cyclic graphs the order of the toposort is not corrct, while the number of elements in toposort == number of verticies
- 2.BFS toposort (kahn's Algo)
   - in case of cyclic graphs teh order of BFS toposort is not correct as well as the number of ele != total verticies 
      so this makes us eaisily detect cycle in a graph, and also it is a faster way to detect cycle because (BFS is somewhat faster)

--------
## 1.DFS Toposort 

input :- 

![graph (4)](https://github.com/YashasviCodes/Graphs/assets/124666305/8a3d51da-9422-407e-8bcc-f971e716f9f7)

flow :- 

![graph (4)](https://github.com/YashasviCodes/Graphs/assets/124666305/26c3f379-1553-4043-92d4-cd4caabd52cc)

Output :- 

`Toposort :- 1,4,3,5,3,6`

Code :-
```cpp
#include<iostream>
#include<unordered_map>
#include<list>
#include<stack>
using namespace std;

class Graph{
    public:
        unordered_map<int,list<int>> AdjList;
    void insertEdge(int vertex1, int vertex2){
        AdjList[vertex1].push_back(vertex2); // directed graph 
    }

    // TOPOSORT (DFS)
    void topoSortDFS(unordered_map<int,bool> &visited, stack<int> &stk, int src){

        visited[src] = true;

        for(int neigh:AdjList[src])
            if(!visited[neigh])
                topoSortDFS(visited, stk, neigh);
        
        stk.push(src); // when we reached the end, means no more neigh of 'src' left to visited, then just store it into stack while returning back
    }

    void topoSort(){
        unordered_map<int,bool> visited;
        stack<int> stk;
        
        int V = AdjList.size();
        for(int node=1; node <= V; node++)
            if(!visited[node])
                topoSortDFS(visited, stk, node);

        while(!stk.empty()){ // ones DFS is done, print the stk 
            cout << stk.top() << " ";
            stk.pop();
        }
    }
};

int main(){
    Graph g;
    g.insertEdge(1,2);
    g.insertEdge(1,4);
    g.insertEdge(2,5);
    g.insertEdge(4,5);
    g.insertEdge(2,3);
    g.insertEdge(5,6);
    g.insertEdge(3,6);

    cout << "toposort :- ";
    g.topoSort();
}
```

--------
## 2. BFS Toposort (Kahn's Algo)

input :- 

![graph](https://github.com/YashasviCodes/Graphs/assets/124666305/6f949e72-72f7-41af-956c-f7ed652a2854)

flow :- 
![Ziteboard (3)](https://github.com/YashasviCodes/Graphs/assets/124666305/80920e2d-a59a-405c-80cf-09d132e2ae96)

Output :- 
`Toposort : 1, 2, 4, 5, 6, 7`

code:- 
```cpp
#include<iostream>
#include<queue>
#include<unordered_map>
#include<list>
#include<stack>
using namespace std;

/* Topological Sort is always applied on the directed acyclic graph

    2 ways to apply toposort 
        1. DFS toposort
            - in case of cyclic graphs the order of the toposort is not corrct, while the number of elements in toposort == number of verticies
        2. BFS toposort (kahn's Algo)
            - in case of cyclic graphs teh order of BFS toposort is not correct as well as the number of ele != total verticies 
              so this makes us eaisily detect cycle in a graph, and also it is a faster way to detect cycle because (BFS is somewhat faster)

*/
class Graph{
    public:
        unordered_map<int,list<int>> AdjList;
    void insertEdge(int vertex1, int vertex2){
        AdjList[vertex1].push_back(vertex2); // directed graph 
    }
    
    // topoSort BFS (Kahn's Algo)
    void topoSortBFS(){
        unordered_map<int,int> nodeToIndegree;
        queue<int> q; 

        // find indegree of each node
        for(auto block:AdjList){
            int node = block.first;
            for(int neigh:AdjList[node]){
                nodeToIndegree[neigh]++;
            }
        }

        // push the nodes with indegree 0 into q 
        for(int i=1; i < nodeToIndegree.size(); i++)
            if(nodeToIndegree[i] == 0)
                q.push(i);
            
        while(!q.empty()){

            int front = q.front();
            q.pop();
            cout << front << " ";

            // decrement indegree of all neighbours of front
            for(int neigh:AdjList[front]){
                nodeToIndegree[neigh]--;
                if(nodeToIndegree[neigh] == 0) // and if their indegree becomes 0, push them into q
                    q.push(neigh);
            }
        }
    }

};

int main(){
    Graph g;
    g.insertEdge(1,2);
    g.insertEdge(1,4);
    g.insertEdge(2,5);
    g.insertEdge(4,5);
    g.insertEdge(2,3);
    g.insertEdge(5,6);
    g.insertEdge(3,6);
    
    cout << "BFS toposort :- ";
    g.topoSortBFS();
    cout << endl;

}
```
