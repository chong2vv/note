## PHP学习
### PHP输出
PHP echo ,print 和 print_r 语句
- echo   - 可以输出一个或多个字符串
- print   - 只能输出简单类型变量的值,如int,string
- print_r - 可以输出复杂类型变量的值,如数组,对象

### 基本数据类型
弱语言，不需要指明，但是依旧要了解他的数据类型：
> String（字符串）, Integer（整型）, Float（浮点型）, Boolean（布尔型）, Array（数组）, Object（对象）, NULL（空值）。 

### 设置常量
设置常量的话，使用define()函数：

```
define(string constatn_name, mixed value, case_sensitive = true);

constant_name：必选参数，常量名称，即标志符。
value：必选参数，常量的值。
case_sensitive：可选参数，指定是否大小写敏感，设定为 true 表示不敏感。
```

### 字符串常用函数
长度：strlen() 函数

```
echo strlen("Hello world!");
```
匹配字符串：strpos()

```
echo strpos("Hello world!","world");
```
### 运算符
并置 a.b

```
	"Hi" . "Ha" = "HiHa"
```

### PHP数组
array()用于创建数组

```
array();
```
数组长度 count() 函数

```
echo count($cars); 
```
遍历数组

```
<?php 
$age=array("Peter"=>"35","Ben"=>"37","Joe"=>"43"); 

foreach($age as $x=>$x_value) 
{ 
echo "Key=" . $x . ", Value=" . $x_value; 
echo "<br>"; 
} 
?>
```
添加元素

```
array_push($userArray, "1");
```



