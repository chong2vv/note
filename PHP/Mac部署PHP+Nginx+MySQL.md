## Mac部署PHP+Nginx+MySQL
### Homebrew
Mac的很多环境都是直接使用[Homebrew](https://brew.sh/)安装的（毕竟神器），安装方法：

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

### Nginx
Nginx的话直接使用Homebrew安装，

```
brew install nginx
```
安装之后就可以启动了，两种启动方式，可以直接通过Nginx的命令启动，也可以使用brew的方式启动，brew的方式为后台启动。

```
//Nginx命令启动
nginx
//重启 这里如果报权限问题的话，可以使用sudo
nginx -s reload

//brew方式启动
brew services start nginx
```
至于Nginx的配置文件，默认安装时在这个路径下：

```
/usr/local/etc/nginx
```
关于具体的Nginx配置，网上有很多的事例，这里就不复制粘贴了，启动后访问默认的80端口，出现Nginx字样的欢迎页表示安装及启动成功。

### PHP安装
依旧通过Homebrew安装，默认install php是安装最新版本，也就是7.3（当前）

```
brew install php
```
这里特别注意的是Mac本身自带了PHP环境，也就是说默认的是使用系统自带的PHP，所以，这里如果想使用我们自己安装的版本，需要修改一下系统路径，也就是在`~/.bash_profile`里新增
```
export PATH="/usr/local/Cellar/php/7.3.3/bin:$PATH"
export PATH="/usr/local/Cellar/php/7.3.3/sbin:$PATH"
```
然后刷新配置生效：

```
source ~/.bash_profile
```
安装完毕之后，同样可以直接使用php-fpm启动，或者也可以继续通过brew启动，brew的启动方式和之前Nginx一样，php-fpm启动停止

```
php-fpm -D
killall php-fpm //暴力
```
当然，如果权限问题的话，使用sudo运行。

这里Nginx启动PHP的时候可能会遇到权限问题，这里可以查看并修改php-fpm的配置文件，笔者的brew安装的php默认路径是在`/usr/local/etc/php/7.3/php-fpm.d`下的`www.conf`文件，查找并修改如下即可：

```
user = wangyuandong //自己的用户名
group = staff
```

### MySQL
先碎碎念一下，笔者开始是用的brew安装，但是安装半天始终会遇到各种问题，后来直接使用dmg安装，瞬间完毕。。。有兴趣的同学可以继续研究一下brew安装，这里笔者就直接选择dmg安装了。

1. 先下载[mysql](https://dev.mysql.com/downloads/mysql/5.7.html#downloads)（推荐5.7）
2. 一路默认安装，最后弹窗会给mqsql默认密码，记得保存

安装成功之后，同样添加环境配置，如上PHP

```
export PATH="/usr/local/mysql/bin:$PATH"
```
开启和关闭MySQL服务，直接在系统的偏好设置里开启关闭即可，开启后可以连接一下MySQL：

```
mysql -u root -p
```
然后输入之前保存的密码即可。第一次连接的话，会问你是否修改密码，当然，之后也肯定是可以修改的

```
set PASSWORD =PASSWORD('123456')
```

### 安装扩展
目前主要的扩展例如Redis、Yaf等，都可以直接使用pecl安装：

```
pecl install redis
pecl install yaf
```
安装之后，可以直接使用，已经自动配置好php.ini文件了。

### 最后
如果想要自动加载某个包作为全局变量，可以修改php.ini文件
```
auto_prepend_file = ""//文件路径
```



