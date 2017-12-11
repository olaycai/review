# 排序算法
- 排序的稳定性

是指在排序过程中，待排的数据有相同的关键字，在排序过后，两者的位置没有发生变化，这样的排序算法是稳定的，反之为不稳定。
例如: [1, 4, 2, 2, 3], 第2和第3个元素的数值相同，如果排序过程中这两个元素位置发生的改变，则为不稳定的排序 
## 冒泡排序
最基础的排序方法，采用双循环，数据一层层往上冒，好像气泡一样，所以称为冒泡排序。时间复杂度为n^2，是稳定排序

```php
$arr = [28, 38, 1, 23, 80, 46, 58, 101, 94, 44, 2, 88];
$test = bubbleSort($arr);
print_r($test);

/**
 * 冒泡排序
 *
 * @param array $array 待排序的数组
 * @param string $type 排序类型
 * @return array 返回排好序的数组
 */
function bubbleSort($array, $type = 'asc') {
    $length = count($array);
    for ($i = 0; $i < $length - 1; $i++) {
        for ($j = $i + 1; $j < $length; $j++) {
            $compare = $type == 'asc' ? $array[$i] > $array[$j] : $array[$i] < $array[$j];
            // 如果前一个数大于/小于后一个数，则进行交换
            if ($compare) {
                // 临时保存$i的值以免被覆盖
                $tmp = $array[$i];
                $array[$i] = $array[$j];
                $array[$j] = $tmp;
            }
        }
    }
    return $array;
}
```

## 