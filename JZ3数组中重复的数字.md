题目一: 找出数组中重复的数字。
    在一个长度为 $n$ 的数组里的所有数字都在 0 ~ n-1 的范围内。数组中某些数字是重复的, 但不知道有几个数字重复了, 也不知道每个数字重复了几次。请找出数组中任意一个重复的数字。
    例如, 如果输入长度为 7 的数组 $\{2,3,1,0,2,5,3\}$, 那么对应的输出是重复的数字 2 或者 3 。

个人版本代码
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
    
个人优化版本:交换思想——如果不一致就交换，如果交换的位置又和该数相同，则该数重复
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
