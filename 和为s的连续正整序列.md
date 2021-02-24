# 面试题57 - II. 和为s的连续正数序列
输入一个正整数 target ，输出所有和为 target 的连续正整数序列（至少含有两个数）。

序列内的数字由小到大排列，不同序列按照首个数字从小到大排列。

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/he-wei-sde-lian-xu-zheng-shu-xu-lie-lcof
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

注意这个toArray方法，对于对于二维数组用起来，有时候会感觉不太方便，就用一个list，里面放一个一维数组，最后将list转为数组。
```
class Solution {
    public int[][] findContinuousSequence(int target) {
        ArrayList<int[]> res=new ArrayList<>();
        int left=1;
        int right=1;
        int sum=1;
        while(left<=target/2)
        {
            if(sum==target)
            {
                int[] temp=new int[right-left+1];
                for(int i=0;i<temp.length;i++)
                    temp[i]=left+i;
                res.add(temp);
                sum-=left;
                left++;
            }
            else if(sum<target)
            {
                right++;
                sum+=right;
            }
            else 
            {
                sum-=left;
                left++;
            }
        }
        return res.toArray(new int[res.size()][]);
    }
}
```