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



