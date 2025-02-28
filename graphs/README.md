## Given an m x n binary matrix mat, return the distance of the nearest 0 for each cell. The distance between two cells sharing a common edge is 1.
Input: mat = [[0,0,0],[0,1,0],[0,0,0]]\
Output: [[0,0,0],[0,1,0],[0,0,0]]\
Input: mat = [[0,0,0],[0,1,0],[1,1,1]]\
Output: [[0,0,0],[0,1,0],[1,2,1]]\

```
class Solution
{
    public:
        struct node
        {
            int i, j, dist;
        };

    bool indexSafe(int i, int j, int r, int c)
    {
        return i >= 0 && i < r && j >= 0 && j < c;
    }

    vector<vector < int>> updateMatrix(vector<vector < int>> &mat)
    {
        int r = mat.size();
        int c = mat[0].size();

        queue<node> q;
        vector<vector < bool>> visited(r, vector<bool> (c, false));
        vector<vector < int>> distance(r, vector<int> (c, 0));

        for (int i = 0; i < r; i++)
        {
            for (int j = 0; j < c; j++)
            {
                if (mat[i][j] == 0)
                {
                    q.push({ i,
                        j,
                        0 });
                    visited[i][j] = true;
                }
            }
        }

        while (!q.empty())
        {
            int levelNodesCount = q.size();

            for (int i = 0; i < levelNodesCount; i++)
            {
                node curr = q.front();
                q.pop();

                distance[curr.i][curr.j] = curr.dist;

                vector<int> delX = { -1,
                    0,
                    0,
                    1
                };
                vector<int> delY = { 0,
                    -1,
                    1,
                    0
                };

                for (int k = 0; k < 4; k++)
                {
                    int _i = curr.i + delX[k];
                    int _j = curr.j + delY[k];
                    if (indexSafe(_i, _j, r, c) && !visited[_i][_j])
                    {
                        q.push({ _i,
                            _j,
                            curr.dist + 1 });
                        visited[_i][_j] = true;
                    }
                }
            }
        }

        return distance;
    }
};
```

## You are given an m x n matrix board containing letters 'X' and 'O', capture regions that are surrounded:
Connect: A cell is connected to adjacent cells horizontally or vertically.
Region: To form a region connect every 'O' cell.
Surround: The region is surrounded with 'X' cells if you can connect the region with 'X' cells and none of the region cells are on the edge of the board.
To capture a surrounded region, replace all 'O's with 'X's in-place within the original board. You do not need to return anything.

Example 1:

Input: board = [["X","X","X","X"],["X","O","O","X"],["X","X","O","X"],["X","O","X","X"]]

Output: [["X","X","X","X"],["X","X","X","X"],["X","X","X","X"],["X","O","X","X"]]

Example 2:

Input: board = [["X"]]

Output: [["X"]]

```
class Solution {
public:
    bool isIndexSafe(int i, int j, int r, int c){
        return i>=0 && i<r && j>=0 && j<c;
    }

    bool isEdge(int i, int j, int r, int c){
        return i==0 || j==c-1 || i==r-1 || j==0;
    }

    bool isSurrounded(vector<vector<char>>& board, int i, int j, vector<pair<int, int>> &nodes,
    vector<vector<bool>> &visited){
        int r = board.size();
        int c = board[0].size();
        queue<pair<int, int>> q;

        q.push({i,j});
        visited[i][j] = true;
        nodes.push_back({i,j});

        bool flag = true;

        while(!q.empty()){
            auto curr = q.front();
            q.pop();

            vector<int> delX = {-1, 0, 0, 1};
            vector<int> delY = {0, -1, 1, 0};

            for(int k=0; k<4; k++){
                int _i = curr.first + delX[k];
                int _j = curr.second + delY[k];

                if(isIndexSafe(_i, _j, r, c) && board[_i][_j] == 'O' && !visited[_i][_j]){
                    if(isEdge(_i, _j, r, c)) flag = false;

                    q.push({_i, _j});
                    visited[_i][_j] = true;
                    nodes.push_back({_i,_j});
                }
            }
        }  

        return flag;
    }

    void solve(vector<vector<char>>& board) {
        int r = board.size();
        int c = board[0].size();

        vector<vector<bool>> visited(r, vector<bool>(c, false));

        for(int i=1; i<r-1; i++){
            for(int j=1; j<c-1; j++){
                if(board[i][j] == 'O' && !visited[i][j]){
                    vector<pair<int, int>> nodes;
                    bool isSurroundedRegion = isSurrounded(board, i, j, nodes, visited);
                    if(isSurroundedRegion){
                        for(auto node: nodes){
                            board[node.first][node.second] = 'X';
                        }
                    }
                }
            }
        }

        return;
    }
};
```


## You are given an m x n binary matrix grid, where 0 represents a sea cell and 1 represents a land cell.

A move consists of walking from one land cell to another adjacent (4-directionally) land cell or walking off the boundary of the grid.

Return the number of land cells in grid for which we cannot walk off the boundary of the grid in any number of moves.

Input: grid = [[0,0,0,0],[1,0,1,0],[0,1,1,0],[0,0,0,0]]
Output: 3
Explanation: There are three 1s that are enclosed by 0s, and one 1 that is not enclosed because its on the boundary.
Input: grid = [[0,1,1,0],[0,0,1,0],[0,0,1,0],[0,0,0,0]]
Output: 0
Explanation: All 1s are either on the boundary or can reach the boundary.

```
class Solution {
public:
    bool isSafe(int i, int j, int r, int c){
        return i>=0 && j>=0 && i<r && j<c;
    }

    int numEnclaves(vector<vector<int>>& grid) {
        int r = grid.size();
        int c = grid[0].size();

        vector<vector<bool>> visited(r, vector<bool>(c, false));
        queue<pair<int, int>> q;

        // top row
        for(int j=0; j<c; j++){
            if(grid[0][j] == 1){
                q.push({0, j});
                visited[0][j] = true;
            }
        }

        // right col
        for(int i=0; i<r; i++){
            if(grid[i][c-1] == 1){
                q.push({i, c-1});
                visited[i][c-1] = true;
            }
        }

        // bottom row
        for(int j=0; j<c; j++){
            if(grid[r-1][j] == 1){
                q.push({r-1, j});
                visited[r-1][j] = true;
            }
        }

        // left col
        for(int i=0; i<r; i++){
            if(grid[i][0] == 1){
                q.push({i, 0});
                visited[i][0] = true;
            }
        }

        while(!q.empty()){
            auto curr = q.front();
            q.pop();

            vector<int> delX = {-1, 0, 0, 1};
            vector<int> delY = {0, -1, 1, 0};

            for(int k=0; k<4; k++){
                int _i = curr.first + delX[k];
                int _j = curr.second + delY[k];

                if(isSafe(_i,_j,r,c) && !visited[_i][_j] && grid[_i][_j] == 1){
                    q.push({_i,_j});
                    visited[_i][_j] = true;
                }
            }
        }

        int ans = 0;

        for(int i=1; i<r-1; i++){
            for(int j=1; j<c-1; j++){
                if(grid[i][j] == 1 && !visited[i][j]){
                    ans++;
                }
            }
        }

        return ans;
    }
};
```

## A transformation sequence from word beginWord to word endWord using a dictionary wordList is a sequence of words beginWord -> s1 -> s2 -> ... -> sk such that:

Every adjacent pair of words differs by a single letter.
Every si for 1 <= i <= k is in wordList. Note that beginWord does not need to be in wordList.
sk == endWord
Given two words, beginWord and endWord, and a dictionary wordList, return the number of words in the shortest transformation sequence from beginWord to endWord, or 0 if no such sequence exists.

 

Example 1:

Input: beginWord = "hit", endWord = "cog", wordList = ["hot","dot","dog","lot","log","cog"]
Output: 5
Explanation: One shortest transformation sequence is "hit" -> "hot" -> "dot" -> "dog" -> cog", which is 5 words long.
Example 2:

Input: beginWord = "hit", endWord = "cog", wordList = ["hot","dot","dog","lot","log"]
Output: 0
Explanation: The endWord "cog" is not in wordList, therefore there is no valid transformation sequence.

```
class Solution {
public:
    bool isDifferenceOne(string &s1, string &s2){
        int difference=0;
        
        for(int i=0; i<s1.length() && i<s2.length(); i++){
            if(s1[i] != s2[i]) difference++;
            if(difference > 1) return false;
        }

        return difference == 1;
    }

    void fillGraph(unordered_map<string, vector<string>> &adjList, vector<string> &nodes){
        for(int i=0; i<nodes.size(); i++){
            for(int j=i+1; j<nodes.size(); j++){
                bool connected = isDifferenceOne(nodes[i], nodes[j]);
                if(connected){
                    adjList[nodes[i]].push_back(nodes[j]);
                    adjList[nodes[j]].push_back(nodes[i]);
                }
            }
        }
    }

    int ladderLength(string beginWord, string endWord, vector<string>& wordList) {
        bool isEndWordPresent = false;
        for(string word: wordList){
            if(word == endWord){
                isEndWordPresent = true;
            }
        }
        if(!isEndWordPresent) return 0;

        unordered_map<string, vector<string>> adjList;

        vector<string> nodes = wordList;
        nodes.push_back(beginWord);

        fillGraph(adjList, nodes);

        queue<string> q;
        unordered_set<string> visited;

        q.push(beginWord);
        visited.insert(beginWord);
        int level = 1;
        
        while(!q.empty()){
            int levelNodeCount = q.size();

            for(int i=0; i<levelNodeCount; i++){
                string curr = q.front();
                q.pop();

                if(curr == endWord) return level;

                for(string y: adjList[curr]){
                    if(visited.find(y) == visited.end()){
                        q.push(y);
                        visited.insert(y);
                    }
                }
            }

            level++;
        }

        return 0;
    }
};
```
