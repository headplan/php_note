# 使用组合来解决实际问题

举个例子, 假设你的老板进来了, 希望你能用过去30天登记的所有用户的电话号码生成一个新的报告 . 我们假定我们有以下类代表我们的用户 . 显然 , 一个真正的类将存储和返回真实的数据 , 但让我们只定义我们的 API : 

```php
<?php
class User
{
    public function phone(): string
    {
        return '';
    }

    public function registration_date(): DateTime
    {
        return new DateTime();
    }
}

$users = [new User(), new User(), new User()]; // etc.
```

如果没有任何关于函数编程的知识 , 您可以编写如下内容 :

```php
<?php
class User
{
    public function phone(): string
    {
        return '';
    }

    public function registration_date(): DateTime
    {
        return new DateTime();
    }
}

$users = [new User(), new User(), new User()]; // etc.
```



