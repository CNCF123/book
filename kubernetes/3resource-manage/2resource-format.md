## 资源格式

API Server 仅接受及响应 JSON格式的数据\( JSON对象\)。

同时，为了便于使用，它也允许用户提供 YAML 格式的POST 对象，但API Server需要事先自行将其转换为JSON格式后方能提交。API Server接受和返回的所有JSON对象都遵循同一个模式，它们都具有kind 和 apiVersion宇段，用于标识对象所属的资源类型、API群组及相关的版本。

