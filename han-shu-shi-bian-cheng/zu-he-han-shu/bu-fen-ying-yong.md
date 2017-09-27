# 部分应用

您可能需要设置函数的一些参数 , 但将其中的一部分保留为以后未分配 . 例如 , 我们可能希望创建一个函数来返回一个博客帖子的摘录 

设置此类值的专用术语是绑定参数或绑定一个参数 . 进程本身称为部分应用程序 , 新函数被设置为部分应用 . 

这样做的简单方法是将函数换为一个新的 : 

```php
<?php

function excerpt(string $s)
{
    return substr($s, 0, 5);
}

echo excerpt('Lorem ipsum dolor si amet.');
// Lorem
```



