# leetcode第二题 两数相加

## 题目
给出两个 非空 的链表用来表示两个非负的整数。其中，它们各自的位数是按照 逆序 的方式存储的，并且它们的每个节点只能存储 一位 数字。

如果，我们将这两个数相加起来，则会返回一个新的链表来表示它们的和。

您可以假设除了数字 0 之外，这两个数都不会以 0 开头。

示例：

输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
输出：7 -> 0 -> 8
原因：342 + 465 = 807

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/add-two-numbers
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。
***

## 思路

1. 对于这道题来说，主要还是每次都遍历链表，取出一个元素，然后设置一个carry表示上一位进几，比如5+6=11，然后要往下一位进1，那么对于下一位来说，carry就等于1。
2. 然后就是简单的相加，sum=链表1取出的元素+链表2取出的元素+carry，carry对于第一次来说肯定是0。
3. 得到每一次计算出来的sum值来说，我们可以得到最后结果的这一位上是什么，还是5+6我们得到11，然后将11对10取模，得到一个1，是这一位上的值，对10取整得到进位为1，也就是下一次的carry=1，这样一直计算到每次都取不到值，也就是两个链表都指向null，结束循环。
****
##注意
1. 对于我们每次取出两个链表中的值来说，由于两个链表不太可能是一样的长度，所以有可能为null，但是只要有一个不为null，那么就可以继续运算，所以一个为null时，只要表示成0就好了。
2. 结束循环计算以后，我们还需要判断一下carry是否为0，因为计算到最高位，可能还出现了进位，但是由于前面已经全部为null了，此时循环已经结束，所以我们要手动判断一下carry是否为0，如果不为0，将这个carry作为最后一位即可。
*****
## 代码

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
    ListNode dummyHead = new ListNode(0);
    ListNode p = l1, q = l2, curr = dummyHead;
    int carry = 0;
    while (p != null || q != null) {
        int x = (p != null) ? p.val : 0;
        int y = (q != null) ? q.val : 0;
        int sum = carry + x + y;
        carry = sum / 10;
        curr.next = new ListNode(sum % 10);
        curr = curr.next;
        if (p != null) p = p.next;
        if (q != null) q = q.next;
    }
    if (carry > 0) {
        curr.next = new ListNode(carry);
    }
    return dummyHead.next;
    }
}

```