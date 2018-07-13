# JS,jQuery以及AJAX基础

#### JS基本语法

**变量的定义**

变量必须以字母开头 , 也可以$或\_开头 , 变量名称对大小写敏感 .

使用var关键字声明变量 , 可以在一条语句中声明多个变量 , var a=1, b=2

未使用值来声明的变量 , 值是undefined , 也就是var a,b,c;

如果重新声明js变量 , 该变量的值不会丢失 . 例如 , var a=1; var a; 此时a还是1,不会丢失.

**数据类型**

字符串,数字,布尔,数组,对象,Null,Undefined.

js变量均为对象 , 即声明一个变量时 , 就创建了一个新对象 .

**创建原声对象**

new Object\(\);

```
person=new Object();
person.firstname="Bill";
person.lastname="Gates";
person.age=56;
person.eyecolor="blue";
# 或者
var myFather=new person("Bill","Gates",56,"blue");
```

使用对象构造器 , 然后new就可以了 .

```
function person(firstname,lastname,age,eyecolor)
{
this.firstname=firstname;
this.lastname=lastname;
this.age=age;
this.eyecolor=eyecolor;
}
```

json对象

```
person={firstname:"John",lastname:"Doe",age:50,eyecolor:"blue"};
```

**函数**

无默认值 . 

函数内部声明的变量\(使用var\)是局部变量 , 在函数外不能用 . 

在函数外声明的变量是全局变量 , 所有脚本和函数都能访问它 . 

**运算符**

+号可以用来拼接字符串 . 其他加减乘除都一样 . 

**流程控制**

else if要分开写

#### js内置对象

**Number对象**

```
var pi = 3.14;
var myNum = new Number(value);
var myNum = Number(value);
```

#### js html dom对象

#### jQuery基础知识



