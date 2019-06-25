## ADD

## \[ 建议使用COPY \]

2种形式：

* `ADD [--chown=<user>:<group>] <src>... <dest>`

* `ADD [--chown=<user>:<group>] ["<src>",... "<dest>"] （包含空格的路径需要此表单）`

该`ADD`指令从中复制新文件，目录或远程文件URL`<src>`，并将它们添加到路径上图像的文件系统中`<dest>`。

`<src>`可以指定多个资源，但如果它们是文件或目录，则它们的路径将被解释为相对于构建上下文的源。

每个都`<src>`可能包含通配符，匹配将使用Go的[filepath.Match](http://golang.org/pkg/path/filepath#Match)规则完成。例如：

```
ADD hom* /mydir/        # adds all files starting with "hom"

ADD hom?.txt /mydir/    # ? is replaced with any single character, e.g., "home.txt"
```

的`<dest>`是一个绝对路径，或相对于一个路径`WORKDIR`，到其中的源将在目标容器内进行复制。

    ADD test relativeDir/          # adds "test" to `WORKDIR`/relativeDir/

    ADD test /absoluteDir/         # adds "test" to /absoluteDir/

添加包含特殊字符（例如`[`和`]`）的文件或目录时，需要按照Golang规则转义这些路径，以防止它们被视为匹配模式。例如，要添加名为的文件`arr[0].txt`，请使用以下命令;

```
ADD arr[[]0].txt /mydir/    # copy a file named "arr[0].txt" to /mydir/
```

除非可选`--chown`标志指定给定用户名，组名或UID / GID组合以请求所添加内容的特定所有权，否则将使用UID和GID为0创建所有新文件和目录。`--chown`标志的格式允许用户名和组名字符串或任意组合的直接整数UID和GID。提供没有组名的用户名或没有GID的UID将使用与GID相同的数字UID。如果提供了用户名或组名，则容器的根文件系统`/etc/passwd`和`/etc/group`文件将分别用于执行从名称到整数UID或GID的转换。以下示例显示了该`--chown`标志的有效定义：

```
ADD --chown=55:mygroup files* /somedir/

ADD --chown=bin files* /somedir/

ADD --chown=1 files* /somedir/

ADD --chown=10:11 files* /somedir/
```

如果容器根文件系统不包含任何文件`/etc/passwd`或`/etc/group`文件，并且`--chown`标志中使用了用户名或组名，则构建将在`ADD`操作上失败。使用数字ID不需要查找，也不依赖于容器根文件系统内容。

在`<src>`远程文件URL的情况下，目标将具有600的权限。如果正在检索的远程文件具有HTTP`Last-Modified`标头，则来自该标头的时间戳将用于设置`mtime`目标文件。但是，与在处理期间处理的任何其他文件一样`ADD`，`mtime`将不包括在确定文件是否已更改且应更新缓存中。

> **注意**：如果通过传递`Dockerfile`STDIN（`docker build - < somefile`）进行构建，则没有构建上下文，因此`Dockerfile`只能包含基于URL的`ADD`指令。您还可以通过STDIN :\(`docker build - < archive.tar.gz`）传递压缩存档`Dockerfile`，该存档位于存档的根目录，其余存档将用作构建的上下文。
>
> **注意**：如果您的网址文件都使用认证保护，您将需要使用`RUN wget`，`RUN curl`或使用其它工具从容器内的`ADD`指令不支持验证。
>
> **注意**：`ADD`如果内容`<src>`已更改，则第一个遇到的指令将使来自Dockerfile的所有后续指令的高速缓存无效。这包括使缓存无效以获取`RUN`指令。有关详细信息，请参阅“[`Dockerfile`最佳实践指南](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/#/build-cache)”。

`ADD`遵守以下规则：

* 该`<src>`路径必须是内部_语境_的构建;你不能`ADD ../something /something`，因为a的第一步`docker build`是将上下文目录（和子目录）发送到docker守护程序。

* 如果`<src>`是URL并且`<dest>`不以尾部斜杠结尾，则从URL下载文件并将其复制到`<dest>`。

* 如果`<src>`是URL并且`<dest>`以尾部斜杠结尾，则从URL推断文件名并将文件下载到`<dest>/<filename>`。例如，`ADD http://example.com/foobar /`将创建该文件`/foobar`。URL必须具有非常重要的路径，以便在这种情况下可以发现适当的文件名（`http://example.com`不起作用）。

* 如果`<src>`是目录，则复制目录的全部内容，包括文件系统元数据。

> **注意**：不复制目录本身，只复制其内容。

* 如果`<src>`是以可识别的压缩格式（identity，gzip，bzip2或xz）的_本地\_tar存档，则将其解压缩为目录。从资源_远程\_网址**不**解压。复制或解压缩目录时，它具有与之相同的行为`tar -x`，结果是：

  1. 无论在目的地路径上存在什么，
  2. 源树的内容，在逐个文件的基础上解决了有利于“2.”的冲突。

  > **注意**：文件是否被识别为可识别的压缩格式仅基于文件的内容而不是文件的名称来完成。例如，如果一个空文件碰巧结束，`.tar.gz`这将不会被识别为压缩文件，并且**不会**生成任何类型的解压缩错误消息，而是将文件简单地复制到目标。

* 如果`<src>`是任何其他类型的文件，则将其与元数据一起单独复制。在这种情况下，如果`<dest>`以尾部斜杠结尾`/`，则将其视为目录，`<src>`并将写入内容`<dest>/base(<src>)`。

* 如果`<src>`直接或由于使用通配符指定了多个资源，则`<dest>`必须是目录，并且必须以斜杠结尾`/`。

* 如果`<dest>`不以尾部斜杠结束，则将其视为常规文件，`<src>`并将写入其中的内容`<dest>`。

* 如果`<dest>`不存在，则会在其路径中创建所有缺少的目录。



