## 常用子命令

kubectl是 Kubemetes 系统的命令行客户端工具，是管理员最常用的 集群管理工具 。

语法格式为:`kubectl [command] [TYPE] [NAME] [flags]`

说明如下:

1 \) command :对资游、执行相应操作的子命令，如 get、 create、 delete、 run等;

2\) TYPE: 资源对象的类型，如 pods、 services等;类型名称区分字符大小写，支持使用简写格式 。

3\) NAME:对象名称，区分字柯:大小写 ; 省略时，则表示指定 TYPE 的所有资源对象 ;

4\) flags: 命令行选项，如“-s”或“一server”;另外， get等命令在输出时还有一个常

用的标志“ -o yaml”用于指定输出格式  。

