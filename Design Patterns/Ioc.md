# Ioc

## Ioc(控制反转)
> 概念就是对组件对象控制权的转移，从程序代码本身转移到了外部容器

通俗来说就是A类要调用C类，不在A类中new C，而是把C类的实例通过参数传进A类中。

## 优点
通过外部引入对象达到高度的解耦。对象变更后调用的类无需或者只需少许改变，可以实现对象的热拔插。

## 缺点
- 代码量没有那么直观
- 由于对象生成基于反射机制，所以效率上有些损耗(复杂的系统中这个损耗是值得的)

## 与DI的关系
依赖注入是实现控制反转的一个方法

## 示例
以一个汽车组装为例来演示Ioc带来的好处

### 传统模式
```php
class BMWEngine {

    public $brand = '宝马';
    /**
     * 引擎工作
     *
     * @return int
     */
    public function work()
    {
        return 280;
    }
}
```
我们的车用的是宝马的引擎，则代码如下:
```php
class Car {

    /**
     * 汽车发动机
     *
     * @var mixed
     */
    protected $engine = null;

    /**
     * 构造函数
     *
     */
    public function __construct()
    {
        $this->engine = new BMWEngine();
    }

    /**
     * 开车
     *
     * @return void
     */
    public function drive() {
        echo $this->engine->brand . '引擎最高时速' . $this->engine->work();
    }
}
```
看上去好像没什么问题，但是如果我现在又要生产一个奔驰的车，那么我又需要复制一个Car类，然后改为BenzCar，然后更改构造函数中的引擎为奔驰的
引擎。这样做太麻烦了，当然还可以使用工厂模式，把对应的汽车品牌传进去Car的构造函数中，一般来说这样已经够好的了，但是如果N多个品牌，那么在
Car的构造函数中就会有N多个case: xxx;代码端。那么我们现在就来尝试一下Ioc的模式。

首先定义一个汽车类
```php
class Car {

    /**
     * 汽车发动机
     *
     * @var mixed
     */
    protected $engine = null;

    /**
     * 构造函数
     *
     * @param EngineInterface $engine
     */
    public function __construct(EngineInterface $engine)
    {
        $this->engine = $engine;
    }

    /**
     * 开车
     *
     * @return void
     */
    public function drive() {
        echo $this->engine->brand . '引擎最高时速' . $this->engine->work();
    }
}
```
构造函数中传入的是引擎对象，引擎对象需要有个标准，所以都必须实现引擎接口类，如下：
```php
interface EngineInterface {
    /**
     * 引擎工作
     *
     * @return int
     */
    public function work();
}
```
接下来就是各个品牌的引擎了
```php
class BMWEngine implements EngineInterface {

    public $brand = '宝马';
    /**
     * 引擎工作
     *
     * @return int
     */
    public function work()
    {
        return 280;
    }
}
class BenzEngine implements EngineInterface {

    public $brand = '奔驰';
    /**
     * 引擎工作
     *
     * @return int
     */
    public function work()
    {
        return 290;
    }
}
...N多个品牌
```
现在所有的零件都准备好了，你可以自己组装一台车
```php
$bmwEngine = new BMWEngine();
$car = new Car($bmwEngine);
$car->drive();
```
这样就是实现了依赖注入，Car类依赖BMWEngine类来发动汽车。但这种方式仍然不够炫酷，我们要使用汽车工厂来组装汽车(Ioc容器)
```php
class CarFactory {

    /**
     * 生产线
     *
     * @var array
     */
    protected $productionLine = [];

    /**
     * 预定
     *
     * @param String $productName
     * @param mixed $product
     * @return void
     */
    public function order($productName, $product)
    {
        // 这里这么写是因为传进来的是匿名函数，为什么要用匿名函数呢？因为初始化Car的时候需要
        // 初始化对应的引擎，如果只传入实例化后的对象，则不能达到效果
        if ($product instanceof Closure) {
            // 是否为匿名函数
            $this->productionLine[$productName] = $product;
        } 
    }

    /**
     * 组装
     *
     * @param String $productName
     * @param mixed $material
     * @return mixed
     */
    public function packaging($productName, $material = [])
    {
        if (!isset($this->productionLine[$productName])) {
            return;
        }

        $material = is_array($material) ? $material : [$material];
        // 因为用到CarFactory，所以把当前对象加入参数中
        array_unshift($material, $this);
        // 这里是实现的重点，使用的是反射机制，这里先取出order传入的匿名函数，然后传入参数调用方法
        return call_user_func_array($this->productionLine[$productName], $material);
    }
}
```
好了，工厂也有了。那就让工厂来生产汽车吧
```php
$carFactory = new CarFactory();

// 工厂预定宝马品牌引擎(传入的是匿名函数)
$carFactory->order('BMW', function($carFactory) {
    return new BMWEngine();
});

// 工厂预定奔驰品牌引擎(默认传的参数$carFactory，不写则会提示错误)
$carFactory->order('Benz', function($carFactory) {
    return new BenzEngine();
});

// 预定一台车
$carFactory->order('car', function($carFactory, $brand) {
    // 这里就是使用匿名函数的原因
    return new Car($carFactory->packaging($brand));
});

// 组装汽车
// 这里先是使用了Car,定位到了上面的匿名函数，匿名函数需要传入2个参数，因为packaging类中默认加入
// 了$this作为参数(也就是)CarFactory类，所以只需要传入引擎就可以生产汽车了
$bmwCar = $carFactory->packaging('car', 'BMW');
$bmwCar->drive();
```