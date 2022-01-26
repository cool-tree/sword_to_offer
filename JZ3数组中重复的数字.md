题目一: 找出数组中重复的数字。
    在一个长度为 $n$ 的数组里的所有数字都在 0 ~ n-1 的范围内。数组中某些数字是重复的, 但不知道有几个数字重复了, 也不知道每个数字重复了几次。请找出数组中任意一个重复的数字。
    例如, 如果输入长度为 7 的数组 [2,3,1,0,2,5,3], 那么对应的输出是重复的数字 2 或者 3 。

个人初始版本：开辟一个n维数组，初始化为0，数字每出现一次，对应下标所在的数组元素就加1
```python
    int duplicate(vector<int>& numbers) {
        // write code here
        int n=numbers.size();
        vector<int> counts(n,0);
        for(const auto &num:numbers){
            if(num<0)
                return -1;
            counts[num]=counts[num]+1;
            if(counts[num]>1){
                return num;
            }
        }
        return -1;
    }
```
    
个人优化版本:交换思想——遍历数组，如果当前位置所存元素和其下标不等，则将其交换到对应下标处，如果发现对应下标处值即为该值，则该值重复
```python
    int duplicate(vector<int>& numbers) {
        // write code here
       for(int i=0;i<numbers.size();i++){
           if(numbers[i]<0)
               return -1;
           int temp=0;
           while(numbers[i]!=i){
               temp=numbers[numbers[i]];
               if(temp==numbers[temp])
                   return temp;
               else{
                   numbers[numbers[i]]=numbers[i];
                   numbers[i]=temp;
               }

           }
       }
       return -1;
    }
```
书中介绍做法一：把输入数组排序。从头到尾扫描排序后的数组。排序长度为n的数组需要O(nlogn)的时间
书中介绍做法二：哈希表，扫描数组，判断哈希表中是否包含该数字，如果没有，就加入，如果有，则重复，时间复杂度为O(n)，空间复杂度为O(n)
    ①个人初始版本
    ②利用set集合（精华题解）
```python
int solution(){
	vector<int> numbers={1,3,2,0,2,5,3};
	set<int> count;
	for(const auto &num:numbers){
		if(count.count(num)>0){
			return num;
		}
		count.insert(num);
	}
	return -1;
}
```
    ③map统计（精华题解）
```python
int solution(){
	vector<int> numbers={1,3,2,0,2,5,3};
	map<int,int> count;
	for(const auto &num:numbers){
		if(count[num]>0){
			return num;
		}
		count[num]++;
	}
	return -1;
}

```
书中介绍做法三：扫描到下标为i的数字时，先比较这个数字（用m表示）是否等于i，如果是，扫描下一个数字；如果不是，和第m个数字进行比较，如果相等，则找到重复数字，如果不等，则第i个数字和第m个数字交换。重复交换过程直到找到重复数字或没有重复数字——个人优化版本
其它思路：
    ①暴力两遍循环（压根没考虑这种做法。。）
