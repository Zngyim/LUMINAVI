# LUMINAVI
**This repos is used to record the process of robotic project _Luminavi**

## 使用Docker配置容器

使用Docker创建ubuntu18.04+ros Melodic容器，[Docker教程](https://github.com/Zngyim/Docker_learning)

**注意要将本地机器上的用户或进程连接到X server**<br>
X server管理了Linux系统中的图形输出，接收并处理来自键盘、鼠标等输入设备的事件。

`sudo xhost +local:`是将任何本地用户都可以连接到X server。**通常是为了允许Docker容器内的进程显示图形界面**


#### 配置/.bashrc文件

> /.bashrc是一个系统文件，这个系统文件在系统启动的时候会被自动运行。需要在容器的/.bashrc文件中加入ros的启动步骤，以确保在启动容器的时候可以自动后台启动ros进程（大概是这么个原理）

```
sudo apt-get install nano #nano一个文本编辑软件。

nano ~/.bashrc #即可开始编辑.bashrc文件

nano的命令主要就是两个，首先是CTRL+O保存文件，然后是CTRL+X退出文本编辑器。

##在.bashrc文件的最后加上
source /opt/ros/melodic/setup.bash
即可在终端启动时配置ros相关的环境变量
```

#### 安装可分多个窗口的终端
```
sudo apt install terminator
```

##### terminator快捷键
```
###在同一标签内的操作
Alt+Up                          //移动到上面的终端
Alt+Down                        //移动到下面的终端
Alt+Left                        //移动到左边的终端
Alt+Right                       //移动到右边的终端
Ctrl+Shift+O                    //水平分割终端
Ctrl+Shift+E                    //垂直分割终端
Ctrl+Shift+Right                //在垂直分割的终端中将分割条向右移动
Ctrl+Shift+Left                 //在垂直分割的终端中将分割条向左移动
Ctrl+Shift+Up                   //在水平分割的终端中将分割条向上移动
Ctrl+Shift+Down                 //在水平分割的终端中将分割条向下移动
Ctrl+Shift+S                    //隐藏/显示滚动条
Ctrl+Shift+F                    //搜索
Ctrl+Shift+C                    //复制选中的内容到剪贴板
Ctrl+Shift+V                    //粘贴剪贴板的内容到此处
Ctrl+Shift+W                    //关闭当前终端
Ctrl+Shift+Q                    //退出当前窗口，当前窗口的所有终端都将被关闭
Ctrl+Shift+X                    //最大化显示当前终端
Ctrl+Shift+Z                    //最大化显示当前终端并使字体放大
Ctrl+Shift+N or Ctrl+Tab        //移动到下一个终端
Ctrl+Shift+P or Ctrl+Shift+Tab  //Crtl+Shift+Tab 移动到之前的一个终端

###在各个标签之间的操作
F11                             //全屏开关
Ctrl+Shift+T                    //打开一个新的标签
Ctrl+PageDown                   //移动到下一个标签
Ctrl+PageUp                     //移动到上一个标签
Ctrl+Shift+PageDown             //将当前标签与其后一个标签交换位置
Ctrl+Shift+PageUp               //将当前标签与其前一个标签交换位置
Ctrl+Plus (+)                   //增大字体
Ctrl+Minus (-)                  //减小字体
Ctrl+Zero (0)                   //恢复字体到原始大小
Ctrl+Shift+R                    //重置终端状态
Ctrl+Shift+G                    //重置终端状态并clear屏幕
Super+g                         //绑定所有的终端，以便向一个输入能够输入到所有的终端
Super+Shift+G                   //解除绑定
Super+t                         //绑定当前标签的所有终端，向一个终端输入的内容会自动输入到其他终端
Super+Shift+T                   //解除绑定
Ctrl+Shift+I                    //打开一个窗口，新窗口与原来的窗口使用同一个进程
Super+i                         //打开一个新窗口，新窗口与原来的窗口使用不同的进程
```

####    
1. VScode 1.74.0之后的版本不支持ubuntu18.04,因此考虑把[VScode](https://github.com/microsoft/vscode/releases?page=9)退回到[之前的版本](https://blog.csdn.net/Kelly_TWH/article/details/149220563)

2. 现在不考虑把**VScode**退回到之前的版本了，因为发现VScode1.74.0实在是太老了，很多插件都无法使用，几乎不可能在本地上安装这么老版本的VScode并运行Docker。也没必要在Docker里面再去探索VScode的下载方式。**因此使用文件挂载的方式直接在宿主机上编辑容器里必要的文件内容**，有以下几个注意点
> 1. 注意挂载目录，将必要的文件保存在该目录下，在会同步在宿主机中。
> 2. 同步之后都是只读权限，使用`chmod -R 777 <file_name/file_path>`，也就是将文件夹内部的所有文件都递归变为可读写的文件。

3. 积累几个常用命令，便于命令行与VScode协同进行
```
mkdir -p hello/src # -p表示创建多级目录
rm -r <directory># 递归地删除目录及内部的所有文件
```

















