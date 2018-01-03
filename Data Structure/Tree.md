# 树

## 二叉树
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
    
    /**
     * 查找节点
     *
     * @param int $key
     * @return BinarySortTree
     */
    public function findNode($key)
    {
        // 找到最后一个元素都没有，则返回null
        if ($this->isLeaf($this) && $this->key != $key) {
            return null;
        }
        // 找到数据则返回
        if ($this->key == $key) {
            return $this;
        }

        // 根据key值寻找查找的路线
        if ($this->key > $key) {
            return $this->leftChild->findNode($key);
        } else {
            return $this->rightChild->findNode($key);
        }

    }

    /**
     * 判断是否为叶子节点
     *
     * @param BinarySortTree $node
     * @return bool
     */
    protected function isLeaf($node)
    {
        return empty($node->leftChild->key) && empty($node->rightChild->key);
    }
    
    /**
     * 查找最小的节点
     *
     * @param BinarySortTree $parent
     * @return BinarySortTree
     */
    public function findMin(&$parent)
    {
        if ($this->isLeaf($this)) {
            return $this;
        }
        $parent = $this;
        return $parent->leftChild->findMin($parent);
    }


    /**
     * 删除节点
     *
     * @param int $node
     * @return void
     */
    public function deleteNode($node)
    {
        $tree = $this->findNode($node);
        if ($this->isLeaf($tree)) {
            // 如果是叶子节点，直接删除
            $tree->key = null;
            $tree->leftChild = null;
            $tree->rightChild = null;
            return;
        }

        // 如果被删节点的子树只有一个则当前被删除节点直接替换为子节点
        if (empty($tree->leftChild->key) || empty($tree->rightChild->key)) {
            $target = empty($tree->leftChild->key) ? $tree->rightChild : $tree->leftChild;
            $tree->key = $target->key;
            $tree->leftChild = $target->leftChild;
            $tree->rightChild = $target->rightChild;
            return;
        }

        // 既有左子树又有右子树
        if (!empty($tree->leftChild->key) && !empty($tree->rightChild->key)) {
            // 第一步先找到右孩子中最小的节点(即右孩子中最左边的节点)
            $minNode = $tree->rightChild->findMin($minNodeParent);
            // 第二步把找到的节点替换为要删除的节点，并删除找到的节点
            $tree->key = $minNode->key;
            $minNodeParent->leftChild = null;
            return;
        }
    }
}

$arr = [16, 38, 8, 12, 46, 58, 22, 40];
$tree = new BinarySortTree();
foreach ($arr as $node) {
    $tree->insert($node);
}
$tree->deleteNode(38);
midOrder($tree, $order);
print_r($order);
```

## 二叉平衡树
二叉平衡树又基于二叉排序树的基础上多了一个平衡因子的判断，如果平衡因子的绝对值小于1且为二叉排序树，则此树则为二叉平衡树。
```text
        15                          15
         \                        /    \
          30                     12     30
           \                    /  \   /  \
            40                 11  14 20  38
           /  \               / 
          32   52            10 
          
       (二叉排序树)              (二叉平衡树)
```
- 平衡因子： 左子树的高度减去右子树的高度
```text
             15            平衡因子: 3 - 2 = 1     
           /    \   
          12     30        12的平衡因子: 2 - 1 = 1; 30的平衡因子: 1 - 1 = 0
         /  \   /  \
        11  14 20  38 
       / 
      10 
           (原始树) 
```
- 最小失衡树： 在新插入的节点往上查找，第一个平衡因子绝对值大于1的树为最小失衡树。
```text
             15            在10后面插入一个新节点9     15
           /    \                                 /     \
          12     30                              12     30
         /  \   /  \                            /  \   /  \
        11  14 20  38                          11  14 20  38   11的平衡因子: 2 - 0 = 2
       /                                      /
      10                                     10
           (原始树)                          /
                                           9
                                           
                                           
      11
     /
    10
   /
  9
    (最小失衡树)
```

### 平衡方法
- LL型

找到节点后，右旋转。3节点的左孩子存在临时节点tmp中，清空3节点的左孩子，tmp作为根节点，tmp的右孩子指向3结点
```text
    3           2
   /           / \
  2           1   3
 /
1
```

-- RR型

找到节点后，左旋转。4节点的右孩子存在临时节点tmp中，清空4节点的右孩子，tmp作为根节点，tmp的左孩子指向4节点
```text
    4               5
     \             / \
      5           4   6
       \
        6
```

-- LR型
先左旋再右旋。
```text
    6           6           5
   /           /           / \
  4           5           4   6
   \         /
    5       4
```

-- RL型
先右旋再左旋
```text
    6           6               9
     \           \             /  \
      10          9           6   10   
      /            \
     9              10
```

```php
class Node {

    /**
     * 数值
     *
     * @var int
     */
    public $data = null;

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
     * 平衡因子
     *
     * @var int
     */
    public $balanceFactor = 0;

    /**
     * 构造函数
     *
     * @param int $value
     */
    public function __construct($value)
    {
        $this->data = $value;
    }

}

class AVLTree {

    /**
     * 左子树高
     *
     * @var int
     */
    const LEFT_HEIGHT = -1;

    /**
     * 右子树高
     *
     * @var int
     */
    const RIGHT_HEIGHT = 1;

    /**
     * 左右等高
     *
     * @var int
     */
    const SAME_HEIGHT = 0;

    /**
     * 根节点
     *
     * @var Node
     */
    public $root = null;

    public function init($arr)
    {
        foreach($arr as $data) {
            $this->insert($this->root, $data);
        }
    }

    /**
     * 插入节点
     *
     * @param Node $node
     * @param int $value
     * @return mixed
     */
    private function insert(&$node, $value, &$test = 0)
    {
        if (empty($node)) {
            $node = new Node($value);
            return;
        }
        if ($node->data > $value) {
            $this->insert($node->leftChild, $value, $test);
            switch($node->balanceFactor) {
                case self::SAME_HEIGHT:
                    // 未增加此节点时等高，则更改为左子树高(因为插入的是左边孩子)
                    $node->balanceFactor = self::LEFT_HEIGHT;
                    break;
                case self::LEFT_HEIGHT:
                    // 未增加此节点时，左子树比较高则进行左边平衡操作
                    $this->leftBalance($node);
                    break;
                case self::RIGHT_HEIGHT:
                    $node->balanceFactor = self::SAME_HEIGHT;
                    break;
            }
        } else {
            $this->insert($node->rightChild, $value, $test);
            switch($node->balanceFactor) {
                case self::SAME_HEIGHT:
                    // 未增加此节点时等高，则更改为右子树高(因为插入的是右边孩子)
                    $node->balanceFactor = self::RIGHT_HEIGHT;
                    break;
                case self::LEFT_HEIGHT:
                    $node->balanceFactor = self::SAME_HEIGHT;
                    break;
                case self::RIGHT_HEIGHT:
                    // 未增加此节点时，左子树比较高则进行右边平衡操作
                    $this->rightBalance($node);
                    break;
            }
        }

    }

    /**
     * 左边平衡衡操作
     *
     * @param Node $node
     * @return void
     */
    public function leftBalance(&$node)
    {
        // 暂存节点
        $leftChild = $node->leftChild;

        // 判断是左左还是左右情况
        switch ($leftChild->balanceFactor) {
            // 左左情况
            case self::LEFT_HEIGHT:
                // 左旋转
                $this->rightRotate($node);
                break;
            // 左右情况
            case self::RIGHT_HEIGHT:
                // 先左旋再右旋
                $this->leftRotate($leftChild);
                $node->leftChild = $leftChild;

                // 调整完后再右旋一次
                $this->rightRotate($node);
                break;
        }
    }

    /**
     * 右旋转
     *
     * @param Node $node
     * @return void
     */
    public function rightRotate(&$node)
    {
        // 暂存节点
        $tmp = $node;
        $leftChild = $tmp->leftChild;

        // 节点左孩子替换原节点
        $node = $leftChild;

        // 原节点的左孩子设置为空
        $tmp->leftChild = null;
        if (($tmp->leftChild == null && $tmp->rightChild == null) || ($tmp->leftChild != null && $tmp->rightChild != null)) {
            $tmp->balanceFactor = self::SAME_HEIGHT;
        } else if ($tmp->leftChild == null) {
            $tmp->balanceFactor = self::RIGHT_HEIGHT;
        } else {
            $tmp->balanceFactor = self::LEFT_HEIGHT;
        }

        // 替换后的节点右孩子连接原节点
        $node->rightChild = $tmp;
        // 设置平衡因子
        $node->balanceFactor = self::SAME_HEIGHT;
    }

    /**
     * 左旋转
     *
     * @param Node $node
     * @return void
     */
    public function leftRotate(&$node)
    {
        // 暂存节点
        $tmp = $node;
        $rightChild = $tmp->rightChild;

        $node = $rightChild;
        // 原节点右孩子替换为原节点右孩子
        $tmp->rightChild = $node->leftChild;
        if (($tmp->leftChild == null && $tmp->rightChild == null) || ($tmp->leftChild != null && $tmp->rightChild != null)) {
            $tmp->balanceFactor = self::SAME_HEIGHT;
        } else if ($tmp->leftChild == null) {
            $tmp->balanceFactor = self::RIGHT_HEIGHT;
        } else {
            $tmp->balanceFactor = self::LEFT_HEIGHT;
        }

        $node->leftChild = $tmp;
        $node->balanceFactor = self::SAME_HEIGHT;
    }

    /**
     * 右边平衡操作
     *
     * @param Node $node
     * @return void
     */
    public function rightBalance(&$node)
    {
        // 暂存节点
        $rightChild = $node->rightChild;
        // 判断是右左情况还是右右情况
        switch ($rightChild->balanceFactor) {
            // 右右情况
            case self::RIGHT_HEIGHT:
                // 左旋转
                $this->leftRotate($node);
                break;
            // 右左情况
            case self::LEFT_HEIGHT:
                // 先右旋再左旋
                $this->rightRotate($rightChild);
                $node->rightChild = $rightChild;

                $this->leftRotate($node);
                break;
        }
    }

    /**
     * 查找节点
     *
     * @param Node $node
     * @param int $value
     * @return Node
     */
    public function &find(&$node, $value)
    {
        if ($node->data == $value) {
            return $node;
        }
        if ($node->data > $value) {
            return $this->find($node->leftChild, $value);
        } else {
            return $this->find($node->rightChild, $value);
        }

        return null;
    }

    /**
     * 获取最小的节点
     *
     * @param Node $node
     * @return Node
     */
    public function &findMin(&$node)
    {
        if (empty($node->leftChild)) {
            return $node;
        }
        return $this->findMin($node);
    }

    /**
     * 删除节点
     *
     * @param int $value
     * @return bool
     */
    public function deleteNode($value)
    {
        // 找到该节点的引用
        $node = &$this->find($this->root, $value);
        if (empty($node)) {
            return false;
        }
        if (empty($node->leftChild)) {
            // 如果没有左孩子，则直接把右孩子作为父节点
            $node = $node->rightChild;
        } else if (empty($node->rightChild)) {
            // 如果没有右孩子，则直接把左孩子作为父节点
            $node = $node->leftChild;
        } else {
            // 既有左孩子又有右孩子
            // 找到被删除的节点的右孩子中的最小节点
            $minNode = &$this->findMin($node->rightChild);
            // 最小节点值替换为父节点值
            $node->data = $minNode->data;
            // 删掉最小节点
            $minNode = null;
        }
    }
}

$avlTree = new AVLTree();
$avlTree->init([4, 2, 12, 34, 8, 22]);
print_r($avlTree->root);
```