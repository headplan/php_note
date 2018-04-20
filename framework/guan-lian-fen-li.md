# 关联分离

为了更好的复用我们的框架 , 现在讲入口文件中的代码打包到一个类中 . 入口文件public/index.php有一个输入点\(input\) , 即Request , 以及一个输出点\(output\) , 即Response . 我们的框架类将遵循以下简单原则 : **程序的逻辑就是要创建“与请求相关联”的响应** . 

#### 重构框架

```
./
├── README.md
├── app/
│   ├── Controllers/
│   │   └── LeapYearController.php
│   └── Models/
│       └── LeapYear.php
├── bootstrap/
│   ├── app.php
│   └── autoload.php
├── composer.json
├── composer.lock
├── public/
│   └── index.php
├── resources/
│   └── views/
│       ├── about.php
│       └── hello.php
├── routes/
│   └── web.php
├── src/
│   └── Headplan/
│       └── Framework.php
└── vendor/
    ├── autoload.php
    ├── composer/
    ├── psr/
    └── symfony/
```





