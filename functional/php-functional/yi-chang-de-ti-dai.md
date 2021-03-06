# 异常的替代

正如我们刚才所看到的, 如果我们想保持代码的纯净, 就不能使用异常。我们的选择是什么, 以确保我们可以表示一个错误的调用者我们的功能？我们希望我们的解决方案具有以下功能:

* 强制执行错误管理, 以便没有任何错误可以冒泡到最终用户
* 避免样板或复杂的代码结构
* 在我们的功能签名中发布
* 避免将错误误以为正确结果的风险

在本章下一节介绍一种具有所有这些优势的解决方案之前，我们来看看各种方法，在命令式语言中进行错误管理。

为了测试各种方法，我们将尝试实现我们早已使用的max函数：

```php
<?php
function max2(array $data): int
{
    return array_reduce($data, function(int $max, int $i) : int 
    {
        return $i > $max ? $i : $max;
    }, 0);
}
```

因为我们选择了初始值0 , 如果我们使用一个空数组调用函数 , 我们将得到结果0.是否真的是一个空数组的最大值 ? 如果我们调用与PHP自带的函数 , max\(\[\]\)方法会发生什么 ?

> Warning: max\(\): Array must contain at least one element

此外，返回值false。 我们的版本使用值0作为默认值，我们可以将false视为错误代码。 PHP版本还会向您发出警告。

现在我们可以改进这个函数 , 让我们尝试我们拥有的各种选择 , 从最坏的到最好的 .

#### 记录/显示错误消息

正如我们刚刚看到的, PHP 可以显示一个警告消息。我们也可以使用级别通知或错误消息。这可能是最坏的, 你可以做, 因为没有办法让你的功能调用者知道出了问题。一旦应用程序运行, 消息将只显示在日志或屏幕上。

此外，在某些情况下，错误是可以从中恢复的。 由于你不知道发生什么事情，所以在这种情况下你不能这样做。

更糟糕的是, PHP 允许您配置显示哪个错误级别。在大多数情况下, 通知只是隐藏的, 所以没有人会看到在应用程序中的某个地方发生了错误。

为了公平对待PHP，有一种方法可以在运行时使用通过set\_error\_handler参数声明的自定义错误处理程序来捕获这些警告和通知。 但是，为了正确地管理错误，您将不得不找到一种方法来确定处理程序内部是生成错误的函数，并相应地执行。

如果您有多个函数使用这些类型的消息来发出错误信号, 您很快就会有一个真正大的错误处理程序, 或者是大量的较小的, 使得整个过程容易出错, 而且非常繁琐。

#### 错误代码

错误代码是一种来自 C 语言的遗产, 它没有任何异常的概念。其思想是, 函数总是返回一个代码来表示计算的状态, 并找到其他方法来传递返回值。通常, 代码0意味着一切都很顺利, 任何其他的都是错误。

当涉及到数字错误代码时 , PHP就没有使用它们作为返回值的功能 , 据我所知 . 但是 , 当出现错误而不是期望值时 , PHP有很多函数返回false值 . 只用一个潜在的值来表示失败 , 可能会导致传播信息方面的困难 . 例如 , move\_uploaded\_file的文档规定 :

> 成功返回 TRUE。
>
> 如果文件名不是有效的上载文件, 则不会发生任何操作, 并且move\_uploaded\_file \(\) 将返回 False。如果文件名是有效的上载文件, 但由于某种原因无法移动, 则不操作将会发生, 而 move\_uploaded\_file \(\) 将返回 False。此外,将发出警告。

这意味着当您有错误时, 您将得到通知, 但您无法知道它是哪一类错误, 而不诉诸于读取错误信息。即使这样, 您也将缺少重要的信息, 例如, 上传的文件为何无效。

如果我们想更好地模拟 PHP 的max函数 , 我们可以这样做 :

```php
<?php
function max3(array $data)
{
    if(empty($data)) {
        trigger_error('max3(): Array must contain at least oneelement', E_USER_WARNING);
        return false;
    }

    return array_reduce($data, function(int $max, int $i) : int {
        return $i > $max ? $i : $max;
    }, 0);
}
```

因为现在我们的函数需要返回错误值的情况下, 我们不得不删除返回值的类型提示, 从而使我们的签名少一些自我记录。

其他函数 \(通常是包装外部库的功能\) 也会返回错误值, 以防出错, 但在窗体 X\_errno 和 X\_error 中有辅助函数, 返回有关所执行的上一个函数的错误的更多信息。几个例子是 curl\_exec、curl\_errno 和 curl\_error 函数。

这样的帮助者可以进行更细粒度的错误处理, 但你必须考虑到认知成本。未实施错误管理。为了进一步说明我的观点, 让我们注意, 即使是正式文档中的 curl\_exec 函数的示例也没有设置检查返回值的最佳做法 :

```php
<?php
/* create a new cURL resource */
$ch = curl_init();
/* set URL and other appropriate options */
curl_setopt($ch, CURLOPT_URL, "http://www.example.com/");
curl_setopt($ch, CURLOPT_HEADER, 0);
/* grab URL and pass it to the browser */
curl_exec($ch);
/* close cURL resource, and free up system resources */
curl_close($ch);
```

使用错误的值作为失败的标记在执行松散类型转换 \(如 PHP\) 的语言中还有另一个后果。如上述文档中所述, 如果不执行严格的相等比较, 则可能会考虑将计算结果为 false 的有效返回值作为错误 :

> 警告: 此函数可能返回布尔值 false, 但也可能返回计算结果为 false 的非布尔数值。有关详细信息, 请阅读有关布尔值的部分。使用 === 运算符测试此函数的返回值。

PHP仅在出现错误的情况下使用错误代码，但不像C中通常情况那样返回true或0。您不需要找到向用户传回返回值的方式。

但是, 如果您希望使用数字错误代码实现自己的函数, 从而有可能对错误进行分类, 则必须找到一种方法来返回代码和值。通常, 您可以使用以下两个选项之一 :

* 使用通过引用传递的参数来保存结果;例如, 即使出于不同的原因, preg\_match 参数也会这样做。只要参数被清楚地确定为返回值, 这并不是严格地反对功能纯度。
* 返回可以容纳两个或更多值的数组或其他数据结构。这个想法是我们将在下一节中提出的功能性解决方案的开始。

#### 默认值或null

当涉及到认知负担时, 稍微比错误代码更好的是默认值。如果函数只有一组减少的输入, 可能导致错误, 或者错误原因不重要, 则可以想象返回默认值, 而不是通过错误代码指定错误原因。

然而, 这将打开一个新的蠕虫罐头。确定什么是好的默认值并不总是容易的, 在某些情况下, 默认值也将是有效值, 这使得无法确定是否存在错误。例如, 如果在调用 max2 函数时得到0的结果, 则无法知道该数组是空的还是只包含值0和负数。

默认值也可能取决于上下文, 在这种情况下, 您必须向函数中添加一个参数, 以便在调用它时也可以指定默认值。除了使函数签名更大外, 这还会破坏我们以后将学习到的一些性能优化, 虽然是完全纯和借鉴透明的, 但增加了认知负担。

让我们在 max 函数中添加一个默认值参数:

```php
<?php
function max4(array $data, int $default = 0): int
{
    return empty($data) ? $default :
    array_reduce($data, function(int $max, int $i) : int
    {
        return $i > $max ? $i : $max;
    }, 0);
}
```

在强制执行默认类型时, 我们能够还原返回值的类型提示。如果您希望将任何内容作为默认值传递, 则还必须删除类型提示。

为了避免某些讨论的问题, 值 null 有时用作默认的返回值。虽然不是一个真正的值, 但 null 在错误代码类别中没有分类, 因为它在某些情况下是完全有效的值。假设您正在搜索集合中的项目, 如果找不到什么, 您会返回什么？

但是, 使用 null 值作为可能的返回值有两个问题:

* 不能将返回类型提示用作 null, 不会将其视为正确的类型。此外, 如果计划将该值用作参数, 则它也不能被提示为类型, 或者它必须是可选的, 值为 null 作为默认值。这将强制您删除类型提示或使参数可选。
* 如果函数通常返回对象, 则必须检查值是否为 null, 否则您将面临托尼霍尔所称的十亿美元错误的风险, 这是一个空指针引用。或者, 正如在 PHP 中报告的那样, 在 null 时调用一个成员函数XXX \(\)

> 这里托尼霍尔的典故可以查看下面的内容了解 :
>
> [https://www.infoq.com/presentations/Null-References-The-Billion-Dollar-Mistake-Tony-Hoare](https://www.infoq.com/presentations/Null-References-The-Billion-Dollar-Mistake-Tony-Hoare)

#### Error handler\(错误处理程序\)

最后一种方法在 JavaScript 世界中得到了很大的应用, 那里的回调无处不在。其思想是在每次调用函数时传递一个错误回调。如果允许调用方通过多个回调, 则它可能会更强大, 对于可能出现的每种错误都有一个。

尽管它缓解了默认值所具有的一些问题, 例如将一个有效值与默认数值混合起来的可能性, 但您仍然需要根据上下文传递不同的回调, 从而使此解决方案稍微好一些。

这种方法将如何寻找我们的功能？请考虑以下实现:

```php
<?php
function max5(array $data, callable $onError): int
{
    return empty($data) ? $onError() :
    array_reduce($data, function(int $max, int $i) : int {
        return $i > $max ? $i : $max;
    }, 0);
}

max5([], function(): int {
    // 你可以自由地做任何你想做的事。
    // 在这样一个简单的情况下不是很有用, 
    // 但是当创建复杂的对象时, 它可以证明其是无价的。
    return 42;
});
```

同样, 我们保留了返回类型提示, 因为我们与调用方的约定是返回一个整数值。如注释中所述, 在这种特殊情况下, 作为参数的默认值可能已经足够, 但在更复杂的情况下, 这种方法提供了更多的权力。

我们还可以设想将初始参数传递给回调, 以及有关失败的信息, 以便错误处理程序可以相应地采取行动。在某种程度上, 这种方法有点像我们之前看到的所有内容的组合, 因为它允许您:

* 指定选择的默认返回值
* 显示或记录所需的任何类型的错误信息
* 如果您希望, 返回一个更复杂的数据结构和错误代码



