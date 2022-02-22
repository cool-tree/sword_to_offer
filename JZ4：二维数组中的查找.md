一、题目

​	在一个二维数组array中（每个一维数组的长度相同），每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。
[
[1,2,8,9],
[2,4,9,12],
[4,7,10,13],
[6,8,11,15]
]
给定 target = 7，返回 true。
给定 target = 3，返回 false。

​	数据范围：矩阵的长宽满足 0 \le n,m \le 500 ， 矩阵中的值满足 0 \le val \le 10^9

​	进阶：空间复杂度 O(1) ，时间复杂度 O(n+m)

​	输入：
7,[[1,2,8,9],[2,4,9,12],[4,7,10,13],[6,8,11,15]]

​	返回值：
true

​	说明：
存在7，返回true   

二、个人思考解决方案

```C++
class Solution {
public:
    bool Find(int target, vector<vector<int> > array) {
        if(array[0].empty())
            return false;
        bool flag=false;
        int n=array.size();
        int m=array[0].size();
        int max_diag=0;
        for(int i=0;(i<n)&&(i<m);i++){
            if(array[i][i]==target)
                flag=true;
            else if(array[i][i]>target){
                max_diag=i;
                break;
            }
        }
        
        if(max_diag!=0){
            for(int j=0;j<max_diag;j++){
                if(array[max_diag][j]==target)
                    flag=true;
                
                if(array[j][max_diag]==target)
                    flag=true;
            }
        }
        else{
            if(n>m){
                for(int j=m-1;j<n;j++){
                    if(array[j][m-1]==target)
                        flag=true;
                }
            }else if(n<m){
                for(int j=n-1;j<m;j++){
                    if(array[n-1][j]==target)
                        flag=true;
                }
            }
        }
        
        return flag;
    }
};
```

问题：没有考虑输入数组为空的特殊情况

```
思考:输入为二维数组，当为空数组时，array[0]必然存在，不存在的时array[0][0]
```

问题：注意下标不要超界，最后一行应该是array[n-1]而不是array[n]

问题：目标值可能出现在max_diag所在列以上元素所在行的后几列中。

思考：如何设计自测案例？

​	方阵：3,[[1,2,8,9],[2,4,9,12],[4,7,10,13],[6,8,11,15]]

​	n>m: 3,[[1,2],[4,5],[6,7]]

​	m>n:3,[[1,2,3]]

​	包含目标值和不包含目标值

总结：未曾充分利用有序条件，效果不佳



书中给出解答：思考：上述方案中，将导致两个待分析区域，如果只产生一个待分析区域则结果会好很多，而产生两个待分析区域的原因是从矩阵中间一个元素开始分析，如果从数组的一个角上选取数字会不会简单一些？答案是肯定的，如下图中寻找数字7，每次都只产生一个待分析域，且待分析域越来越小。即思路为：**从数组的左下角或右上角开始分析（对应规则是右增和下增）——化简的思路在于变小变少变好解**

分析复杂问题时，可以从一个具体问题入手，通过分析简单具体的例子，寻找普遍规律。如下例中寻找数字7。



![image-20220218133616108](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220218133616108.png)

根据书中提示编写代码：

```c++
class Solution {
public:
    bool Find(int target, vector<vector<int> > array) {
        if(array[0].empty())
            return false;
        bool flag=false;
        int n= array.size();
        int m= array[0].size();
        int i=m-1;
        
        while(i>=0){
            if(array[0][i]==target){
                flag=true;
                break;
            }
            else if(array[0][i]>target)
                i--;
            else if(array[0][i]<target)
                break;
        }
        
        int j=0;
        while((j<n)&&(i>=0)){
            if(array[j][i]==target){
                flag=true;
                break;
            }
            else if(array[j][i]<target)
                j++;
            else if(array[j][i]>target){
                i--;
            }
        }
        
        return flag;
    }
};
```

其余解法：

①暴力遍历法

②N行折半法

③双折半查找

```c++
class Solution {
public:
    bool Find(int target, vector<vector<int> > array) {
        if (array.size()==0 || array[0].size()==0)
            return false;
        else {
            int top = 0, bottom = array.size()-1;
            int left = 0, right = array[0].size()-1;
            int sLeft = 0, sRight = 0;
            int sTop = 0, sBottom = 0;
            int mid = 0;

            for (;left<right || top<bottom;) {
                //对上边界进行折半，可以缩小右边界
                sLeft = left, sRight = right;
                for (;sLeft<=sRight;) {
                    mid = (sLeft + sRight) / 2;
                    if (array[top][mid]==target) return true;
                    else if (array[top][mid]<target) sLeft = mid+1;
                    else sRight = mid-1;
                }
                if (mid<right) right = mid; //利用终止点缩小右边界
                top++;

                //对下边界进行折半，可以缩小左边界
                sLeft = left, sRight = right;
                for (;sLeft<=sRight;) {
                    mid = (sLeft + sRight) / 2;
                    if (array[bottom][mid]==target) return true;
                    else if (array[bottom][mid]<target) sLeft = mid+1;
                    else sRight = mid-1;
                }
                if (left<mid) left = mid; //利用终止点缩小左边界
                bottom--;

                //对左边界进行折半，可以缩小下边界
                sTop = top, sBottom = bottom;
                for (;sTop<=sBottom;) {
                    mid = (sTop + sBottom) / 2;
                    if (array[mid][left]==target) return true;
                    else if (array[mid][left]<target) sTop = mid+1;
                    else sBottom = mid-1;
                }
                if (mid<bottom) bottom = mid; //利用终止点缩小下边界
                left++;

                //对右边界进行折半，可以缩小上边界
                sTop = top, sBottom = bottom;
                for (;sTop<=sBottom;) {
                    mid = (sTop + sBottom) / 2;
                    if (array[mid][right]==target) return true;
                    else if (array[mid][right]<target) sTop = mid+1;
                    else sBottom = mid-1;
                }
                if (top<mid) top = mid; //利用终止点缩小上边界
                right--;
            }
            if (array[top][left]==target) return true;
            else return false;
        }
    }
};
```

④十字分割法？

```c++
class Solution {
public:
    bool Find(int target, vector<vector<int> > array) {
        if (array.size()==0 || array[0].size()==0)
            return false;
        else {
            return find_by_ten_shape_divide(array, target, 0, 0, array[0].size()-1, array.size()-1);
        }
    }

    bool find_by_ten_shape_divide(vector<vector<int> > &datas, int target, int left, int top, int right, int bottom) {
        // 递归终止的条件：当区域被分割为只有一个元素
        if (left==right && top==bottom) {
            if (datas[top][left] == target)
                return true;
            else return false;
        }
        // 利用头尾数据进行优化：不在区域内的直接false，可以减少递归节点数量
        else if (datas[top][left]>target || datas[bottom][right]<target)
            return false;
        else {
            // 水平、竖直方向各自等分
            int cHoriz = (left + right) / 2;
            int cVertic = (top + bottom) / 2;

            if (datas[cVertic][cHoriz] == target)
                return true;
            else {
                // 目标大于分割点则排除左上区域，对右下进行递归
                if (datas[cVertic][cHoriz] < target) {
                    if (cHoriz<right && cVertic<bottom &&
                        find_by_ten_shape_divide(datas, target, cHoriz+1, cVertic+1, right, bottom))
                        return true;
                }
                // 否则排除右下区域，对左上进行递归
                else if ((left<cHoriz || top<cVertic) &&
                         find_by_ten_shape_divide(datas, target, left, top, cHoriz, cVertic))
                    return true;
                // 对右上区域进行递归
                if (cHoriz < right &&
                    find_by_ten_shape_divide(datas, target, cHoriz+1, top, right, cVertic))
                    return true;
                // 对左下区域进行递归
                if (cVertic < bottom &&
                    find_by_ten_shape_divide(datas, target, left, cVertic+1, cHoriz, bottom))
                    return true;
                return false;
            }
        }
    }
};
```

