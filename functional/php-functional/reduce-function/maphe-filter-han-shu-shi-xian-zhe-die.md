# Map和Filter函数实现折叠

现在 , 我们的折叠只返回简单的标量值 . 但没有什么能阻止我们构建更复杂的数据结构 . 例如 , 我们可以使用折叠实现映射和筛选功能:

```php
<?php
function map(array $data, callable $cb): array
{
    return array_reduce($data, function(array $acc, $i) use ($cb)
    {
        $acc[] = $cb($i);

        return $acc;
    }, []);
}

function filter(array $data, callable $predicate): array
{
    return array_reduce($data, function(array $acc, $i) use($predicate)
    {
        if($predicate($i)) {
            $acc[] = $i;
        }

        return $acc;
    }, []);
}
```

同样 , 这些主要是为了证明有可能返回数组与折叠 . 如果不需要操作更复杂的集合 , 本机函数就足够了 . 

作为一个练习 , 你可以尝试实现 map\_filter 或 filter\_map 函数 , 如果你喜欢, 还有 array\_reverse 函数 . 还可以尝试编写头部和尾部方法 , 它返回数组中的第一个、最后一个元素 , 并经常在函数语言中找到 . 

正如你所看到的 , 折叠是非常强大的 , 它背后的想法是很多功能技术的核心 . 这就是为什么我更喜欢谈论折叠而不是减少 , 我觉得有点还原 , 双关的意图 . 

在更进一步之前 , 要确保你了解折叠是如何工作的 , 因为它会使其他的事情变得更容易 . 

