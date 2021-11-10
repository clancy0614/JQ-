# myLeetCode
my solutions to some LeetCode and other coding problems

## Number of good subsets 
https://leetcode.com/discuss/interview-question/268604/Google-interview-Number-of-subsets
> For a given list of integers and integer K, find the number of non-empty subsets S such that min(S) + max(S) <= K.

**My solution:**
Sort the list: 

a[1], a[2], ..., a[i], a[i+1], ..., a[j],...

For every pair of i and j >= i, check if a[i] + a[j] <= K. If yes, then {a[i], a[j]} is a good subset, and {a[i], a[j]} + any subset formed by numbers between i and j is also good. There are j-i-1 numbers between i and j (for j >= i + 1), so the number of subset including the empty set formed by these numbers is 2^(j-i-1).


