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