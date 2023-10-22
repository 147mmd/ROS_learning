### 服务器指令
1.  
1. 
2. 
1. 

```bash
sudo apt-get install ros-noetic-

sudo vim /etc/apt/sources.list
sudo apt-get install vim;
sudo apt-get update

```

```
```
* 0sudo apt-get install ros-noetic-

* sudo vim /etc/apt/sources.list
* sudo apt-get install vim;
* sudo apt-get update


$$ f(T) = \int_{-\infty}\$$
GITHUB:
1.cd ~/catkin_ws/src（运行终端程序前）
2.git clone www.xxx.com(下载github的东西)
3.cd ~/catkin_ws
4.catkin_make编译（或在VScode中CTRL+shift+B）
5.source ~/catkin_ws/devel/setup.bash(载入环境设置才能运行程序)(已经自动化于。bashrc）
roslaunch 运行程序
rosrun



ctrl + c结束终端指令

vscode:
1.创建一个软件包，要有依赖项
2.在src下创建一个node.cpp
3.在CMakelists.txt中设置编译规则
写完源代码要重新编译（catkin_make编译（或在VScode中CTRL+shift+B））
自己写的节点在终端运行：运行node之前都需要进行roscore ；rosrun

sudo su（超级用户root）
tree指令


slam：写的cpp需要用cmake.和make进行编译才会有可执行程序
运行cpp可执行程序的指令：在当前文件夹下     ./xxx