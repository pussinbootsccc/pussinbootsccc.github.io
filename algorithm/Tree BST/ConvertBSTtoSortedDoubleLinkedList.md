---
layout: page
title: Convert BST to Sorted Doubly Linked List
permalink: /algorithm/treebst/convertbsttosorteddoublylinkedlist
---
Convert a Binary Search Tree to a sorted Circular Doubly-Linked List in place.  
return the new head of the linked list

{% highlight txt %} 
Input: root = [4,2,5,1,3]
Output: [1,2,3,4,5]
{% endhighlight %}

Key Ideas
 - record `head` the down leftmost node, the first node met in inorder traversal
 - maintain `prev` pointer, finally come the last node
 - do inorder travseral

{% highlight java %}
class Solution {
    Node head; // the smallest node in bst
    Node prev;
    public Node treeToDoublyList(Node root) {
        if (root == null)
            return null;
        helper(root);
        head.left = prev; // connect head and last
        prev.right = head; // connect head and last
        return head;
    }
    
    // inorder traverse a BST will create a sorted sequence
    private void helper(Node node) {
        if (node == null) return;
        
        helper(node.left); // process left
        
        if (prev == null) { // process curr
            head = node;
        } else {
            node.left = prev; // double linked
            prev.right = node; // double linked
        }
        prev = node; // catch-up
        
        helper(node.right); // process right
    }
}
{% endhighlight %}
