#leetcode3   			无重复字符的最长子串
#题目
给定一个字符串，请你找出其中不含有重复字符的 最长子串 的长度。

示例 1:

输入: "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。

示例 2:

输入: "bbbbb"
输出: 1
解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。

示例 3:

输入: "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/longest-substring-without-repeating-characters
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

#注意
1. 对于题目中的字串来说，必须要连续，不能是间隔的。

***
#思路
1. 对于这道题来说，我一开始会想到用dp，然后又想到要用双指针，指针两头指向的就是不含重复元素的子串，但是问题在于如果右边的指针再移动时遇见了一个重复的元素的话， 那么我们需要判断出来这个重复元素再这个已经存在的字串中的位置，然后我们再调整左边的指针，让其指向这个重复元素的下一位，毕竟只有这样，这个子串的长度才有可能扩张。
2. 原因在于，我们一开始右边指针在移动时遇见不一样的好说，直接扩张这个字串就好，但是如果遇见了重复的，那我们可以明确，现在这个重复元素在子串中的位置的左边肯定是不能在出现在子串中了，因为从子串中的重复元素到我们遇到的这个重复元素，这一段的字符串肯定是我们现在持有的子串的子串。
可以这么看abcb，从c到b来说遇见了重复的，那么现在是abc，如果像要扩大，只能往右，所以这个b我们必须要，那么子串中的b左边的只能舍弃，要扩大只能往右，往右的话，那么重复的就不能包括，所以重复的左边都不能要。所以我们只要在沿途中记录好max就好，不管右边会不会出现更大的，希望是在右边，所以只能记录比较当前的长度，让希望向右走。
3. 对于子串中的元素，保存起来的话，相信大家都会选择用hashmap或者hashset，不能重复，这个关键点，让人联想到hash。
4. 还有一点就是我自己想了比较久才明白的，比如说bacab来说，从bac到a，我们确实让左指针指向了c，但是我们在hashmap中并不将b删除，我们只需要让左指针指向c就可以，因为在我们整个的遍历中，我们对于遇到的重复的元素对于我们的影响只是对于左指针的位置的影响，那么我们只需要让其指向最靠右的那个位置就好，比如说上面的bacab，遇到第二个a以后就成了bcab，然后遇到第二个b我们并不需要做什么，因为第一个b的下一个位置并不影响左指针的位置。
当然你也可以不这么干，毕竟这么干有些让人感觉别扭，那么你也可以多几步操作，就是在遇到重复的元素以后，对于hashmap中的元素来说，将这个子串中重复元素左边的都给它删掉，让这个hashmap中只保存我们现在持有的子串中的元素，就这相当于让左指针一步一步向指定位置移动，而不是直接跳到那个位置上，这样看来的话复杂度应该就会变成2n，问题不大，毕竟无论怎么移动左右指针，就是两个都从头到尾走一遍的话，也就走了两边数组，还是可以的。
***
#代码
1. 这是我觉得思路比较容易理解的一种，就是始终让hashmap中存放子串的内容，多的就给它干掉。
```
class Solution {
    public int lengthOfLongestSubstring(String s) {
        if(s==null||s.length()==0)
            return 0;
        int left=0;
        int right=0;
        int max=0;
        HashMap<Character,Integer> map=new HashMap<>();
        while(right<s.length())
        {
            if(!map.containsKey(s.charAt(right)))
            {
                map.put(s.charAt(right),right);
            } 
            else
            {
                int index=map.get(s.charAt(right));
                while(left<=index)
                {
                    map.remove(s.charAt(left));
                    left++;
                }
                map.put(s.charAt(right),right);
                
            }
            max=Math.max(max,right-left+1);
            right++;
        }
        return max;
    }
}
```
2. 这是不干掉的代码，仔细看，还是可以看出很明显的区别，当然这种时间上会更快的
```
class Solution {
    public int lengthOfLongestSubstring(String s) {
        if (s.length()==0) return 0;
        HashMap<Character, Integer> map = new HashMap<Character, Integer>();
        int max = 0;
        int left = 0;
        for(int i = 0; i < s.length(); i ++){
            if(map.containsKey(s.charAt(i))){
                left = Math.max(left,map.get(s.charAt(i)) + 1);
            }
            map.put(s.charAt(i),i);
            max = Math.max(max,i-left+1);
        }
        return max;
        
    }
}
```