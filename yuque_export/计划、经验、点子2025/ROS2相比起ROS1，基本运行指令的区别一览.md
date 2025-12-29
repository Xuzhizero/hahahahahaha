---
title: ROS2相比起ROS1，基本运行指令的区别一览
slug: mnmr1xgkv7dgyw5g
url: https://www.yuque.com/caogongzi/pu6995/mnmr1xgkv7dgyw5g
---

以下是 ROS2 与 ROS1 在基本运行指令上的关键区别一览表，帮助你快速迁移或对比两者的核心命令：

---

**1. 核心命令**

| 功能 | ROS1 | ROS2 | 说明 |
| --- | --- | --- | --- |
| 启动核心服务 | `roscore` | 无需显式启动 | ROS2 使用去中心化设计（DDS 中间件） |
| 运行节点 | `rosrun <pkg> <node>` | `ros2 run <pkg> <node>` | 语法类似，但节点名称规则不同 |
| 启动文件 | `roslaunch <pkg> <launch>` | `ros2 launch <pkg> <launch>` | ROS2 支持 Python 和 XML 格式 |


---

**2. 节点与话题**

| 功能 | ROS1 | ROS2 | 说明 |
| --- | --- | --- | --- |
| 列出所有节点 | `rosnode list` | `ros2 node list` | ROS2 显示节点名称和命名空间 |
| 列出所有话题 | `rostopic list` | `ros2 topic list` | 功能一致 |
| 查看话题信息 | `rostopic info <topic>` | `ros2 topic info <topic>` | ROS2 显示消息类型和 QoS 配置 |
| 发布话题 | `rostopic pub <topic>` | `ros2 topic pub <topic>` | ROS2 需指定消息类型（如 `--type`） |
| 监听话题 | `rostopic echo <topic>` | `ros2 topic echo <topic>` | ROS2 支持更多过滤选项（如 `--once`） |


---

**3. 服务与参数**

| 功能 | ROS1 | ROS2 | 说明 |
| --- | --- | --- | --- |
| 列出所有服务 | `rosservice list` | `ros2 service list` | 功能一致 |
| 调用服务 | `rosservice call <service>` | `ros2 service call <service>` | ROS2 需指定请求参数（JSON 格式） |
| 查看参数 | `rosparam list` | `ros2 param list` | ROS2 参数存储在节点内部 |
| 设置参数 | `rosparam set <param>` | `ros2 param set <node> <param>` | ROS2 需指定节点名称 |


---

**4. 消息与接口**

| 功能 | ROS1 | ROS2 | 说明 |
| --- | --- | --- | --- |
| 查看消息定义 | `rosmsg show <msg>` | `ros2 interface show <msg>` | ROS2 合并消息/服务/动作接口 |
| 列出所有消息 | `rosmsg list` | `ros2 interface list` | ROS2 包含 `msg`, `srv`, `action` |


---

**5. 其他关键变化**

1. 命名空间与节点名称:  
• ROS1: 通过 `__ns` 和 `__name` 参数设置。• ROS2: 使用结构化命名（如 `/robot1/camera_node`）。
2. QoS 配置:  
• ROS2 引入了 QoS（服务质量）配置，可通过命令行或代码控制通信可靠性。
3. 生命周期节点:  
• ROS2 支持生命周期管理（`lifecycle` 节点），需通过 `ros2 lifecycle` 命令控制状态。
4. 去中心化架构:  
• ROS2 无需 `roscore`，节点通过 DDS 中间件直接通信。



---

**6. 常用命令对比示例**

```bash
# 运行节点
ROS1: rosrun turtlesim turtlesim_node
ROS2: ros2 run turtlesim turtlesim_node

# 查看运行中的节点
ROS1: rosnode list
ROS2: ros2 node list

# 发布话题数据
ROS1: rostopic pub /turtle1/cmd_vel geometry_msgs/Twist ...
ROS2: ros2 topic pub /turtle1/cmd_vel geometry_msgs/msg/Twist ...
```

---

**总结**  
• 语法相似，细节不同：ROS2 延续了 ROS1 的命令风格，但参数和输出更结构化。

• 去中心化与 QoS：ROS2 强调分布式通信和可靠性控制。

• 统一接口管理：`ros2 interface` 合并了消息、服务和动作的定义。



建议结合 `ros2 --help` 和官方文档（如 `ros2cli`）深入理解新特性。


