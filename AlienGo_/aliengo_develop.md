# 宇树机器狗开发记录

## 个人PC与机器狗连接
> **客户PC通过网线或WiFi连接机器狗后，需要先ping一下相应主板的IP，确保网络畅通。高层控制（HighLevel）需要ping通运动控制板卡，底层控制（LowLevel）需要ping通主控板。**
主控板：MCU（192.168.123.10）
运动控制主板：Mini PC（192.168.123.220）
感知主板：TX2（192.168.123.12）


网线或无线网卡连接go1，需要设定用户ip地址为192.168.123.XXX
1. 网线连接狗（狗开机）
2. 将本机地址设置为与机器狗同一网段，如192.168.123.222
3. 测试连接是否成功。终端输入：ping 192.168.123.161/13
4. 查看123网段对应的网卡名字。终端输入：ifconfig


## ROS
### 1. 编译使用unitree_ros及unitree_ros_to_real

如果只是想通过ros控制机器狗，仅需将GitHub上**unitree_ros_to_real**包放到工作空间的/src目录下，并按readme里配置环境及编译。

如果只想在个人PC上仿真，仅需要将**unitree_ros**包及unitree_ros_to_real包里的**unitree_legged_msgs**一起放到工作空间的/src目录下，并按readme里配置环境及编译。

如果在个人PC上仿真及通过ros控制机器狗，需要将unitree_ros包及unitree_ros_to_real包一起放到工作空间的/src目录下，并按readme里配置环境及编译。

下面本文的工作空间以~/catkin_ws为例，介绍前两种情况，第三种情况不再赘述。

### LowLevel与HighLevel控制

- lowlevel是对单个关节或电机的控制，强烈建议把机器狗挂起来悬空后再进行测试，否则容易让机器狗摔倒损坏
- highlevel是对机器人整体行为的控制，适合在地面上进行测试

