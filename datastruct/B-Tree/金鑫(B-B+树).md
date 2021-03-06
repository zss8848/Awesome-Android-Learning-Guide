##B树（多叉查找树）
###为什么要用B树:
当大规模数据存储到磁盘中的时候，显然定位是一个非常花费时间的过程，但是我们可以通过B树进行优化，提高磁盘读取时定位的效率。
因为我们可以根据B类树的特点，构造一个多阶的B类树，然后在尽量多的在结点上存储相关的信息，保证层数尽量的少，以便后面我们可以更快的找到信息，磁盘的IO操作也少一些，而且B类树是平衡树，每个结点到叶子结点的高度都是相同，这也保证了每个查询是稳定的。

###定义（一个m阶树）:
1. 每个节点至多有m棵子树。
2. 根节点至少有两颗子树。
3. 除了节点以外，其余的每个分支至少拥有m/2棵子树。
4. 所有叶子节点都在同一层。
5. 有k棵子树的分支结点则存在k-1个关键码，关键码按照递增次序进行排列。
6. 关键字数量需要满足ceil(m/2)-1 <= n <= m-1。

###操作

####插入：
1. 如果该结点的关键字个数没有到达m-1个，那么直接插入即可。
2. 如果该结点的关键字个数已经到达了m-1个，那么根据B树的性质显然无法满足，需要将其进行分裂。分裂的规则是该结点分成两半，将中间的关键字进行提升，加入到父亲结点中，但是这又可能存在父亲结点也满员的情况，则不得不向上进行回溯，甚至是要对根结点进行分裂，那么整棵树都加了一层。

####删除：
1. 如果该结点拥有关键字数量仍然满足B树性质，则不做任何处理。
2. 如果该结点在删除关键字以后不满足B树的性质（关键字没有到达ceil(m/2)-1的数量），则需要向兄弟结点借关键字，这有分为兄弟结点的关键字数量是否足够的情况。
	如果兄弟结点的关键字足够借给该结点，则过程为将父亲结点的关键字下移，兄弟结点的关键字上移；
	如果兄弟结点的关键字在借出去以后也无法满足情况，即之前兄弟结点的关键字的数量为ceil(m/2)-1，借的一方的关键字数量为ceil(m/2)-2的情况，那么我们可以将该结点合并到兄弟结点中，合并之后的子结点数量少了一个，则需要将父亲结点的关键字下放，如果父亲结点不满足性质，则向上回溯。

##B+树
###为什么要用B+树:
由于B+树的数据都存储在叶子结点中，分支结点均为索引，方便扫库，只需要扫一遍叶子结点即可，但是B树因为其分支结点同样存储着数据，我们要找到具体的数据，需要进行一次中序遍历按序来扫，所以B+树更加适合在区间查询的情况，所以通常B+树用于数据库索引，而B树则常用于文件索引。

###定义（一个m阶树）:
1. 根结点只有一个，分支数量范围为[2，m]。
2. 分支结点，每个结点包含分支数范围为[ceil(m/2), m]。
3. 分支结点的关键字数量等于其子分支的数量减一，关键字的数量范围为[ceil(m/2)-1, m-1]，关键字顺序递增。
4. 所有叶子结点都在同一层。

###操作：
和B树类似，需要注意的是在增加值的时候，如果存在满员的情况，将选择结点中的值作为新的索引，还有在删除值的时候，索引中的关键字并不会删除，也不会存在父亲结点的关键字下沉的情况，因为那只是索引。


###B树和B+树的区别:

1. 关键字的数量不同。
B+树中分支结点有m个关键字，其叶子结点也有m个，其关键字只是起到了一个索引的作用，但是B树虽然也有m个子结点，但是其只拥有m-1个关键字。
2. 存储的位置不同。
B+树中的数据都存储在叶子结点上，也就是其所有叶子结点的数据组合起来就是完整的数据，但是B树的数据存储在每一个结点中，并不仅仅存储在叶子结点上。
3. 分支结点的构造不同。
B+树的分支结点仅仅存储着关键字信息和儿子的指针（这里的指针指的是磁盘块的偏移量），也就是说内部结点仅仅包含着索引信息。
4. 查询不同。
B树在找到具体的数值以后，则结束，而B+树则需要通过索引找到叶子结点中的数据才结束，也就是说B+树的搜索过程中走了一条从根结点到叶子结点的路径。

###小结:

####B-树：多路搜索树，每个节点存储m/2到m个关键字，非叶子节点存储指向关键字范围的子节点；所有关键字在整棵树中出现且只出现一次，非叶子节点可以命中。
####B+树：在B-树的基础上，为叶子节点增加链表指针，所有关键字都在叶子节点中出现，非叶子节点作为叶子节点的索引。
####B*树：在B+树的基础上，为非叶子节点也增加了链表指针，将节点的最低利用率从1/2提高到了2/3。

