
# ROS以及ubuntu学习笔记
## apt下载包
```
sudo apt-get install ros-noetic-

sudo vim /etc/apt/sources.list
sudo apt-get install vim;
sudo apt-get update
```

## 在github中clone
GITHUB:
```
1.cd ~/catkin_ws/src（运行终端程序前）
2.git clone www.xxx.com(下载github的东西)
3.cd ~/catkin_ws
4.catkin_make编译（或在VScode中CTRL+shift+B）
5.source ~/catkin_ws/devel/setup.bash(载入环境设置才能运行程序)(已经自动化于。bashrc）

```

### git下来的包

可以在script文件夹里面运行install_...文件，下载依赖项s
## 运行package
运行一切package时都要先:
```
roscore
roslaunch 运行程序
rosrun ssr_pkg chao_node //运行一个结点
```


ctrl + c结束终端指令
## 如何建立package
vscode:
1. 创建一个软件包，要有依赖项
2. 在src下创建一个node.cpp
3. 在CMakelists.txt中设置编译规则
4. 写完源代码要重新编译（catkin_make编译（或在VScode中CTRL+shift+B））
5. 自己写的节点在终端运行：运行node之前都需要进行roscore ；rosrun


终端上创建软件包：
在~/catkin_ws/src
```
catkin_creat_pkg <包名><依赖项>
catkin_creat_pkg atr_pkg rospy roscpp std_msgs
```
### 创建结点
创建一个结点：

```
#include<ros/ros.h>

int main(int argc, char *argv[])//输入main就可以自动补全
{
    ros::init(argc,argv,"<结点名>")；//初始化ros结点（！！必备）

    ...

    return 0;
}

```
### 创建之后要修改cmakelist文件夹用于编译

在BUILD中复制这三项即可：
add_executable(${PROJECT_NAME}_node src/xxx.cpp)

add_dependencies(${PROJECT_NAME}_node ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

target_link_libraries(${PROJECT_NAME}_node
  ${catkin_LIBRARIES}
 )

只用改第一个${PROJECT_NAME}，executable两项名称一致
```
sudo su（超级用户root）
tree指令
```

slam：写的cpp需要用cmake.和make进行编译才会有可执行程序
运行cpp可执行程序的指令：在当前文件夹下     ./xxx

### message
index.ros.org中查询message类型：搜索std_msgs,，进入noetic版本
### 创建一个话题
创建一个topic,直接修改node即可

```rostopic hz /woshi147  //查看topic：woshi147消息包的频率
rostopic list //当前运行的话题
rostopic echo /xxx（话题名） //查看message的data
```

1. 确定话题名称和消息类型
2. 在代码文件中include消息类型对应的头文件
3. 在main函数中通过NodeHandler大管家发布一个话题并得到消息发送对象。
4. 生成要发送的消息包并进行发送数据的赋值调用消息发送对象的publish()函数将消息包发送到话题当中
#### 示例代码
```
#include <ros/ros.h>
#include<std_msgs/String.h>   //string必备的头文件

int main(int argc, char  *argv[])   //输入main就可以自动补全
{   
    ros::init(argc,argv,"chao_node");  //初始化ros结点（！！必备）
    printf("hello world\n");

    ros::NodeHandle nh;  //固定格式，用于分配给publisher 发送信息的 ”设备“（！！！非常重要
    ros::Publisher pub = nh.advertise<std_msgs::String>("woshi147", 10);           //固定格式 nh.advertise("话题名称"，缓存长度)； 构建消息发送对象，缓存长度一般比0大即可

    ros::Rate loop_rate(10);  //控制每秒发送message的频率 （一秒10条） （！！必备

    while(ros::ok)  //ros::ok 可以用ctrl+c结束循环，如果是while（ture)无法用ctrl+c
    {
        printf("啊？");
        std_msgs::String msg; //创建消息包
        msg.data = "你好";
        pub.publish(msg);     //发送给nodehandle中!!!必备
        loop_rate.sleep(); //用于控制频率（！！必备
    }
    return 0;
} 
```



```
rqt_graph：可以显示当前正在运行的node和之间的话题通讯关系（执行前需要roscore）
```

### 话题的订阅

1. 确定话题名称和消息类型
2. 在代码文件中include <ros.h> 和消息类型对应的头文件
3. 在main函数中通过NodeHandler大管家订阅一个话题并设置消息接收回调函数。
4. 定义一个回调函数，对接收到的消息包进行处理
5. main函数中需要执行ros::spinOnce()，让回调函数能够响应接收到的消息包。


#### 示例代码
```
#include<ros/ros.h>
#include<std_msgs/String.h>

void chao_callback(std_msgs::String msg)    //回调函数：订阅者对消息包到来的一个自动相应，传进来的参数是最新获取的消息包
{
    //printf(msg.data)   会报错，printf需要char *，但msg.data为string
  //  printf(msg.data.c_str());  //打印收到的消息包
   // printf("\n");

   ROS_INFO(msg.data.c_str());//用于观测收到message的数量，检查是否在收包，可通过数据每秒算出发送的数量，但需要提前配置中文环境，不然会出现“？？？”
}

void h_callback (std_msgs::String msg)
{

    ROS_WARN (msg.data.c_str()); //生成黄色的反馈INFO为白色

}
int main(int argc, char *argv[])
{
    setlocale(LC_ALL,"");       //中文ubuntu用“”即可，英文的需要:“zh_CN.UTF-8”                                                                                                                                                                                               
    ros::init(argc,argv,"ma_node");
    ros::NodeHandle nh;
    ros::Subscriber sub = nh.subscribe("woshi147", 10, chao_callback);   //格式：ros::Subscriber sub = nh.subscribe("话题名",缓存长度：由发送方频率和接收方处理速度共同决定,回调函数)；
    ros::Subscriber sub2 = nh.subscribe("woshiTAKIII", 10, h_callback);

    while (ros::ok)
    {
       ros::spinOnce();      //用于检查有没有接收到消息包message（！！！必备

    }
    

    return 0;
}
```
只运行订阅者，虽然收不到消息包，但ros中仍活跃与之相关的话题

话题并不属于订阅者与发布者，由ros系统管理

### 利用launch一次启动多个节点
#### xml语言：
功能：描述一组多层嵌套的数据结构 

格式：`<标记名称 属性名1=“属性值1”...>内容 </标记名称>`

```
<大纸盒>
<小纸盒 颜色=“黄色>        </小纸盒>
<(标记名称开头)(属性)>(内容)</(标记名称结尾)>
</大纸盒>
```
或者：
```
<大纸盒>
<小纸盒 颜色=“黄色”/>
<又一个小纸盒 颜色=“黄色”/>
...
</大纸盒>
```
e.g.
```
<阵容>
<选手名字=“诺言” 英雄=“花木兰” 职业=“战士”/>
<选手名字=“花海” 英雄=“孙尚香” 职业=“射手”/>
<选手名字=“Cat” 英雄=“安琪拉” 职业=“法师”/>
<选手名字=“Alan” 英雄=“兰陵王” 职业=“刺客”/>
<选手名字=“无铭” 英雄=“钟馗” 职业=“法师”/>
</阵容>
```
#### launch
利用launch文件同时启动多个节点，launch可以放在任何地方，因为他是逐层遍历的

```
roslaunch <包名> <launch文件>
```

#### 建立一个launch文件：
```
<launch>

  <node pkg = "ssr_pkg" type = "chao_node" name = "chao_node" launch-prefix = "gnome-terminal -e"/>

  <node pkg = "ssr_pkg" type = "wo2_node" name = "wo2_node" launch-prefix = "gnome-terminal -e"/>    <!--launch-prefix = "gnome-terminal -e" 在新的终端中打开该节点-->

  <node pkg = "atr_pkg" type = "ma_node" name = "ma_node" output="screen"/>      <!-- 没有output的话只会输出WARN的黄色字，一般输出都要OUTPUT -->

</launch>
```

## 对机器人的控制
### 机器人运动学
对机器人的控制可以分为矢量运动和旋转运动，这两个都有x,y,z三个变量

### 机器人的控制话题
一般使用cmd_vel做话题名，实现对机器人的控制

### 创建机器人控制的发布者
`catkin_creat_pkg vel_pkg rospy roscpp geometry_msgs`

其中geometry_msgs为机器人通讯的消息格式

### 示例代码 
```
#include <ros/ros.h>
#include <geometry_msgs/Twist.h>//Twist格式需要查阅官网，Twist为表示速度的message，包括矢量速度与旋转速度

int main(int argc, char *argv[])
{
    ros::init(argc ,argv,"vel_node");

    ros::NodeHandle n;
    ros::Publisher vel_pub = n.advertise<geometry_msgs::Twist>("/cmd_vel",10); //话题名称为“/cmd_vel”约定俗成，所以启动此节点可以直接控制机器人

    geometry_msgs::Twist vel_msg; //Twist为表示速度的message，包括矢量速度与旋转速度
    vel_msg.linear.x = 0.5;     //vel_msg.linear.x为矢量速度，分为xyz轴
    vel_msg.linear.y = 0; 
    vel_msg.linear.z = 0;
    vel_msg.angular.x = 0;
    vel_msg.angular.y = 0;
    vel_msg.angular.z = 0;      //vel_msg.angular.x为矢量速度，分为xyz轴

    ros::Rate r(30);

    while(ros::ok)
    {
        vel_pub.publish(vel_msg);
        r.sleep();
    }
    return 0;
}

```