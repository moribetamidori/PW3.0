---
title: Searching Algorithm-DFS and BFS | 检索算法-深度优先搜索与广度优先搜索
date: 2020-05-19 17:10:48
tags:
- DFS
- BFS
- code
- Searching Algorithm
---
<b>INTRO: </b> Notes about DFS and BFS with coding examples. I feel that the major difference between DFS and BFS is that the data structure it uses. DFS uses Stack and BFS uses Queue. However, as you can see after you read through it, such a small difference would create two completely different searching strategies. I also used a different notation for deriving the time and space complexity for DFS and BFS, as I learned from my 440 (Intro to AI) class that it would give you much more details than a simple V and E notation. 

Knowldege required before you dive into the topic: <b><font color=#33BBFF>Tree, Node, Linked List, Stack, Queue. </font></b> 

## The General Searching Algorithm for both BFS and DFS

```python 
def XFS (source, goal, restricted):
    fringe = [] # A list used to hold the nodes to be visited
    visited = [] # Used to hold nodes that you have already visited
    pred = [] # Used to hold the predecessor of each node. If you're solving a matrix of states, you can also formulate it to be a 2D matrix array.
    while (len(fringe) > 0): 
        currentNode  = fringe.pop()
        if currentNode.data == goal 
            return "Sucess", pred
        for child in currentNode:
            if child not in visited and child not in restricted:
                fringe.append(child)
                pred[child] = currentNode
        visited.append(currentNode)
    return "No Solution", NULL
```
Since for some reason the comment function for python markdown is not working after the while loop, I attach the explanation for the while loop as follows:

<b>Line 5:</b> if there are still nodes in the fringe that are not being visited
<b>Line 6:</b> Pop the node (using either stack or queue, for queue use .pop(0) to pop from the first element).
<b>Line 7-8:</b> if that node is the goal, then I return the success message and the predecessor set. 
<b>Line 9-12:</b> Otherwise, I would loop through each child of that node, and append it to the fringe and mark its predecessor in the pred set as currentNode. 
<b>Line 13:</b> After I loop through all of its children, I mark currentNode as visited. 
<b>Line 14:</b> Finally, If I still can't find a solution after expand all possible nodes in the graph, I return No Solution and a Null pred set. 

***Note:*** There are a lot of different ways to manipulate the code depend on the question it asks. For example, the restricted state does not have to be passed down. You could check the restricted state within the for loop for each child. However, there are some components that have to be there:

<b>Frindge:</b> Used to hold nodes to be visited.
<b>Visited:</b> Used to hold nodes that have been fully visited (All of its children are visited).
<b>Predecessor:</b> Used to hold the predecessor (the parent node) of a node. We need this set because we want to be able to print out the path once the search is finished by tracing backwards from the goal to the source and looking at each node's predecessor. 



## Comparing BFS and DFS 

<a href="https://vivadifferences.com/difference-between-dfs-and-bfs-in-artificial-intelligence/" target="_blank"><img src="https://i.loli.net/2020/05/20/opY4i2zbeWFjs81.png" ></a>


| Type  | Removing Strategy   | Completeness  | Optimality  |
|---|---|---|---|
| DFS  | Remove the newest node from fringe (LIFO), and therefore uses Stack.  | Not complete if state space is infinite, is complete if state space is finite.  |  No, it does not guarantee to give you the shortest path.|
| BFS  |  Remove the oldest node from fringe (FIFO), and therefore uses Queue. | Is complete. Because BFS will explore all possible states.   |  Yes, it guarantees to give you the shortest path.  |


### DFS Depth-First Searching Algorithm 
<b><font color=#33BBFF>Features:</font></b> Like its name, it would go down to one branch as deep as possible before exploring other braches in a tree. 

<b><font color=#33BBFF>Data Structure Used: </font></b>Stack. Notice that if you always pop the last element first (and at the same time you always append the children to the end of the list), you would always explore the children of the node and the children of that children before you move on to the neighboring node at the same level. Try to draw a tree and a one dimensional horizontal list that illustrate the process by yourself and you will see the point. 

<b><font color=#33BBFF>Strategy: </font></b>DFS is usually a faster way to find a path (if it exists) than BFS because instead of exploring all the possibilities on a level, it only explores one branch until it finds a viable path. However, it is not suitble for finding the shortest path. 

<b><font color=#FF1493>Time Complexity: </font></b><b>O(b<sup>d</sup>).</b>
- b represents the number of children a parent can hold. For example, a tree can have more than 2 children, and therefore it is useful to use b to measure against the time complexity. 
- d represents the depth of the tree. 
- Derivation: Total nodes = 1 + b + b<sup>2</sup> + b<sup>3</sup> + ... + b<sup>d</sup>. Drop the lower terms you get O(b<sup>d</sup>).
- Usually you would see something like O(|V|), in which |V| represents the total number of nodes since you need to traverse through all nodes. O(b<sup>d</sup>) here represents the same idea but with different variables. 
- It can also be O(|V| + |E|) if you are using adjacency lists, and O(V<sup>2</sup>) for adjacency matrix.

<b><font color=#FF1493>Space Complexity:</font></b> <b>O(b·d<sup>max</sup>). </b>
- d<sup>max</sup>: represents the depth of maximal solution.
- Derivation: Given the nature of DFS, you would go deep down into a branch before exploring the node in the same level. Therefore the nodes in the stack would be something like 1+1+1...+b·d<sup>max</sup>. Droping the 1 terms, it means that in the worst case scenario, you would only have at most b·d<sup>max</sup> nodes in a stack. 
- Notice usually the space complexity would also be O(|V|). With same reasoning as time complexity.


### BFS Breadth-First Searching Algorithm (shortest path)
<b><font color=#33BBFF>Features: </font></b>Explore all the branches level by level and proceed to the shortest path. 

<b><font color=#33BBFF>Data Structure Used: </font></b>Queue. In contrast to BFS, if you always pop the first element first and at the same time you always append the children of that node to the end of the list, you would always first explore all the node at the same level before moving on to the nodes in the next level (a drawing would be helpful here too).

<b><font color=#33BBFF>Strategy: </font></b>You use it to find the best solution because you would explore all possibilities in each level before moving on to the next level, but with greater space complexity as a trade off. 

<b><font color=#FF1493>Time Complexity:</font></b> <b>O(b<sup>d</sup>). </b>Same explantion in DFS.  

<b><font color=#FF1493>Space Complexity:</font></b> <b>O(b<sup>d<sup>min</sup></sup>).</b>
- d<sup>min</sup>: represents the depth of minimal solution.
- Derivation: It is because you have to hold all the nodes from previous layer until you find the best solution at minimal depth of d<sup>min</sup>: 1 + b + b<sup>2</sup> + b<sup>3</sup> + ... + b<sup>d<sup>min</sup></sup>. Notice that it grows exponentially compared to DFS, and is therefore generally more expensive than DFS. 


*Some end notes: After watched the AlphaGo documentary, I was amazed about how AI is able to beat human with a game that has countless possibilities for each move. Even though the actual algorithm behind AlphaGo is way more complicated than DFS and BFS, but you might want to primarily use DFS instead of BFS when calculating the next move in the game of Go.*