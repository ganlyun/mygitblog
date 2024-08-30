# react diff vs vue diff

React的diff算法主要基于三个核心思想：1) 不同类型的元素会产生不同的树；2) 相同类型的元素会生成相似的树结构；3) 通过key可以识别可复用的节点。React通过这些假设将传统的树编辑距离算法复杂度从O(n^3)降低到O(n)。React的diff过程包括tree diff、component diff和element diff，它在同层节点间进行比较，不跨层级比较，并且在列表渲染时使用key来优化diff过程。

[深入理解React：diff 算法 - OneForCheng - 博客园 (cnblogs.com)](https://www.cnblogs.com/forcheng/p/13246874.html)

Vue的diff算法在Vue 3中得到了改进，它采用了**双端比较**和**最长递增子序列**的策略来减少DOM操作。Vue 3的diff算法首先处理前置和后置节点，然后对乱序部分构建最长递增子序列，以此来最小化节点的移动。这种方法在某些情况下比React的算法更高效，因为它可以减少真实DOM的移动次数301

[深度对比 React 与 Vue 的 Diff 算法-CSDN博客](https://blog.csdn.net/zz_jesse/article/details/140965466)



React通过大胆的假设和策略简化了问题，而Vue 3通过更复杂的算法来减少DOM操作。



不要使用index序列作为key，会导致数据与key之间的对应关系出错
