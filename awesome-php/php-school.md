# PHP School

#### 安装

```
curl -O https://php-school.github.io/workshop-manager/workshop-manager.phar
mv workshop-manager.phar /usr/local/bin/workshop-manager
chmod +x /usr/local/bin/workshop-manager
workshop-manager verify
```

最后一步验证安装 , 可能会报错 , 都会有提示 , 按照提示即可 , 没报错会提示绿色OK

```
echo 'export PATH="$PATH:/Users/******/.php-school/bin"' >> ~/.bashrc && source ~/.bashrc
```

#### 使用

安装了workshop-manager , 就可以使用了 , 它仅仅是一个工具 , 用来搜索 , 安装 , 删除PHP School workshops的工具 .

更详细的说明在Github中查看

[https://github.com/php-school/workshop-manager](https://github.com/php-school/workshop-manager)

#### 常用命令

```
# 列出所有命令
workshop-manager list
# 搜索特定workshop的工具,不指定搜索workshop,会列出所有可用的workshop
workshop-manager search <topic>
# 安装和删除workshop
workshop-manager install <workshopname>
workshop-manager uninstall <workshopname>
```

#### 安装使用

安装workshop

```
workshop-manager install learnyouphp
learnyouphp
workshop-manager install callablefunctions
callablefunctions
workshop-manager install php7way
php7way
```

```
# 进入菜单交互方式
learnyouphp
# 打印当前进入菜单的说明
learnyouphp print
# 验证程序
learnyouphp verify program.php
# 输出运行
learnyouphp run program.php
```

参考

https://nodeschool.io/

