# 开始之前

## 配置`MySQL`

参考：https://blog.csdn.net/yingLGG/article/details/121400284

安装`mysql`

```bash
sudo apt-get install mysql-server
```

进行初始化配置

```bash
sudo mysql_secure_installation
```

具体如何初始化配置，参考：https://zhuanlan.zhihu.com/p/610793026

> 启动server
>
> ./server port
>
> 浏览器端
>
> ip:port

实际上是这样的：

在terminal中输入`./server 9006`

在浏览器中输入`localhost:9006`

其中，9006可以替换为任意四位数字

就可以啦







# Something new

## 统计代码行数



```shell
find  *.c | xargs wc -l | sort -n
```

