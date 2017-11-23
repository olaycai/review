# 单例模式
这个设计模式比较简单，主要是为了保持对象在系统的唯一性。在以往的工作中接触到的单例模式有数据库连接以及配置
的读取。

## 优势
就拿数据库连接做成单例来说，这样的好处是可以节省不断连接释放句柄造成的消耗。

## 要点
需要确保类不能被实例化，获取对象需要通过静态方法来获取

## 实例

```php
class DB {

    /**
     * 存储实例化变量
     *
     * @var DB
     */
    private static $_db = null;

    /**
     * 初始化函数 默认不能使用new实例化
     *
     */
    private function __construct() {
    }
    
    /**
     * 禁止克隆
     *
     */
    private function __clone() {
    }

    /**
     * 获取实例的方法
     *
     * @return DB
     */
    public static function getInstance() {
        // 判断是否存在实例，不存在则new一个
        if (empty(self::$_db)) {
            self::$_db = new self();
        }

        return self::$_db;
    }

    /**
     * 测试方法
     *
     * @return String
     */
    public function test() {
        echo "hello world";
    }

}

// 此实例化方法报错
$db = new DB();

// 正确用法
$db = DB::getInstance();
$db->test();
```
在Java中还有分`懒汉式`和`饿汉式`的区别，上面的php属于`懒汉式`。`饿汉式`是指在定义成员变量的时候直接new变量，因为php语法限制，
所以无法跑通例子，原理上来说就是
```php
private static $_db = new self();
```

