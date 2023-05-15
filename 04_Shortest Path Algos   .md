### Simple DFS for finding shortes path

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

Time : $ O(n*m) $ - n is number of rows, m is cols
Space : $ O(n*m) $

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
