## 端口设置

pods.spec.containers.ports 字段的值是一个列表，由一到多个端口对象组成，它的常用嵌套宇段包括如下几个 。

name : 当前端口的名称，必须符合 IANA\_SVC\_NAME 规范且在当前Pod内必须是唯一的。

containerPort &lt;integer&gt; :必选字段指定在Pod对象的IP地址上暴露的容器端口，有效范围为\( 1-65535 \);使用时，应该总是指定容器应用正常监听着的端口 。

protocol:端口相关的协议 ，其值仅可为TCP或UDP，默认为TCP。

