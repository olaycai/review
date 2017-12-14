# 树

## 二叉树(B树)
二叉树是指每个节点最多有2个子树的树结构。
```text
    A           A           A
   / \         /             \
  B   C       B               C
```
第一颗树用代码表示则为

```php
class Node {

    /**
     * 节点数据
     *
     * @var String
     */
    public $value = '';

    /**
     * 左孩子
     *
     * @var Node
     */
    public $leftChild = null;

    /**
     * 右孩子
     *
     * @var Node
     */
    public $rightChild = null;

    /**
     * 构造函数
     *
     * @param int $value
     */
    public function __construct($value)
    {
        $this->value = $value;
    }
}

$a = new Node('A');
$b = new Node('B');
$c = new Node('C');

$a->leftChild = $b;
$a->rightChild = $c;
```
### 二叉树的遍历
```text
            A
          /   \
         B     C
        /     /
       D     H
      / \     \
     E   F     I
          \
           G
```
遍历的名称都是以根节点为基准来说的，如果根节点在前面则为先序，在中间则为中序，后面则为后续。

- 先序遍历 (根节点 -> 左孩子 -> 右孩子)
先序遍历结果: A B D E F G C H I
算法实现:
```php
/**
 * 先序遍历 (根 -> 左 -> 右)
 *
 * @param Node $node 树
 * @param array $order 遍历结果
 * @return void
 */
function preOrder($node, &$order = [])
{
    $order[] = $node->value;
    // 如果到达了叶子节点，则向上返回
    if ($node->leftChild == null && $node->rightChild == null) {
        return;
    }
    if ($node->leftChild != null) {
        preOrder($node->leftChild, $order);
    }

    if ($node->rightChild != null) {
        preOrder($node->rightChild, $order);
    }
}
```

- 中序遍历 (左孩子 -> 根节点 -> 右孩子)
中续遍历结果: E D F G B A H I C

算法实现:
```php
/**
 * 中序遍历(左 -> 根 -> 右)
 *
 * @param Node $node 树节点
 * @param array $order 遍历结果
 * @return mixed
 */
function midOrder($node, &$order = [])
{
    // 判断是否到达了叶子节点
    if ($node->leftChild == null && $node->rightChild == null) {
        $order[] = $node->value;
        return;
    }
    // 有左孩子则继续递归左孩子
    if ($node->leftChild != null) {
        midOrder($node->leftChild, $order);
    }
    // 存入节点值
    $order[] = $node->value;
    // 有右孩子则继续递归右孩子
    if ($node->rightChild != null) {
        midOrder($node->rightChild, $order);
    }
}
```

- 后序遍历 (左孩子 -> 右孩子 -> 根节点) 后序遍历结果: E G F D B I H C A

算法实现:
```php
/**
 * 后序遍历 (左 -> 右 -> 根)
 *
 * @param Node $node 树
 * @param array $order 遍历结果
 * @return void
 */
function lastOrder($node, &$order = [])
{
    // 判断是否到达了最后一个节点
    if ($node->leftChild == null && $node->rightChild == null) {
        $order[] = $node->value;
        return;
    }
    // 有左孩子则继续递归左孩子
    if ($node->leftChild != null) {
        lastOrder($node->leftChild, $order);
    }
    // 有右孩子则继续递归右孩子
    if ($node->rightChild != null) {
        lastOrder($node->rightChild, $order);
    }
    // 存入根节点
    $order[] = $node->value;
}
```
### 根据遍历反推树
根据遍历反推树的前提是必需知道中序遍历已经另一个遍历才能确定树结构。用中序遍历确定根节点，另一个遍历确定节点在左孩子还是右孩子

- 中序遍历 + 先序遍历

    已知：前序遍历为 GDAFEMHZ，中序遍历为 ADEFGHMZ，求树
    
```text
第一步: 根据前序遍历确定root节点(因为前序是 根 -> 左 -> 右)

    G
   / \

第二步：根据中序遍历区分左右孩子，因为中序是左 -> 根 -> 右，所以，G左边的元素必定是G的左孩子，右边的元素必定为右孩子

    G
  /   \
ADEF  HMZ

第三步：在左孩子的元素中确定左孩子的root，也是看回前序遍历，ADEF中D在前序遍历中靠前，所以D是左孩子的root节点

        G
      /   \
     D     HMZ
     |
    AEF
第四步: 看回中序遍历D左边的为D的左孩子，右边的为右孩子 即 A为D的左孩子 EF为右孩子
        G
      /   \
     D     HMZ
    / \
   A  EF
   
不断重复判断

(5)                 (6)                 (7)                 (8)
        G               G                    G                      G
      /   \            / \                  / \                   /   \
     D     HMZ        D   HMZ              D   M                 D     M
    / \              / \                  / \  |                / \   / \
   A   F            A   F                A   F HZ              A   F H   Z
       |               /                    /                     /
       E              E                    E                     E
```

   

## 二叉排序树
在二叉树的基础上多加一个条件：左孩子的值比对应节点的值小，右孩子比对应节点的值大。
```text
     5
   /   \
  3     8  
 / \   / \
2   4 6   9

数组中存数可为 [5, 3, 8, 2, 4, 6, 9] 左孩子节点为 2n+1 右孩子节点为2n+2
```

排序二叉树的代码实现
```php
class BinarySortTree {

    /**
     * 左孩子
     *
     * @var BinarySortTree
     */
    public $leftChild = null;

    /**
     * 右孩子
     *
     * @var BinarySortTree
     */
    public $rightChild = null;

    /**
     * 节点值
     *
     * @var int
     */
    public $key = null;


    /**
     * 新增节点
     *
     * @param int $node
     * @return void
     */
    public function insert($node)
    {
        // root节点
        if ($this->key == null) {
            $this->key = $node;
            $this->leftChild = new BinarySortTree();
            $this->rightChild = new BinarySortTree();
            return;
        }

        if ($this->key > $node) {
            $this->leftChild->insert($node);
        } else {
            $this->rightChild->insert($node);
        }
    }
}

$arr = [16, 38, 8, 12, 46, 58, 22, 40];
$tree = new BinarySortTree();
foreach ($arr as $node) {
    $tree->insert($node);
}
```