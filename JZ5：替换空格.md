**JZ5** **替换空格**

描述
请实现一个函数，将一个字符串s中的每个空格替换成“%20”。（将URL中的特殊字符转换成%+对应的ASCII码）
例如，当字符串为We Are Happy.则经过替换之后的字符串为We%20Are%20Happy。

数据范围:$0 \le len(s) \le 1000$ 。保证字符串中的字符为大写英文字母、小写英文字母和空格中的一种。
进阶：时间复杂度 O(n)  ,空间复杂度 O(n) 



测试案例：空串，有空格，无空格，仅有空格



初始解题思路：创建一个临时字符串，遍历原字符串，若原字符串中当前位置非空格，直接追加到临时字符串中，否则在临时字符串中追加"%20"

```c++
    string replaceSpace(string s) {
        // write code here
        if(s.empty())
            return s;
        string solution="";
        
        for(auto &alpha:s){
            if(alpha!=' ')
                solution=solution+alpha;
            else{
                solution=solution+"%20";
            }
        }
        return solution;
    }
```

书中解答

考虑：清楚面试官要求，是在原字符串的基础上修改（扩展，修改）还是创建新的字符串

①在原字符串中修改

​	（1）扫描字符串，遇到空格则替换，需要将空格后面所有字符后移2字节。假设字符串长度为n。对每个空格字符，需要移动后面O(n)个字符，因此对于含有O(n)个空格字符的字符串而言总的时间效率是O(n^2)

​	（2）双指针法

​	先遍历一次字符串，统计出字符串中空格总数，从而计算出替换之后字符串的总长度（每替换一个空格，长度增加2）

​	从字符串 后面开始复制和替换。先准备两个指针P1和P2.P1指向原字符串末尾，P2指向替换之后字符串的末尾。P1向前移动，逐个将它指向的字符串复制到P2指向的位置 ，碰到空格时，P1向前移动一个，P2之前插入"%20"，同时将P2前移3格

​	所有字符都只复制一次，算法 时间效率为O(n)

![image](https://user-images.githubusercontent.com/47242566/154785231-9d5d8c06-402f-425f-b392-3a44edda704e.png)




```c++
    string replaceSpace(string s) {
        // write code here
        if(s.empty())
            return s;
        int n=s.size();
        int m=0;
        
        for(auto &alpha:s){
            if(alpha==' ')
                m++;
        }
        
        if(m==0)
            return s;
        
        for(int i=0;i<m;i++){
            s=s+"  ";
        }
        int p1=n-1;
        int p2=s.size()-1;
        
        while(p1>=0){
            if(s[p1]!=' '){
                s[p2]=s[p1];
                p1--;
                p2--;
            }
            else{
                p1--;
                s[p2--]='0';
                s[p2--]='2';
                s[p2--]='%';
            }
        }
        return s;
    }
```
精华题解：辅助栈python版本
```python
class Solution:
    def replaceSpace(self , s ):
        # write code here
        stack = []
        for c in s:
            # 将空格替换 '%20' 并入栈
            if c == ' ':stack.append('%20')
            # 非空正常入栈
            else: stack.append(c)
        # 以字符串形式返回
        return ''.join(stack)
```
精华题解：python replace
```
class Solution:
    def replaceSpace(self, s):
        return s.replace(' ', '%20')
```
