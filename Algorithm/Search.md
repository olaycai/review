# 查找算法

## 顺序查找
顺序查找就是最常用的循环遍历查找，这种算法时间复杂度为n。

```php
$arr = [28, 39, 53, 12, 3, 94];
$target = 12;
foreach($arr as $index => $number) {
    if ($number != $target) {
        continue();
    }
    echo '找到数据，位置为:' . $index;
}
```

## 二分查找
这种查找前提是待查找的元素列表必须为有序的元素，然后每次查找都把列表分两半，大于中间的数则在右半段找，小于则在左半段找，不断重复只到找到
目标或者为空为止，时间复杂度为log2n

```php
$arr = [1, 10, 39, 48, 69, 83, 97, 101];
$target = 101;
$test = search($arr, $target);
echo $test;

/**
 * 二分查找(递归版本)
 *
 * @param array $arr
 * @param int $target
 * @param int $lastIndex
 * @return int
 */
function search($arr, $target, $lastIndex = 0) {
    $arrLength = count($arr);
    if ($arrLength == 1) {
        return $arr[0] === $target ? $lastIndex : -1;
    }
    // 获取中间位置(向下取整)
    $centerIndex = floor($arrLength / 2);

    // 判断是否等于中间数字
    if ($target == $arr[$centerIndex]) {
        return $centerIndex + $lastIndex;
    }

    // 不等于则把待查找的元素分割两半
    $startIndex = $target > $arr[$centerIndex] ? $centerIndex + 1 : 0;
    $cutLength = $target > $arr[$centerIndex] ? ($arrLength - $centerIndex - 1) : $centerIndex;

    // 记录切割后数组第一个元素在原数组的位置(用于后面index的输出)
    $lastIndex += $target > $arr[$centerIndex] ? $centerIndex + 1 : 0;
    // 切割数组
    $cutArr = array_slice($arr, $startIndex, $cutLength);
    return sort($cutArr, $target, $lastIndex);
}
```
另一个实现方法
```php
/**
 * 二分查找（下标法）
 *
 * @param Array $array
 * @param int $target
 * @return int
 */
function search($array, $target) {

    $start = 0;
    $end = count($array) - 1;

    // 判断是否头尾是查找目标
    if ($target == $array[$start]) {
        return $start;
    }
    if ($target == $array[$end]) {
        return $end;
    }

    while ($start <= $end) {
        $middle = floor(($start + $end) / 2);
        if ($target == $array[$middle]) {
            return $middle;
        }

        if ($target > $array[$middle]) {
            $start = $middle + 1;
        } else {
            $end = $middle - 1;
        }
    }

    return -1;
}
```

## 插值查找
这种方法是二分查找演变而来的，二分法是无脑的一直对半分，插值是算出大概位置作为分段依据。

```php
$arr = [1, 10, 39, 48, 69, 83, 97, 101];
$target = 97;

echo search($arr, $target);

/**
 * 插值查找（下标法）
 *
 * @param Array $array
 * @param int $target
 * @return int
 */
function search($array, $target) {

    $start = 0;
    $end = count($array) - 1;

    // 判断是否头尾是查找目标
    if ($target == $array[$start]) {
        return $start;
    }
    if ($target == $array[$end]) {
        return $end;
    }

    while ($start <= $end) {
        // 把有序元素分为(101 - 1)份，算出target大概在这个数中的百分之几的位置，然后用这个百分比乘以元素的个数
        // （97 - 1） / (101 -1) = 96%   下标为7的元素96%的位置是6(6.72向下取整)
        $middle = $start + ($target - $array[$start]) / ($array[$end] - $array[$start]) * ($end - $start);
        $middle = floor($middle);
        if ($target == $array[$middle]) {
            return $middle;
        }

        if ($target > $array[$middle]) {
            $start = $middle + 1;
        } else {
            $end = $middle - 1;
        }
    }

    return -1;
}
```

- 注意:
如果被查找的元素密度较密（数字跨度不大）则此算法比二分法高效。上例中只需要查找一次就能找到，如果最后一个改成1001则需要运行多次才能找到