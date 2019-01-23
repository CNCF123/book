## COPY

2种形式：

* COPY \[--chown=&lt;user&gt;:&lt;group&gt;\] &lt;src&gt;... &lt;dest&gt;

* COPY \[--chown=&lt;user&gt;:&lt;group&gt;\] \["&lt;src&gt;",... "&lt;dest&gt;"\] （包含空格的路径需要此表单）

> **注意**：该`--chown`功能仅在用于构建Linux容器的Dockerfiles上受支持，并且不适用于Windows容器。由于用户和组所有权概念不能在Linux和Windows之间进行转换，因此使用`/etc/passwd`和`/etc/group`将用户名和组名转换为ID会限制此功能仅适用于基于Linux OS的容器。

该`COPY`指令从中复制新文件或目录`<src>`，并将它们添加到路径中容器的文件系统中`<dest>`。

`<src>`可以指定多个资源，但文件和目录的路径将被解释为相对于构建上下文的源。

每个都`<src>`可能包含通配符，匹配将使用Go的[filepath.Match](http://golang.org/pkg/path/filepath#Match)规则完成。例如：

```
COPY hom* /mydir/        # adds all files starting with "hom"

COPY hom?.txt /mydir/    # ? is replaced with any single character, e.g., "home.txt"
```

的`<dest>`是一个绝对路径，或相对于一个路径`WORKDIR`，到其中的源将在目标容器内进行复制。

    COPY test relativeDir/   # adds "test" to `WORKDIR`/relativeDir/

    COPY test /absoluteDir/  # adds "test" to /absoluteDir/

复制包含特殊字符（例如`[`和`]`）的文件或目录时，需要按照Golang规则转义这些路径，以防止它们被视为匹配模式。例如，要复制名为的文件`arr[0].txt`，请使用以下命令;

```
COPY arr[[]0].txt /mydir/    # copy a file named "arr[0].txt" to /mydir/
```

除非可选`--chown`标志指定给定用户名，组名或UID / GID组合以请求复制内容的特定所有权，否则将使用UID和GID为0创建所有新文件和目录。`--chown`标志的格式允许用户名和组名字符串或任意组合的直接整数UID和GID。提供没有组名的用户名或没有GID的UID将使用与GID相同的数字UID。如果提供了用户名或组名，则容器的根文件系统`/etc/passwd`和`/etc/group`文件将分别用于执行从名称到整数UID或GID的转换。以下示例显示了该`--chown`标志的有效定义：

```
COPY --chown=55:mygroup files* /somedir/

COPY --chown=bin files* /somedir/

COPY --chown=1 files* /somedir/

COPY --chown=10:11 files* /somedir/
```

如果容器根文件系统不包含任何文件`/etc/passwd`或`/etc/group`文件，并且`--chown`标志中使用了用户名或组名，则构建将在`COPY`操作上失败。使用数字ID不需要查找，也不依赖于容器根文件系统内容。

> **注意**：如果使用STDIN（`docker build - < somefile`）构建，则没有构建上下文，因此`COPY`无法使用。

（可选）`COPY`接受一个标志`--from=<name|index>`，该标志可用于将源位置设置为`FROM .. AS <name>`将用于替代用户发送的构建上下文的先前构建阶段（使用其创建）。该标志还接受为`FROM`指令启动的所有先前构建阶段分配的数字索引。如果找不到具有指定名称的构建阶段，则尝试使用具有相同名称的图像。

`COPY`遵守以下规则：

* 该`<src>`路径必须是内部_语境_的构建;你不能`COPY ../something /something`，因为a的第一步`docker build`是将上下文目录（和子目录）发送到docker守护程序。

* 如果`<src>`是目录，则复制目录的全部内容，包括文件系统元数据。

> **注意**：不复制目录本身，只复制其内容。

* 如果`<src>`是任何其他类型的文件，则将其与元数据一起单独复制。在这种情况下，如果`<dest>`以尾部斜杠结尾`/`，则将其视为目录，`<src>`并将写入内容`<dest>/base(<src>)`。

* 如果`<src>`直接或由于使用通配符指定了多个资源，则`<dest>`必须是目录，并且必须以斜杠结尾`/`。

* 如果`<dest>`不以尾部斜杠结束，则将其视为常规文件，`<src>`并将写入其中的内容`<dest>`。

* 如果`<dest>`不存在，则会在其路径中创建所有缺少的目录。



