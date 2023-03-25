A 3 x 3 magic square is a 3 x 3 grid filled with distinct numbers from 1 to 9 such that each row, column, and both diagonals all have the same sum.

Given a row x col grid of integers, how many 3 x 3 "magic square" subgrids are there?  (Each subgrid is contiguous).

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/magic-squares-in-grid
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

####Ideas of Solving Problems

**1**.the sum of grid is 45, because each grid is different from 1 to 9;
**2**.Each column and row must add up to 15, multiplied by 3 to sum the grid;
**3**.The sum of diagonal lines must also be 15. The title says that the sum of diagonal lines is the same as the sum of columns and rows;
**4**.Add the 12 values of four lines passing through the center (that is, two diagonal lines in a row and a column), and the four lines add up to 60; The entire grid adds up to 45.And the center grid equals (60-45)/3 = 5.

####Solution 1 in c++

```c++
class Solution {
public:
    bool ismagic(array<int, 9> m) {
        int count[16] = {0};
        for (auto n : m)
            if (++count[n] > 1 || n > 9)
                return false;
        return m[0] + m[1] + m[2] == 15
            && m[3] + m[4] + m[5] == 15
            && m[6] + m[7] + m[8] == 15
            && m[0] + m[3] + m[6] == 15
            && m[1] + m[4] + m[7] == 15
            && m[2] + m[5] + m[8] == 15
            && m[0] + m[4] + m[8] == 15
            && m[2] + m[4] + m[6] == 15;
    }
    int numMagicSquaresInside(vector<vector<int>>& grid) {
        if (grid.size() < 3 || grid[0].size() < 3)
            return 0;
        int m = grid.size(), n = grid[0].size();
        int res = 0;
        for (int i = 0; i <= m - 3; i++)
            for (int j = 0; j <= n - 3; j++) {
                if (grid[i + 1][j + 1] != 5)
                    continue;
                array<int, 9> v;
                int w = 0;
                for (int r = i; r < i + 3; r++)
                    for (int c = j; c < j + 3; c++)
                        v[w++] = grid[r][c];
                res += ismagic(v);
            }

        return res;
    }
};
```

####Solution 2 in c++ (Optimal time complexity)

```c++
class Solution {
public:
    vector<int> m={8,1,6,7,2,9,4,3,8,1,6,7,2,9,4,3};
    int numMagicSquaresInside(vector<vector<int>>& grid) {
        int di[8]={-1,-1,-1,0,1,1,1,0};
        int dj[8]={-1,0,1,1,1,0,-1,-1};
        int count=0;
        for(int i=1;i<grid.size()-1;i++)
            for(int j=1;j<grid[0].size()-1;j++)
                if(grid[i][j]==5){
                    vector<int> around;
                    for(int k=0;k<8;k++)
                        around.push_back(grid[i+di[k]][j+dj[k]]);
                    count+=IsMagic(around);
                }
        return count;
    }
    bool IsMagic(vector<int>& v){
        for(int i=0;i<8;i+=2)
            if(m[i]==v[0])
            return v==vector<int>(m.begin()+i,m.begin()+i+8)
            ||v==vector<int>(m.rbegin()+7-i,m.rbegin()+15-i);
        return false;//奇数元素
    }
};

```