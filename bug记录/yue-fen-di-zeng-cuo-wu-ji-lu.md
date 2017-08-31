# 常见错误

```php
<?php
date_default_timezone_set('Asia/Shanghai');
# 初始化每月第一天
$first_day_of_month = date('Y-m',time()) . '-01 00:00:01';
$t = strtotime($first_day_of_month);
print_r(array(
    date('Y年m月',$t),
    date('Y年m月',strtotime('- 1 month',$t)),
    date('Y年m月',strtotime('- 2 month',$t)),
));
```



