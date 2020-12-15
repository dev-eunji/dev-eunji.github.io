---
title:  "[Algorithm] 트리 순회(Tree Traversal)"
categories: [Algorithm]
tags: [algorithm, 자료구조, 트리, Tree, 트리순회, tree_traversal]
last_modified_at: 2020-11-23
permalink: /algorithm/:year/:month/:day/:title/
comments: true
---
> 트리 순회의 종류는 3가지, 전위/중위/후위 (pre/in/post order) 순회가 있다.

쉽게 **루트(V)를 언제 탐색할지** 기준으로 외우면 된다.

* `전위순회(preorder)`: 루트 제일 먼저 > 왼쪽 트리 >  오른쪽 트리

* `중위순회(inorder)`: 왼쪽 트리 > 다음으로 루트 > 오른쪽 트리

* `후위순회(postorder)`: 왼쪽 트리 > 오른쪽 트리 > 마지막으로 루트


![SourceTree_Preferences](/assets/images/algorithm/tree_traversal.jpg)