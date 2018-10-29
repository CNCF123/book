## 标签 {#label}

```
LABEL <key>=<value> <key>=<value> <key>=<value> ...
```

该`LABEL`指令将元数据添加到图像。A`LABEL`是键值对。要在`LABEL`值中包含空格，请使用引号和反斜杠，就像在命令行解析中一样。一些用法示例：

```
LABEL "com.example.vendor"="ACME Incorporated"

LABEL com.example.label-with-value="foo"

LABEL version="1.0"

LABEL description="This text illustrates \

that label-values can span multiple lines."
```

图像可以有多个标签。您可以在一行中指定多个标签。在Docker 1.10之前，这减小了最终图像的大小，但现在不再是这种情况了。您仍然可以选择在单个指令中指定多个标签，方法有以下两种：

```
LABEL multi.label1="value1" multi.label2="value2" other="value3"

```

```
LABEL multi.label1="value1" \

      multi.label2="value2" \

      other="value3"
```

基本或父图像中包含的标签（`FROM`线中的图像）由图像继承。如果标签已存在但具有不同的值，则最近应用的值将覆盖任何先前设置的值。

要查看图像的标签，请使用该`docker inspect`命令。

```
"Labels": {

    "com.example.vendor": "ACME Incorporated"

    "com.example.label-with-value": "foo",

    "version": "1.0",

    "description": "This text illustrates that label-values can span multiple lines.",

    "multi.label1": "value1",

    "multi.label2": "value2",

    "other": "value3"

},
```



