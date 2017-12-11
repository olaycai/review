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

## 快速排序
原理是首先把第一个元素当作一个参考的key，然后根据这个key把待排的元素分为2部分key左边是比key小的元素，key的右边是比key大的元素。然后左边
和右边的两组分别再用第一个元素当作key，再分为2部分，一直循环直到数组大小为1为止。此排序算法为不稳定算法

```php
$arr = [28, 38, 1, 23, 80, 46, 58, 101, 94, 44, 2, 88];
$test = quickSort($arr);
print_r($test);

/**
 * 快速排序
 *
 * @param array $array 待排数组
 * @return array 返回排好序的数组
 */
function quickSort($array) {

    if (count($array) <= 1) {
        return $array;
    }

    $key = array_shift($array);
    $leftArr = [];
    $rightArr = [];
    foreach ($array as $num) {
        if ($num <= $key) {
            $leftArr[] = $num;
        }
        if ($num > $key) {
            $rightArr[] = $num;
        }
    }
    $leftArr = quickSort($leftArr);
    $rightArr = quickSort($rightArr);

    return array_merge($leftArr, [$key], $rightArr);
}
```

## 选择排序
原理为每一次都选择待排序列中最小的元素放在第一位，然后再在剩下的数组中找最小的放在第二位，以此类推到找到倒数第二个与第一个元素比较位置。
此算法是不稳定排序

```php
$arr = [28, 38, 1, 23, 80, 46, 58, 101, 94, 44, 2, 88];
$test = selectSort($arr);
print_r($test);

/**
 * 选择排序
 *
 * @param array $array 待排数组
 * @return array 返回排好序的数组
 */
function selectSort($array, &$sort = []) {

    for ($i = 0; $i < count($array); $i++) {
        $min = $i;
        
        // 查找最小元素的下标
        for ($j = $i + 1; $j < count($array); $j++) {
            if ($array[$min] > $array[$j]) {
                $min = $j;
            }
        }
        // 如果最小下标改变，则交换数据
        if ($min != $i) {
            $tmp = $array[$i];
            $array[$i] = $array[$min];
            $array[$min] = $tmp;
        }
    }

    return $array;
}

/**
 * 选择排序(递归简洁版)
 *
 * @param array $array 待排数组
 * @param array $sort 记录数组
 * @return array 返回排好序的数组
 */
function selectSort($array, $sort = []) {
    if (count($array) <= 0) {
        return $sort;
    }
    
    // 找到最小的元素
    $min = min($array);
    $minKey = array_search($min, $array);
    unset($array[$minKey]);

    $sort[] = $min;

    return selectSort($array, $sort);
}
```