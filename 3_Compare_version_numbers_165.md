Given two version numbers, version1 and version2, compare them.

Version numbers consist of one or more revisions joined by a dot '.'. Each revision consists of digits and may contain leading zeros. Every revision contains at least one character. Revisions are 0-indexed from left to right, with the leftmost revision being revision 0, the next revision being revision 1, and so on. For example 2.5.33 and 0.1 are valid version numbers.

To compare version numbers, compare their revisions in left-to-right order. Revisions are compared using their integer value ignoring any leading zeros. This means that revisions 1 and 001 are considered equal. If a version number does not specify a revision at an index, then treat the revision as 0. For example, version 1.0 is less than version 1.1 because their revision 0s are the same, but their revision 1s are 0 and 1 respectively, and 0 < 1.

Return the following:

If version1 < version2, return -1.
If version1 > version2, return 1.
Otherwise, return 0.

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/compare-version-numbers
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

#### Ideas of Solving Problems

#### Solution1 in c++ 
String segmentation.We can divide the version number into revision numbers based on a dot '.', and then compare the same subscript revision numbers of the two version numbers from left to right. When comparing revision numbers, you need to convert the string to an integer for comparison. **Note**: According to the title requirements, if the revision number does not exist at a subscript, the revision number is considered to be 0.

```c++
class Solution {
public:
	int compareVersion(string version1, string version2) {
		stringstream ss;
		vector<int> v1, v2;
		string s;
		ss << version1;
		while (getline(ss, s, '.')) {
			v1.push_back(stoi(s));
		}
		ss.clear();
		ss << version2;
		while (getline(ss, s, '.')) {
			v2.push_back(stoi(s));
		}
		while (v1.size() < v2.size())	v1.push_back(0);
		while (v2.size() < v1.size())	v2.push_back(0);
		for (int i = 0; i < v1.size(); ++i) {
			if (v1[i] > v2[i])	return 1;
			else if (v1[i] < v2[i])	return -1;
		}
		return 0;
	}
};
```
time complexity:O(n + m)
spatial complexity:O(n + m)

#### Solution2 in c++
Solution 1 requires storing the divided revision number. In order to optimize spatial complexity, we can parse the revision number for comparison while dividing the version number.

```c++
class Solution {
public:
    int compareVersion(string version1, string version2) {
        int n = version1.length(), m = version2.length();
        int i = 0, j = 0;
        while (i < n || j < m) {
            int x = 0;
            for (; i < n && version1[i] != '.'; ++i) {
                x = x * 10 + version1[i] - '0';
            }
            ++i; // 跳过点号
            int y = 0;
            for (; j < m && version2[j] != '.'; ++j) {
                y = y * 10 + version2[j] - '0';
            }
            ++j; // 跳过点号
            if (x != y) {
                return x > y ? 1 : -1;
            }
        }
        return 0;
    }
};
```

time complexity:O(n + m)
spatial complexity:O(1)