#### 工作空间，功能包，进程（Nodes，节点）
进程某是某个动作，例如摄像头启动，激光雷达启动，都会对应一个节点

ros是进程的分布式框架，ros中的进程可以分布于不同的主机，不同的主机协同工作来减轻计算压力

功能包就是实现某个进程的功能所编写的源代码等文件，

通过CMakeLists配置实现节点

而工作空间则是更大一层，包含各种功能包。

**ros1的节点的各种配置都是通过roscore来执行的，所以一定要先启动ros核心，再运行功能包中的节点**

## 使用ros实现功能


1. 创建工作空间（包含src子目录，然后使用catkin_make初始化）
2. 创建功能包（进入src，`catkin_create_pkg <pkg_name> roscpp rospy std_msgs`,后面三个是实现功能的相关依赖/库）
3. 进入功能包，在功能包的src目录底下编写实现功能所需的cpp/py文件
4. 修改功能包下的`CMakeLists.txt`(以cpp文件为例)
```
add_executable(节点名  #节点名称是自定义的，一个功能包就需要一个节点
  src/步骤3的源文件名.cpp
)
target_link_libraries(节点名
  ${catkin_LIBRARIES}
)
```
5. 进入工作目录并编译，
```
catkin_make #编译


source ./devel/setup.bash	# 加载工作空间的环境变量到当前终端，
				# 每个终端都需要运行一遍，或者也可以加入到~/.bashrc文件里

```
6. 调用功能包命令，`rosrun <pkg_name> <Nodes_name>`

### 编辑ros文件实现一次性启动多个节点

**可以使用launch文件**

### 话题订阅
1. 发布方代码实现
```cpp
/*
    需求: 实现基本的话题通信，一方发布数据，一方接收数据，
         实现的关键点:
         1.发送方
         2.接收方
         3.数据(此处为普通文本)

         PS: 二者需要设置相同的话题


    消息发布方:
        循环发布信息:HelloWorld 后缀数字编号

    实现流程:
        1.包含头文件 
        2.初始化 ROS 节点:命名(唯一)
        3.实例化 ROS 句柄
        4.实例化 发布者 对象
        5.组织被发布的数据，并编写逻辑发布数据

*/
// 1.包含头文件 
#include "ros/ros.h"
#include "std_msgs/String.h" //普通文本类型的消息
#include <sstream>

int main(int argc, char  *argv[])
{   
    //设置编码
    setlocale(LC_ALL,"");//中文

    //2.初始化 ROS 节点:命名(唯一)
    // 参数1和参数2 后期为节点传值会使用
    // 参数3 是节点名称，是一个标识符，需要保证运行后，在 ROS 网络拓扑中唯一
    ros::init(argc,argv,"talker");
    //3.实例化 ROS 句柄
    ros::NodeHandle nh;//该类封装了 ROS 中的一些常用功能
                       //用于控制ros的一些通信功能，包括定义发布者，创建订阅者等

    //4.实例化 发布者 对象
    //泛型: 发布的消息类型
    //参数1: 要发布到的话题
    //参数2: 队列中最大保存的消息数，超出此阀值时，先进的先销毁(时间早的先销毁)
    //advertise是Publisher类中的一个函数，用于声明发布的话题名称和队列数量
    ros::Publisher pub = nh.advertise<std_msgs::String>("chatter",10);

    //5.组织被发布的数据，并编写逻辑发布数据
    //数据(动态组织)
    std_msgs::String msg;
    // msg.data = "你好啊！！！";
    std::string msg_front = "Hello 你好！"; //消息前缀，定义了一个字符串类型
    int count = 0; //消息计数器

    //逻辑(一秒10次)
    ros::Rate r(1);

    //节点不死
    while (ros::ok())
    {
        //使用 stringstream 拼接字符串与编号
        std::stringstream ss;
        ss << msg_front << count;
        msg.data = ss.str();
        //发布消息
        pub.publish(msg);//把msg的内容发布到chatter的话题上，供Listener订阅
        //加入调试，打印发送的消息
        ROS_INFO("发送的消息:%s",msg.data.c_str());

        //根据前面制定的发送贫频率自动休眠 休眠时间 = 1/频率；
        r.sleep();
        count++;//循环结束前，让 count 自增
        //暂无应用
        ros::spinOnce();
    }


    return 0;
}

```
2. 订阅方代码实现
```cpp
/*
    需求: 实现基本的话题通信，一方发布数据，一方接收数据，
         实现的关键点:
         1.发送方
         2.接收方
         3.数据(此处为普通文本)


    消息订阅方:
        订阅话题并打印接收到的消息

    实现流程:
        1.包含头文件 
        2.初始化 ROS 节点:命名(唯一)
        3.实例化 ROS 句柄
        4.实例化 订阅者 对象
        5.处理订阅的消息(回调函数)
        6.设置循环调用回调函数

*/
// 1.包含头文件 
#include "ros/ros.h"
#include "std_msgs/String.h"

//定义回调函数，当订阅方接收到消息时会自动调用该函数
void doMsg(const std_msgs::String::ConstPtr& msg_p){
          //订阅std_msgs::String类型的消息时，必须使用该输入参数
          //必须与订阅者的接收消息类型相同，否则就会报错
    ROS_INFO("我听见:%s",msg_p->data.c_str());
    // ROS_INFO("我听见:%s",(*msg_p).data.c_str());
}
int main(int argc, char  *argv[])
{
    setlocale(LC_ALL,"");
    //2.初始化 ROS 节点:命名(唯一)
    ros::init(argc,argv,"listener");
    //3.实例化 ROS 句柄
    ros::NodeHandle nh;

    //4.实例化 订阅者 对象
    ros::Subscriber sub = nh.subscribe<std_msgs::String>("chatter",10,doMsg);
    //doMsg是callback function，输入参数自动就是订阅者接受的消息类型
    //5.处理订阅的消息(回调函数)

    //     6.设置循环调用回调函数
    ros::spin();//循环读取接收的数据，并调用回调函数处理

    return 0;
}
```








