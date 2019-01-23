## FROM

* `FROM <image> [AS <name>]`

* `FROM <image>[:<tag>] [AS <name>]`

* `FROM <image>[@<digest>] [AS <name>]`

该`FROM`指令初始化新的构建阶段并为后续指令设置[_基本映像_](https://docs.docker.com/engine/reference/glossary/#base-image)。因此，有效`Dockerfile`必须以`FROM`指令开始。图像可以是任何有效图像 - 通过从[_公共存储库中_](https://docs.docker.com/engine/tutorials/dockerrepos/)**提取图像**来启动它尤其容易。

* `ARG`是先于仅指示`FROM`在`Dockerfile`。请参阅[了解ARG和FROM如何交互](https://docs.docker.com/engine/reference/builder/#understand-how-arg-and-from-interact)。

* `FROM`可以在单个内容中多次出现`Dockerfile`以创建多个图像，或者使用一个构建阶段作为另一个构建阶段的依赖项。只需在每条新`FROM`指令之前记下提交输出的最后一个图像ID。每条`FROM`指令都清除先前指令创建的任何状态。

* 可选地，可以通过添加`AS name`到`FROM`指令来将名称赋予新的构建阶段。该名称可用于后续`FROM`和`COPY --from=<name|index>`指令，以引用此阶段构建的图像。

* 该`tag`或`digest`值是可选的。如果省略其中任何一个，则构建器默认采用`latest`标记。如果找不到`tag`值，构建器将返回错误。



