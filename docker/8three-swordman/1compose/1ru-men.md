## compose

Compose是一个用于定义和运行多容器Docker应用程序的工具。使用Compose，您可以使用YAML文件来配置应用程序的服务。然后，使用单个命令，您可以从配置中创建并启动所有服务。要了解有关Compose所有功能的更多信息，请参阅功能列表。

Compose适用于所有环境：生产，登台，开发，测试以及CI工作流程。您可以在常见用例中了解有关每个案例的更多信息。

使用Compose基本上是一个三步过程：

* 使用Dockerfile定义应用程序的环境，以便可以在任何地方进行复制。
* 在docker-compose.yml中定义构成应用程序的服务，以便它们可以在隔离环境中一起运行。
* 运行docker-compose up和Compose启动并运行整个应用程序。



