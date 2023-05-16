# Shortest Path in binary Matrix (src to desti)
### Using DFS 

```cpp
INPUT : 
 {{1,1,1,0},   
  {0,1,1,1},
  {1,1,0,1},
  {1,1,1,1}}; 
Enter src coordinates (row,col) : 0 0 
Enter desti coordinates (row,col) : 3 3 
  
OUTPUT : -
Shortest path length : 6
(0,0) --> (0,1) --> (0,2) --> (1,2) --> (1,3) --> (2,3) --> (3,3)
```

Time : $O((n*m)^2)$  ⚠️

Space : $O(n*m)$

code:-
```cpp
#include<unordered_map>
#include<list>
#include<map>
#include<iostream>
#include<vector>
using namespace std;

//fun.3
bool isSafeToMove(vector<vector<int>> &matrix, vector<vector<bool>> &visited, int row, int col){
    int rowSize = matrix.size();
    int colSize = matrix[0].size();
    if( (row < rowSize && row >= 0) && (col >= 0 && col < colSize) && !visited[row][col] && matrix[row][col] == 1)
        return true;
    return false;
}

// fun.2
void shortestPathSrcDesti(vector<vector<int>> &matrix, vector<vector<bool>> &visited, vector<pair<int,int>> &shortestPathVec, vector<pair<int,int>> &currPathVec, int &ShortestPathLen, int currPathLen, int row, int col, int destiRow, int destiCol){

    // base case - if reached desti update and out currPath is shorter then the main 'shortestPath' then update it
    if(row == destiRow && col == destiCol){
        if(currPathLen < ShortestPathLen){
            ShortestPathLen = currPathLen;
            shortestPathVec = currPathVec;
        }
        return;
    }

    // if we are not at desti then, visit curr cell then explore 4 neigh of curr cell
    visited[row][col] = true;

    vector<int> deltaRow = {0, 0, -1, +1};
    vector<int> deltaCol = {-1, +1, 0, 0};
    for(int i=0; i < deltaCol.size(); i++){
        int nextRow = row + deltaRow[i];
        int nextCol = col + deltaCol[i];

        // if it is safe to move than only step ahead 
        if(isSafeToMove(matrix, visited, nextRow, nextCol)){ // also store the path 

            currPathVec.push_back({nextRow,nextCol}); // store nextCell into path
            shortestPathSrcDesti(matrix, visited, shortestPathVec, currPathVec , ShortestPathLen, currPathLen + 1, nextRow, nextCol, destiRow, destiCol);
            currPathVec.pop_back(); // ones we return from a path, we remove its coordi -> (backtrack)
        }
    }

    // ones we have visited all 4 neigh cells for the curr cell, then just mark it false (to find out any other possible path with lower path length)
    visited[row][col] = false;
}

int main(){
    cout<< "--------------------------------------------" << endl;

    /*
        Shortest Path 
        S - - 
            | -
              |
              D    (length = 6)
            
    */
    vector<vector<int>> matrix = {{1,1,1,0},   
                                  {0,1,1,1},
                                  {1,1,0,1},
                                  {1,1,1,1}}; 
    
    // input 
    cout << "Enter src coordinates (row,col) : ";
    int srcRow, srcCol;
    cin >> srcRow >> srcCol; 
    cout << "Enter desti coordinates (row,col) : ";
    int destiRow, destiCol;
    cin >> destiRow >> destiCol;

    // algo
    int rowSize = matrix.size();
    int colSize = matrix[0].size();
    vector<vector<bool>> visited(rowSize, vector<bool>(colSize, false));
    int ShortestPathLen = INT_MAX;
    vector<pair<int,int>> shortestPathVec; // to store the path followed
    vector<pair<int,int>> currentPathVec;
    currentPathVec.push_back({srcRow, srcCol});
    shortestPathSrcDesti(matrix, visited, shortestPathVec, currentPathVec, ShortestPathLen, 0, srcRow, srcCol, destiRow, destiCol);

    // printing shortest path 
    cout << "Shortest path length : " << ShortestPathLen << endl;
    for(int i=0; i < shortestPathVec.size(); i++){
        pair<int,int> cell = shortestPathVec[i];
        
        if(i == shortestPathVec.size()-1)// lastIndex
            cout << "(" << cell.first << ","<< cell.second <<  ")" ;
        
        else cout << "(" << cell.first << ","<< cell.second <<  ") --> " ;
    }
    cout << endl;



    cout<< "--------------------------------------------" << endl;


}
```

----------------------------------

### Using BFS 

```
INPUT 
Enter src coordinates (row,col) : 0 0 
Enter desti coordinates (row,col) : 3 3 

OUTPUT 
shortest Path Length : 7
Shortest path vector :- 
(0,0) --> (0,1) --> (0,2) --> (1,2) --> (1,3) --> (2,3) --> (3,3)

```

![Matrix (BFS)](https://github.com/YashasviCodes/Graphs/assets/124666305/f96d97dc-3b4b-49a1-8976-ea64df9b410a)

![image](https://github.com/YashasviCodes/Graphs/assets/124666305/9c1e5059-196c-412b-af91-b476c0e7cbb0)


Time : $O(E+V)$ ⭐
Space : $O(E+V)$

code :- 

```cpp
#include<iostream>
#include<vector>
#include<queue>
using namespace std;

// BFS
bool isSafeToMove(vector<vector<int>> &grid, vector<vector<bool>> &visited, int row, int col){
    // if cell is in range && cell is not visited yet && is a '1' cell, then only its safe
    int rowSize = grid.size(), colSize = grid[0].size();
    if( (row >= 0 && row < rowSize) && (col >= 0 && col < colSize) && !visited[row][col] && grid[row][col] == 1)
        return true;
    return false;
}

// returns the shortest path vector.
vector<pair<int,int>> shortestPathSrcDestiBFS(vector<vector<int>> &grid, int srcRow, int srcCol, int destiRow, int destiCol){

    if(grid[srcRow][srcCol] == 0) // exception case- when start cell is '0' 
        return {}; // no path 

    int rowSize = grid.size(), colSize = grid[0].size(); 
    // we need to maintain the curr path and the shortest path as well, also the visited cells 
    vector<int> shortestPath, currPath;
    vector<vector<bool>> visited(rowSize, vector<bool>(colSize, false));

    // BFS - queue( { {front cell coordi}, currPathVec of pairs of coordinates } )
    queue< pair< pair<int,int>, vector<pair<int,int>> > > q;
    q.push({{srcRow, srcCol},{{srcRow,srcCol}} }); // currPathVec is empty at start cell  
    visited[srcRow][srcCol] = true;

    vector<int> deltaRow = {0, 0, -1, +1};  // will be using this for exploring the 4 neighbours of the front cell
    vector<int> deltaCol = {-1, +1, 0, 0};

    while(!q.empty()){

        pair< pair<int,int>,vector<pair<int,int>>> front = q.front();
        q.pop();
        int frontRow = front.first.first; // fetched row and cols and currPathLen of the front block of q
        int frontCol = front.first.second;
        vector<pair<int,int>> currPathVec = front.second;

        if(frontRow == destiRow && frontCol == destiCol) // if we reached desti, then return the pair 'currPathVec'
            return currPathVec;

        // exploring 4 neigh's of the frontCell
        for(int i=0; i < deltaRow.size(); i++){

            int nextRow = frontRow + deltaRow[i];
            int nextCol = frontCol + deltaCol[i];
            // if we reached desti, then this is the shortest path , reutrn it (but make sure to add the nectRow, nextCol as well into currPath)
            if(nextRow == destiRow && nextCol == destiCol){
                currPathVec.push_back({nextRow, nextCol});
                return currPathVec;
            } 

            if(isSafeToMove(grid, visited, nextRow, nextCol)){ // if next coordinates are safe, then push them into currPath and also into q 
                currPathVec.push_back({nextRow,nextCol});
                q.push({{nextRow,nextCol}, currPathVec});
                visited[nextRow][nextCol] = true;
                // if the nextCell is the desti , then return the curr path (including this neigh)
                if(nextRow == destiRow && nextCol == destiCol)
                    return currPathVec;
                // ones this nextCell is done, make sure to remove it from curr path, else result path will be wrong 
                currPathVec.pop_back();

            }
        }
    }
    // reached here means no path found that can take us to the desti 
    return {}; // no path found
    
}

int main(){
    // input

    vector<vector<int>> grid =   {{1,1,1,0},     // 0,0 -> 3,3 (shortest path len : 6)☑️
                                  {0,1,1,1},
                                  {1,1,0,1},
                                  {1,1,1,1}}; 

/*
    vector<vector<int>> grid = {{1,1,1,0},     // 0,0 -> 3,3 (no path exists )☑️
                                  {0,0,0,0},
                                  {1,1,0,1},
                                  {1,1,1,1}}; 

*/
/*
    vector<vector<int>> grid =   {{1,1,1,1},     // 0,0 -> 3,3 (shortest path len : 6)☑️
                                  {1,1,1,0},
                                  {1,1,0,0},
                                  {1,1,1,1}}; 
*/
    cout << "Enter src coordinates (row,col) : ";
    int srcRow, srcCol;
    cin >> srcRow >> srcCol; 
    cout << "Enter desti coordinates (row,col) : ";
    int destiRow, destiCol;
    cin >> destiRow >> destiCol;

    // algo call
    int rowSize = grid.size();
    int colSize = grid[0].size();
    vector<pair<int,int>> shortestPathVec = shortestPathSrcDestiBFS(grid,srcRow, srcCol, destiRow, destiCol);

    if(shortestPathVec.size() == 0)
        cout << "no path exists" << endl;
    else{
        cout << "shortest Path Length : " << shortestPathVec.size()-1 << endl;
        cout << "Shortest path vector :- " << endl;
        for(int i=0; i < shortestPathVec.size(); i++){

            pair<int,int> block = shortestPathVec[i];
            if(i == shortestPathVec.size()-1) // last index
                cout << "(" << block.first << "," << block.second << ") ";
            else    
                cout << "(" << block.first << "," << block.second << ") --> ";
        }
    }
}
```
-----------------------------
### Shortest Path in Undirected-Graph (using BFS)

![graph (2)](https://github.com/YashasviCodes/Graphs/assets/124666305/78ffa06a-ef01-4a18-b9b9-9ce11cc7fe72)
```cpp
INPUT 
Enter src and desti nodes : 1 8
Shortest Path length : 2
OUTPUT 
shortest Path : 1-->3-->8
```
```cpp
#include<iostream>
#include<list>
#include<unordered_map>
#include<queue> 
#include<vector>
using namespace std;

class Graph{
    
    public:
        unordered_map<int,list<int>> AdjList;
    void insertEdge(int vertex1, int vertex2){
        AdjList[vertex1].push_back(vertex2);
        AdjList[vertex2].push_back(vertex1); // undirected graph
    }

    vector<int> shortestPathBFS(int srcNode, int destiNode){

        unordered_map<int,bool> visited;
        queue<pair<int,vector<int> >> q;
        q.push({srcNode,{srcNode}}); // initially curr path is empty
        visited[srcNode] = true;

        while(!q.empty()){

            auto front = q.front();
            int frontNode = front.first;
            vector<int> currPathVec = front.second;
            q.pop();

            // if we reached desti, then return the currpath
            if(frontNode == destiNode)
                return currPathVec;

            // explore all neigh of the 'front' and if it's not visited, then insert that neigh into queue 
            for(int neigh:AdjList[frontNode]){
                
                if(!visited[neigh]){

                    currPathVec.push_back(neigh);
                    q.push({neigh,currPathVec}); 

                    visited[neigh] = true;
                    // if neigh is desti return currPath
                    if(neigh == destiNode)
                        return currPathVec;

                        
                    currPathVec.pop_back(); // if we do not do then other's result will be disturbed

                }
            }
        }

        return {};// reached here means no path exists that can take us to 'desti' node
        
    }
};

int main(){

    Graph g;


    // Shortest Path length : 3
    //shortest Path : 1-->1-->3
    g.insertEdge(1,2);
    g.insertEdge(1,3);
    g.insertEdge(1,4);
    g.insertEdge(2,5);
    g.insertEdge(3,8);
    g.insertEdge(4,6);
    g.insertEdge(5,8);
    g.insertEdge(6,7);
    g.insertEdge(7,8);

/* 
    g.insertEdge(1,4);
    g.insertEdge(1,2);
    g.insertEdge(1,3);
    g.insertEdge(2,4);
    g.insertEdge(2,3);
*/

    //input
    int srcNode,destiNode;
    cout << "Enter src and desti nodes : ";
    cin >> srcNode >> destiNode;
    vector<int> shortestPathVec = g.shortestPathBFS(srcNode, destiNode);

    // printing shortest path 
    cout << "Shortest Path length : " << shortestPathVec.size() -1 << endl;
    cout << "shortest Path : ";
    for(int i=0; i < shortestPathVec.size(); i++){
        if(i == shortestPathVec.size()-1)
            cout << shortestPathVec[i] << endl;
        else cout << shortestPathVec[i] << "-->";
    }

    return 0;
}
```
