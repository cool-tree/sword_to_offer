题目一: 找出数组中重复的数字。
    在一个长度为 $n$ 的数组里的所有数字都在 0 ~ n-1 的范围内。数组中某些数字是重复的, 但不知道有几个数字重复了, 也不知道每个数字重复了几次。请找出数组中任意一个重复的数字。
    例如, 如果输入长度为 7 的数组 [2,3,1,0,2,5,3], 那么对应的输出是重复的数字 2 或者 3 。

个人初始版本：开辟一个n维数组，初始化为0，数字每出现一次，对应下标所在的数组元素就加1
```c++
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
```c++
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
```c++
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
```c++
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

题目二: 不修改数组找出重复的数字。
	在一个长度为 n+1 的数组里的所有数字都在 1 ~ n 的范围内, 所以数组中至少有一个数字是重复的。请找出数组中任意一个重复的数字, 但不能修改输入的数组。
例如, 如果输入长度为 8 的数组{2,3,5,4,3,2,6,7}, 那 么对应的输出是重复的数字 2 或者 3 。
	思考：可以用哈希方法，但不可以用排序和交换法（会改变原数组），如此空间复杂度为O(n)。如果考虑时间效率，可以用时间换空间，注意题目中已经说明肯定有数字是重复的，则可以利用1-m的数字出现m+1次及以上则必有重复元素这个特点，结合二分的思想解决该问题：将1-n的数字二分，讨论1-n/2和n/2+1-n的两组数字在原数组中出现的次数，如果次数大于n/2则说明该组数字中存在重复数字，然后再对存在重复数字的该组数组进行同样的二分，直到找到重复元素为止。
首次尝试：
```c++
int count(int begin,int end,vector<int> arr){
	int coun=0;
	for(const auto &num:arr){
		if((num>=begin) && (num<=end))
			coun++;
	}
	return coun;
}

int main(){
        vector<int> numbers={2,3,5,4,3,2,6,7};
        int n=numbers.size()-1;
        int begin=1,end=n;
        int middle=(begin+end)/2;

        while(begin<end){
                if(count(begin,middle,numbers)>middle-begin+1){
                        begin=begin;
                        end=middle;
                        middle=(end+begin)/2;
                }
                else if(count(middle+1,end,numbers)>end-middle+1){
                        begin=middle+1;
                        end=end;
                        middle=(end+begin)/2;
                }
        }
        cout<<middle<<endl;
        return 0;
}
```
错误：当count(begin,middle,numbers)=middle-begin+1且count(middle+1,end,numbers)=end-middle+1时，两边都是可能有重复元素的，如[2,2,2,5,5,5]，然而此时该算法找不出来.修改，把else if 直接改成else即可，当这种改法不能找出所有重复元素——重复元素2会被跳过，转而找到重复元素3（以{2,3,5,4,3,2,6,7}为例）
书中实例代码
```C++
int getDuplication(const int* numbers, int length)
{
	if(numbers=nullptr||length<=0)//注意异常输入
		return -1;
	int start =1;
	int end = length - 1;
	while(end>=start){
		int middle = ((end-start)>>1)+start;
		int count=countRange(numbers,length,start,middle);
		if(end==start){
			if(count>1)
				return start;
			else
				break;
		}
		
		if(count>(middle-start+1))
			end=middle;
		else
			start=middle+1;
	}
	return -1;
}

int countRange(const int* nunmbers,int length,int start, int end){
	if(numbers=nullptr)
		return 0;
	int count=0;
	for(int i=0;i<length;i++)
		if(numbers[i]>=start && numbers[i]<=end)
			++count;
	return count;
}
```
	
