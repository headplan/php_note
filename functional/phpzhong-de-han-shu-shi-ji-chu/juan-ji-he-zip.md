# 卷积和Zip

卷积, 或我们常说的zip , 是将所有给定数组中的每个第 n 个元素组合在一起的过程。实际上, 这正是我们通过将 null 值传递给 array\_map 函数之前所做的工作 :

```php
<?php
print_r(array_map(null, [1, 2], ['one', 'two'], ['un', 'deux']));

# 输出
Array
(
    [0] => Array
        (
            [0] => 1
            [1] => one
            [2] => un
        )
    [1] => Array
        (
            [0] => 2
            [1] => two
            [2] => deux
        )
)
```

重要的是要注意, 如果数组的长度不同, PHP 将使用 null 作为填充值 :

```php
<?php
$numerals = [1, 2, 3, 4];
$english = ['one', 'two'];
$french = ['un', 'deux', 'trois'];
print_r(array_map(null, $numerals, $english, $french));

# 输出
Array
(
    [0] => Array
        (
            [0] => 1
            [1] => one
            [2] => un
        )
    [1] => Array
        (
            [0] => 2
            [1] => two
            [2] => deux
        )
    [2] => Array
        (
            [0] => 3
            [1] =>
            [2] => trois
        )
    [3] => Array
        (
            [0] => 4
            [1] =>
            [2] =>
        )
)
```

请注意, 在大多数编程语言中, 包括 "Haskell"、"Scala" 和 "Python" 中, zip 操作将在最短的数组中停止, 而不会填充任何值。您可以尝试在 PHP 中实现类似的功能, 例如, 使用 array\_slice 函数在调用 array\_merge 函数之前将所有数组都缩小到相同的大小。

我们还可以通过从阵列数组中创建多个阵列来执行反向操作。这个过程有时被称为解压缩。这里是一个幼稚的实现, 这是缺少了大量的检查, 使其足够健壮的生产使用 :

```php
<?php
function unzip(array $data): array
{
    $return = [];
    $data = array_values($data);
    $size = count($data[0]);

    foreach($data as $child) {
        $child = array_values($child);
        for($i = 0; $i < $size; ++$i) {
            if(isset($child[$i]) && $child[$i] !== null) {
            $return[$i][] = $child[$i];
            }
        }
    }

    return $return;
}

# 你可以像这样使用它
$zipped = array_map(null, $numerals, $english, $french);
list($numerals2, $english2, $french2) = unzip($zipped);
var_dump($numerals == $numerals2);
// bool(true)
var_dump($english == $english2);
// bool(true)
var_dump($french == $french2);
// bool(true)
```



