  给定节点数为 n 的二叉树的前序遍历和中序遍历结果，请重建出该二叉树并返回它的头结点。例如输入前序遍历序列{1,2,4,7,3,5,6,8}和中序遍历序列{4,7,2,1,5,3,8,6}，则重建出如下图所示。
![image](https://user-images.githubusercontent.com/47242566/155442467-6fbd8b9d-4ceb-41eb-ac29-c54c669ec594.png)
  提示： 
  1.vin.length =pre.length 
  2.pre和vin均无重复元素 
  3.vin出现的元素均出现在pre里 
  4.只需要返回根结点，系统会自动输出整颗树做答案对比 
  数据范围：n≤2000，节点的值-10000≤val≤10000 
  要求：空间复杂度O(n),时间复杂度O(n) 
  （1）个人思考
  测试案例：无节点，单节点。
  做不出来……
  （2）书中解答
  ①二叉树节点定义：
```C++
struct BinaryTreeNode{
  int m_nValue;
  BinaryTreeNode* m_pLeft;
  BinaryTreeNode* m_pRight;
};
```
  ②使用递归方法求解
  二叉树前序遍历中，第一个数字总是树的根节点的值。而中序遍历中，根节点的值在序列中间，左子树的节点的值位于根节点的值的左边，而右子树的节点的值位于根节点的值的右边。需要扫描中序遍历，才能找到根节点的值。
  如上图所示，前序遍历序列的第一个数字1就是根节点的值。扫描中序遍历序列，就能确定根节点的值的位置。根据中序遍历的特点，在根节点的值1前面的3个数字都是左子树节点的值，位于1后面的数字都是右子树节点的值。由于在中序遍历序列中，有3个数字是左子树节点的值，因此左子树共有3个左子节点。同样，在前序遍历序列中，根节点后面的3个数字就是3个左子树节点的值，再后面的所有数字都是右子树节点的值。这样我们就在前序遍历和中序遍历两个序列中分别找到了左、右子树对应的子序列。
  ![image](https://user-images.githubusercontent.com/47242566/155443028-a52fbe80-f6d4-4c1c-afb8-f076b436e2c6.png)
  既然已经找到左、右子树的前序遍历序列和中序遍历序列，就可以用同样的方法分别构建左、右子树。则接下来的事情可以递归完成。
```C++
BinaryTreeNode* Construct(int* preorder, int* inorder, int length){
  if(preorder == nullptr || inorder == nullptr || length<=0)
    return nullptr;
  
  return ConstructCore(preorder, preorder+length-1, inorder,inorder+length-1);
}

BinaryTreeNode* ConstructCore(int* startPreorder, int* endPreorder,int* startInorder, int* endInorder)
{
  // 前序遍历序列的第一个数字是根节点的值
  int rootValue = startPreorder[0];
  BinaryTreeNode* root = new BinaryTreeNode();
  root->m_nValue=rootValue;
  root->m_pLeft=root->m_pRight=nullptr;

  if(startPreorder == endPreorder){
    if(startInorder == endInorder && *startPreorder == *startInorder)
      return root;
    else
      throw std::exception("Invalid input.");
  }//递归结束条件
  
  //在中序遍历序列中找到根节点的值
  int* rootInorder = startInorder;
  while(rootInorder <= endInorder && *rootInorder!=rootValue)
    ++rootInorder;
    
  if(rootInorder == endInorder && *rootInorder != rootValue)
    throw std::exception("Invalid input.");
  
  int leftLength = rootInorder - startInorder;
  int* leftPreorderEnd=startPreorder+leftLength;
  
  if(leftLength>0){
    //构建左子树
    root->m_pLeft = ConstructCore(startPreorder+1,leftPreorderEnd,startInorder,rootInorder-1);
  }
  if(leftLength<endPreorder-startPreorder){
    //构建右子树
    root->m_pRight = ConstructCore(leftPreorderEnd+1,endPreorder,rootInorder+1,endInorder);
  }
  return root;
}
```
  书中给出测试用例
  ①普通二叉树（完全二叉树；不完全二叉树）
  ②特殊二叉树（所有节点都没有右子节点的二叉树；所有节点都没有左子节点的二叉树；只有一个节点的二叉树）
  ③特殊输入测试（二叉树的根节点指针为nullptr；输入的前序遍历序列和中序遍历序列不匹配）
根据书中提示自己写的牛客网的题目的答案
```C++
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    TreeNode* reConstructBinaryTree(vector<int> pre,vector<int> vin) {
       if(pre.empty())
           return NULL;
        return ConstructBinaryTree(pre.begin(), pre.end()-1, vin.begin(), vin.end()-1);
    }
    
    TreeNode* ConstructBinaryTree(vector<int>::iterator preb,vector<int>::iterator pree,vector<int>::iterator vinb,vector<int>::iterator vine){
        int rootvalue = *preb;
        TreeNode* root=new TreeNode(rootvalue);
        
        if(preb==pree)
            return root;
        
        vector<int>::iterator vinroot=find(vinb,vine+1,rootvalue);
        int leftlength=vinroot-vinb,rightlength=vine-vinroot;
        
        if(leftlength>0){
            root->left=ConstructBinaryTree(preb+1, preb+leftlength, vinb, vinroot-1);
        }
        if(rightlength>0){
            root->right=ConstructBinaryTree(pree-rightlength+1, pree, vinroot+1, vine);
        }
        return root;
    }
};
```

精华题解：

（1）python 版本递归
```python
class Solution:
  def reConstructBinaryTree(self, pre, tin):
    if not pre:
      return None
    root = TreeNode(pre[0])
    tmp = tin.index(pre[0])
    root.left = self.reConstructBinaryTree(pre[1:tmp+1],tin[:tmp])
    root.right = self.reConstructBinaryTree(pre[tmp+1:],tin[tmp+1:])
    return root
```
