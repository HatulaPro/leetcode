# 🟨 [Rotate List](https://leetcode.com/problems/rotate-list/) 

## Description:

> Given the head of a linked list, rotate the list to the right by k places.

### Starting point:

```js
/**
 * Definition for singly-linked list.
 * function ListNode(val, next) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.next = (next===undefined ? null : next)
 * }
 */
/**
 * @param {ListNode} head
 * @param {number} k
 * @return {ListNode}
 */
var rotateRight = function(head, k) {
    
};
```

## The Solution

Our goal is to shift the linked list to the right by `k` places. While there are probably more linear ways to do it, I chose the recursive way - just for fun.

Linked lists work in a simple way: Each node points us to the next, until we get to null. The issue is, we can't access nodes just by their indexes, we have to iterate over the list to get that information.

Note, that given the nodes' indexes and the list's length, this is a simple O(1) operation:

- Given a list of the following form, and `k` of 2:
    ```
    1 -> 2 -> 3 -> 4 -> null
    ```

- We can simply detach the last 2 nodes:
    ```
    1 -> 2 -> null, 3 -> 4 -> null
    ```

- And connect them to the start:
    ```
    3 -> 4 -> 1 -> 2 -> null
    ```

We can't just magically access the first, third and last nodes though, which is why this has to be done with an O(n) time complexity.

The way I implemented it, we go through the list, and once we reach the end we use backtracking to get to the nodes we need. Those nodes are the:
- the first: So we know where the last one connects to 
- the `(length - k)th` node: the splitting spot, in our example, the one with a value of 2
- the last: So we can connect it to the third

In order to track all these, I chose to pass them as a parameter in every recursive call. Using global variables is possible too, but this makes more intuitive sense (to me at least). 

We are keeping track of the first and last nodes (for the reasons I explained), as well as the index of the current node, which will be used to calculate the length too (so we know which node is at the `(length - k)th` position).

```js
var rotateRight = function (head, k, data = { first: head, last: null, index: 0 }) {
    if (!head) return null; // We can stop, we got to the end of the list
    ...
}
```
Next is what happens before we call the recursive function. It will execute in order, meaning it will apply to the first node, the second, the third, and so on:

```js
const index = data.index;
data.index++;
const returnValue = rotateRight(head.next, k, data);

...

return returnValue;
```

First, we save `data.index` into a variable, and then increase its value. This way, we will be able to access the next nodes' indexes (via `data.index`), and the current one will still have access to its index. Then, we call our function with the updated `data` and return the value. Essentially, we want the "splitting" node to return the new head of the list. That will be the value of `returnValue`.

Next, is what happens during the backtracking process. The nodes will be accessed from last to first, so:

```js
// ------------------------------------------
// The first time this part is executed, head is the last node.
// ------------------------------------------
if (!data.last) data.last = head; // Keeping the last node for future use

// We've gone through all nodes, so the index now represents the length of the list.
const length = data.index;

// If we are at the splitting node
// In the case of the following list (k = 2):
// 1 -> 2 -> 3 -> 4 -> null
if (index === length - k - 1) {
    // 1 -> 2 -> 3 -> 4 -> null
    //      ^
    const next = head.next; // Keeping the next value (the node with value 3 in our example)
    head.next = null; // Splitting:
    // 1 -> 2 -> null, 3 -> 4 -> null
    data.last.next = data.first; // Setting the last node's next, to be the first node:
    //  3 -> 4 -> 1 -> 2 -> null
    return next;
}
```

The value we just returned will go "up" the function stack, until reaching the first call, and we will get the new head of the list. 
Now, we just have to handle a couple of edge cases:
- When `k` > `length`: 
    ```js
    k = k % length;
    ```
- When `k` is zero:
    ```js
    if (k === 0) return head;
    ```

Eventually, this is what we end up with:

```js
/**
 * @param {ListNode} head
 * @param {number} k
 * @return {ListNode}
 */
var rotateRight = function (head, k, data = { first: head, last: null, index: 0 }) {
	if (!head) return null;

	const index = data.index;
	data.index++;
	const returnValue = rotateRight(head.next, k, data);
	if (!data.last) data.last = head;

	const length = data.index;
	k = k % length;
    if (k === 0) return head;
	if (index === length - k - 1) {
		const next = head.next;
		head.next = null;
		data.last.next = data.first;
		return next;
	}
	return returnValue;
};
```