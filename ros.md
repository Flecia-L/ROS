# Flecia's ROS notes  

本文参考官网[ROS教程](http://wiki.ros.org/cn/ROS/Tutorials#A.2BUh1.2Bp2VZegs-)。

## 一、安装和配置ROS环境  

本教程将指导您在计算机上安装ROS和配置ROS环境。  

### 1. 安装ROS

首先需要进行[ROS安装步骤](http://wiki.ros.org/cn/ROS/Installation)，根据指示选择相应版本进行安装，以下以Unbuntu20.04上安装ROS Noetic为参考。

1. 添加ROS软件源
`$ sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'`
2. 添加密钥
`$ sudo apt install curl # if you haven't already installed curl`
`$ curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | sudo apt-key add -`
3. 安装ROS
`$ sudo apt update`
`$ sudo apt install ros-melodic-desktop-full`
4. 设置环境变量
`$ echo "source /opt/ros/noetic/setup.bash" >> ~/.bashrc`
`$ source ~/.bashrc`
5. 安装rosinstall
`$ sudo apt install python3-rosdep python3-rosinstall python3-rosinstall-generator python3-wstool build-essential`
6. 初始化rosdep
`$ sudo rosdep init`
`$ rosdep update`

### 2. 管理环境  

在安装ROS期间，你会看到提示说需要source多个setup.*sh文件中的某一个，或者添加这条命令到你的shell启动脚本里面。这些操作是必须的，因为ROS依赖于使用shell环境组合空间的概念，这使得针对不同版本的ROS或不同的软件包集开发变得更加容易。

如果你在查找和使用ROS软件包方面遇到了问题，请确保已经正确配置了环境。有个好办法可以检查，确保ROS_ROOT和ROS_PACKAGE_PATH这两个环境变量正确设置：

`$ printenv | grep ROS`

如果没有的话，这个时候你就需要重新source一下了。  

### 3. 创建ROS工作空间

下面我们开始创建和构建一个catkin工作空间：

`$ mkdir -p ~/catkin_ws/src`
`$ cd ~/catkin_ws/`
`$ catkin_make`

在你的工作空间中第一次运行catkin_make命令时，它会在src目录下创建一个CMakeLists.txt的链接。当前目录还能看到build和devel这两个目录。在devel文件夹里面你可以看到几个setup.\*sh文件。接下来首先source一下新生成的setup.\*sh文件：

`$ source devel/setup.bash`

要保证工作区被安装脚本正确覆盖，需确定ROS_PACKAGE_PATH环境变量包含你当前的工作空间目录：

    $ echo $ROS_PACKAGE_PATH
    /home/<username>/catkin_ws/src:/opt/ros/<distro>/share

至此，环境已经搭建完成，接下来可以继续学习 ROS文件系统导览。

## 二、ROS文件系统导览

本教程介绍ROS文件系统的概念，包括如何使用roscd、rosls和rospack命令行工具。

### 1. 概念

文件系统概念简介

* 软件包（Packages）：包是ROS代码的软件组织单元，每个软件包都可以包含程序库、可执行文件、脚本或其他构件。

* Manifests (package.xml)： 清单（Manifest）是对软件包的描述。它用于定义软件包之间的依赖关系，并记录有关软件包的元信息，如版本、维护者、许可证等。

文件系统工具

程序代码散落在许多ROS包中。使用Linux内置命令行工具（如ls和cd）来进行查找和导航可能非常繁琐，因此ROS提供了专门的命令工具来简化这些操作。

### 2. 使用

* 使用rospack

rospack允许你获取软件包的有关信息。在本教程中，我们只涉及到find参数选项，该选项可以返回软件包的所在路径。

用法：

`$ rospack find [package_name]`

比如：

`$ rospack find roscpp`

将会输出：

    YOUR_INSTALL_PATH/share/roscpp

如果你是在Ubuntu操作系统上通过apt安装的ROS，你看到的应该是：

    /opt/ros/<distro>/share/roscpp

* 使用roscd

roscd是rosbash命令集的一部分，它允许你直接切换目录（cd）到某个软件包或者软件包集当中。

用法：

`$ roscd [locationname[/subdir]]`

要想验证是否能切换到roscpp包的位置，请运行以下示例：

`$ roscd roscpp`

现在让我们使用Unix命令pwd输出工作目录：

`$ pwd`

你应该会看到：

    YOUR_INSTALL_PATH/share/roscpp

roscd也可以切换到一个软件包或软件包集的子目录中。

执行：

`$ roscd roscpp/cmake`
`$ pwd`

应该会看到：

    YOUR_INSTALL_PATH/share/roscpp/cmake

roscd log将带您进入存储ROS日志文件的目录。需要注意的是，如果你没有执行过任何ROS程序，系统会报错说该目录不存在。

如果你已经运行过ROS程序，那么可以尝试：

    roscd log

* 使用 rosls

rosls 是rosbash命令集的一部分，它允许你直接按软件包的名称执行 ls 命令（而不必输入绝对路径）。

用法：

`$ rosls [locationname[/subdir]]`

示例：

`$ rosls roscpp_tutorials`

应输出：

    cmake launch package.xml  srv

* Tab补全

总是输入完整的软件包名称感觉比较繁琐。在之前的例子中，roscpp tutorials是个相当长的名称。幸运的是，一些ROS工具支持TAB补全的功能。

试着开始输入：

`$ roscd roscpp_tut<<<按TAB键>>>`

当按TAB键后，命令行应该会自动补充剩余部分：

`$ oscd roscpp_tutorials/`

如果要查看当前安装的所有软件包的列表，你也可以利用TAB补全：

`$ rosls <<<双击TAB键>>>`

### 3. 复习

你也许已经注意到了ROS命令工具的的命名方式：

    rospack = ros + pack(age)
    roscd = ros + cd
    rosls = ros + ls 

这种命名模式在许多ROS命令工具中都会用到。

到此你已经了解了ROS的文件系统结构，接下来开始创建ROS软件包吧。

## 三、创建ROS软件包

本教程介绍如何使用roscreate-pkg或catkin创建新的ROS软件包，并使用rospack列出软件包的依赖关系。

### 1. 一个catkin软件包由什么组成?

一个包要想称为catkin软件包，必须符合以下要求：

* 这个包必须有一个符合catkin规范的package.xml文件

  * 这个package.xml文件提供有关该软件包的元信息

* 这个包必须有一个catkin版本的CMakeLists.txt文件

  * 如果它是个Catkin元包的话，则需要有一个CMakeList.txt文件的相关样板
* 每个包必须有自己的目录
  * 这意味着在同一个目录下不能有嵌套的或者多个软件包存在

最简单的软件包看起来就像这样(在workspace_folder/src/下)：

    my_package/
      CMakeLists.txt
      package.xml

### 2. catkin工作空间中的软件包

开发catkin软件包的推荐方法是使用catkin工作空间，但是你也可以单独开发catkin软件包。一个简单的工作空间如下所示：

    workspace_folder/        -- WORKSPACE
      src/                   -- SOURCE SPACE
        CMakeLists.txt       -- 'Toplevel' CMake file, provided by catkin
        package_1/
          CMakeLists.txt     -- CMakeLists.txt file for package_1
          package.xml        -- Package manifest for package_1
        ...
        package_n/
          CMakeLists.txt     -- CMakeLists.txt file for package_n
          package.xml        -- Package manifest for package_n

### 3. 创建catkin软件包

本部分教程将演示如何使用catkin_create_pkg脚本来创建一个新的catkin软件包，以及创建之后都能做些什么。

首先切换到刚才创建的空白catkin工作空间中的源文件空间目录：

`$ cd ~/catkin_ws/src`

现在使用catkin_create_pkg命令创建一个名为beginner_tutorials的新软件包，这个软件包依赖于std_msgs、roscpp和rospy：

`$ catkin_create_pkg beginner_tutorials std_msgs rospy roscpp`

catkin_create_pkg命令会要求你输入package_name，如有需要还可以在后面添加一些需要依赖的其它软件包：

    # This is an example, do not try to run this

    $ catkin_create_pkg <package_name> [depend1] [depend2] [depend3]

catkin_create_pkg命令也有更多的高级功能，这些功能在catkin/commands/catkin_create_pkg中有描述。

### 4. 构建一个catkin工作区并生效配置文件

现在你需要在catkin工作区中构建软件包：

`$ cd ~/catkin_ws`
`$ catkin_make`

工作空间构建完成后，在devel子目录下创建了一个与你通常在/opt/ros/$ROSDISTRO_NAME下看到的目录结构类似的结构。

要将这个工作空间添加到ROS环境中，你需要source一下生成的配置文件：

`$ . ~/catkin_ws/devel/setup.bash`

### 5. 软件包依赖关系

#### 5.1 一级依赖

之前在使用catkin_create_pkg命令时提供了几个软件包作为依赖关系，现在我们可以使用rospack命令工具来查看这些一级依赖包。

`$ rospack depends1 beginner_tutorials`

    std_msgs
    rospy
    roscpp

如你所见，rospack列出了在运行catkin_create_pkg命令时作为参数的依赖包，这些依赖关系存储在package.xml文件中。

#### 5.2 间接依赖

在很多情况下，一个依赖包还会有它自己的依赖关系，比如，rospy就有其它依赖包。

`$ rospack depends1 rospy`

    genpy
    roscpp
    rosgraph
    rosgraph_msgs
    roslib
    std_msgs

一个软件包可以有相当多间接的依赖关系，好在使用rospack可以递归检测出所有嵌套的依赖包。

`$ rospack depends beginner_tutorials`

    cpp_common
    rostime
    roscpp_traits
    roscpp_serialization
    catkin
    genmsg
    genpy
    message_runtime
    gencpp
    geneus
    gennodejs
    genlisp
    message_generation
    rosbuild
    rosconsole
    std_msgs
    rosgraph_msgs
    xmlrpcpp
    roscpp
    rosgraph
    ros_environment
    rospack
    roslib
    rospy

### 6. 自定义你的软件包

本部分教程将剖析catkin_create_pkg命令生成的每个文件，并详细描述这些文件的组成部分，以及如何自定义这些文件。

#### 6.1 自定义package.xml

自动生成的package.xml文件应该在你的新软件包中。现在让我们一起来看看新生成的package.xml文件以及每一个需要你关注的元素。

##### 6.1.1 描述标签

    5   <description>The beginner_tutorials package</description>

##### 6.1.2 维护者标签

接下来是maintainer标签：

    7   <!-- One maintainer tag required, multiple allowed, one person per tag --> 
    8   <!-- Example:  -->
    9   <!-- <maintainer email="jane.doe@example.com">Jane Doe</maintainer> -->
    10   <maintainer email="user@todo.todo">user</maintainer>

##### 6.1.3 许可证标签

    12   <!-- One license tag required, multiple allowed, one license per tag -->
    13   <!-- Commonly used license strings: -->
    14   <!--   BSD, MIT, Boost Software License, GPLv2, GPLv3, LGPLv2.1, LGPLv3 -->
    15   <license>TODO</license>

对于本教程，我们使用BSD许可证，因为其他核心ROS组件已经在使用它：

    8   <license>BSD</license>

##### 6.1.4 依赖项标签

除了catkin中默认提供的buildtool_depend，所有我们列出的依赖包都已经被添加到build_depend标签中。在本例中，因为在编译和运行时都需要用到所有指定的依赖包，因此还要将每一个依赖包分别添加到run_depend标签中：

    12   <buildtool_depend>catkin</buildtool_depend>
    13 
    14   <build_depend>roscpp</build_depend>
    15   <build_depend>rospy</build_depend>
    16   <build_depend>std_msgs</build_depend>
    17 
    18   <exec_depend>roscpp</exec_depend>
    19   <exec_depend>rospy</exec_depend>
    20   <exec_depend>std_msgs</exec_depend>

##### 6.1.5 最终的package.xml

现在看下面最后去掉了注释和未使用标签后的package.xml文件就显得更加简洁了：

    1 <?xml version="1.0"?>
    2 <package format="2">
    3   <name>beginner_tutorials</name>
    4   <version>0.1.0</version>
    5   <description>The beginner_tutorials package</description>
    6 
    7   <maintainer email="you@yourdomain.tld">Your Name</maintainer>
    8   <license>BSD</license>
    9   <url type="website">http://wiki.ros.org/beginner_tutorials</url>
    10   <author email="you@yourdomain.tld">Jane Doe</author>
    11 
    12   <buildtool_depend>catkin</buildtool_depend>
    13 
    14   <build_depend>roscpp</build_depend>
    15   <build_depend>rospy</build_depend>
    16   <build_depend>std_msgs</build_depend>
    17 
    18   <exec_depend>roscpp</exec_depend>
    19   <exec_depend>rospy</exec_depend>
    20   <exec_depend>std_msgs</exec_depend>
    21 
    22 </package>

#### 6.2 自定义CMakeLists.txt

到此，这个包含软件包元信息的package.xml文件已经按照需要完成了裁剪整理，现在你可以继续下面的教程了。catkin_create_pkg命令生成的CMakeLists.txt文件将在后续关于构建ROS程序代码的教程中讲述。

现在你已经创建了一个新的ROS软件包，接下来开始编译ROS软件包吧。

## 四、构建ROS软件包

本教程介绍了构建软件包及使用的工具链。

首先记得确认激活环境配置，在工作区下输入：

`$ source devel/setup.bash`

前面已经用过，使用 catkin_make 可以同时编译多个包：

    # In a catkin workspace
    $ catkin_make [make_targets] [-DCMAKE_VARIABLES=...]

这会自动编译 src 目录下的包。如果包名是自定义的，比如说 my_src，那么应该这样做：

    # In a catkin workspace
    $ catkin_make --source my_src

之前修改了 package.xml，这里在工作区内输入：

`$ catkin_make`

输出：

    Base path: /workspace/catkin_ws
    Source space: /workspace/catkin_ws/src
    Build space: /workspace/catkin_ws/build
    Devel space: /workspace/catkin_ws/devel
    Install space: /workspace/catkin_ws/install
    ####
    #### Running command: "make cmake_check_build_system" in "/workspace/catkin_ws/build"
    ####
    -- Using CATKIN_DEVEL_PREFIX: /workspace/catkin_ws/devel
    -- Using CMAKE_PREFIX_PATH: /workspace/catkin_ws/devel;/opt/ros/melodic
    -- This workspace overlays: /workspace/catkin_ws/devel;/opt/ros/melodic
    -- Found PythonInterp: /usr/bin/python2 (found suitable version "2.7.15", minimum required is "2") 
    -- Using PYTHON_EXECUTABLE: /usr/bin/python2
    -- Using Debian Python package layout
    -- Using empy: /usr/bin/empy
    -- Using CATKIN_ENABLE_TESTING: ON
    -- Call enable_testing()
    -- Using CATKIN_TEST_RESULTS_DIR: /workspace/catkin_ws/build/test_results
    -- Found gmock sources under '/usr/src/googletest': gmock will be built
    -- Found PythonInterp: /usr/bin/python2 (found version "2.7.15") 
    -- Found gtest sources under '/usr/src/googletest': gtests will be built
    -- Using Python nosetests: /usr/bin/nosetests-2.7
    -- catkin 0.7.17
    -- BUILD_SHARED_LIBS is on
    -- BUILD_SHARED_LIBS is on
    -- ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    -- ~~  traversing 1 packages in topological order:
    -- ~~  - beginner_tutorials
    -- ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    -- +++ processing catkin package: 'beginner_tutorials'
    -- ==> add_subdirectory(beginner_tutorials)
    -- Configuring done
    -- Generating done
    -- Build files have been written to: /workspace/catkin_ws/build
    ####
    #### Running command: "make -j2 -l2" in "/workspace/catkin_ws/build"
    ####

现在我们已成功构建了一个ROS软件包，让我们再聊聊理解ROS节点。

## 五、理解ROS节点

本教程介绍了ROS图的概念，并探讨了roscore、rosnode和rosrun命令行工具的使用。

### 1. 预备工作

本教程中我们将会用到一个轻量级模拟器。如果你之前安装的ROS不是完整桌面版（Desktop-Full），请先：

`$ sudo apt-get install ros-<distro>-ros-tutorials`

### 2. 概念速览

* 节点（Nodes）：节点是一个可执行文件，它可以通过ROS来与其他节点进行通信。

* 消息（Messages）：订阅或发布话题时所使用的ROS数据类型。

* 话题（Topics）：节点可以将消息发布到话题，或通过订阅话题来接收消息。

* 主节点（Master）：ROS的命名服务，例如帮助节点发现彼此。

* rosout：在ROS中相当于stdout/stderr（标准输出/标准错误）。

* roscore：主节点 + rosout + 参数服务器（会在以后介绍）。

### 3. 节点

* 节点（node）就是 ROS 包中的可执行文件。

* ROS 节点使用 ROS 客户端库来与各节点进行通信。
  
* 节点可以发布或者订阅话题。

* 节点也可以提供或者使用服务。

### 4. 客户端库

ROS客户端库可以让用不同编程语言编写的节点进行相互通信：

* rospy = Python客户端库

* roscpp = C++客户端库

### 5. 使用roscore

roscore是你在运行所有ROS程序前首先要运行的命令。

`$ roscore`

### 6. 使用rosnode

在运行 roscore 后，另外打开一个终端，输入：

`$ rosnode list`

输出：

    /rosout

这说明当前只有一个 rosout 节点在运行，该节点总是在运行，为了收集记录节点的输出日志。

查看某节点的信息：

`$ rosnode info /rosout`

### 7. 使用rosrun

rosrun 用于直接运行包中的节点：

`$ rosrun [package_name] [node_name]`

打开一个新的终端，输入：

`$ rosrun turtlesim turtlesim_node`

在一个新终端中：

`$ rosnode list`

你会看到类似下面的输出信息：

    /rosout
    /turtlesim

ROS有一个强大的功能，就是你可以通过命令行重新分配名称。

这一次使用重映射参数来改变节点名称：

`$ rosrun turtlesim turtlesim_node __name:=my_turtle`

现在，如果我们回去使用rosnode list：

`$ rosnode list`

你会看到类似下面的输出信息：

    /rosout
    /my_turtle

我们可以看到新的/my_turtle节点。使用另外一个rosnode指令，ping，来测试它是否正常：

    $ rosnode ping my_turtle

    rosnode: node is [/my_turtle]
    pinging /my_turtle with a timeout of 3.0s
    xmlrpc reply from http://aqy:42235/     time=1.152992ms
    xmlrpc reply from http://aqy:42235/     time=1.120090ms
    xmlrpc reply from http://aqy:42235/     time=1.700878ms
    xmlrpc reply from http://aqy:42235/     time=1.127958ms

### 8. 复习

本节所涉及的内容：

* roscore = ros+core：主节点（为ROS提供命名服务) + rosout (stdout/stderr) + 参数服务器（会在以后介绍）
* rosnode = ros+node：获取节点信息的ROS工具
* rosrun = ros+run：运行给定的软件包中的节点

到这里，您已经了解ROS节点是如何工作的，下一步，我们来看看理解ROS话题。如果想关闭turtlesim_node，可以随时按下<kbd>Ctrl</kbd>+<kbd>C</kbd>。

## 六、理解ROS话题

本教程介绍了ROS话题，以及如何使用rostopic和rqt_plot命令行工具。

### 1.开始

#### 1.1 roscore

首先确保roscore正在运行, 打开一个新终端：

`$ roscore`

#### 1.2 turtlesim

在本教程中我们也会使用到turtlesim，打开一个新终端：

`$ rosrun turtlesim turtlesim_node`

#### 1.3 通过键盘遥控turtle

我们还需要通过键盘来控制turtle，打开一个新终端：

`$ rosrun turtlesim turtle_teleop_key`

### 2. ROS话题

turtlesim_node节点和turtle_teleop_key节点之间是通过一个ROS话题来相互通信的。turtle_teleop_key在话题上发布键盘按下的消息，turtlesim则订阅该话题以接收消息。让我们使用rqt_graph来显示当前运行的节点和话题。

#### 2.1 使用rqt_graph

rqt_graph用动态的图显示了系统中正在发生的事情。rqt_graph是rqt程序包中的一部分。如果你发现没有安装，请：

`$ sudo apt-get install ros-<distro>-rqt`
`$ sudo apt-get install ros-<distro>-rqt-common-plugins`

打开一个新终端：

`$ rosrun rqt_graph rqt_graph`

#### 2.2 介绍rostopic

rostopic命令工具能让你获取ROS话题的信息。

你可以使用帮助选项查看可用的rostopic的子命令，例如：

`$ rostopic -h`

或者在输入rostopic 之后双击Tab键输出可能的子命令：

    $ rostopic 
    bw    echo  find  hz    info  list  pub   type 

接下来我们将使用其中的一些子命令来调查turtlesim。

#### 2.3 使用rostopic echo

rostopic echo可以显示在某个话题上发布的数据。

用法：

`$ rostopic echo [topic]`

打开一个新终端：

`$ rostopic echo /turtle1/cmd_vel`

在发布按键话题的节点中活动箭头键后，这边的 rostopic 会显示出：

    linear: 
    x: 2.0
    y: 0.0
    z: 0.0
    angular: 
    x: 0.0
    y: 0.0
    z: 0.0
    ---
    linear: 
    x: 2.0
    y: 0.0
    z: 0.0
    angular: 
    x: 0.0
    y: 0.0
    z: 0.0
    ---

重新查看 rqt_graph 出的图（刷新或者重启），可以看到 rostopic 也订阅了 /turtle1/cmd_vel 主题了。

#### 2.4 使用rostopic list

rostopic list能够列出当前已被订阅和发布的所有话题。

让我们查看一下list子命令需要的参数。打开一个新终端：

`$ rostopic list -h`

在rostopic list中使用verbose选项：

`$ rostopic list -v`

会列出所有发布和订阅的主题及其类型的详细信息。

### 3. ROS消息

话题的通信是通过节点间发送ROS消息实现的。发布者（turtle_teleop_key）和订阅者（turtulesim_node）进行通信，必须发送和接收相同类型的消息。话题的类型是由发布在它上面消息的类型决定的。

#### 3.1 使用rostopic type

rostopic type命令用来查看所发布话题的消息类型。

用法：

`$ rostopic type [topic]`

运行：

`$ rostopic type /turtle1/cmd_vel`

你会看到：

    geometry_msgs/Twist

我们可以使用rosmsg查看消息的详细信息：

    $ rosmsg show geometry_msgs/Twist  

        geometry_msgs/Vector3 linear
          float64 x
          float64 y
          float64 z
        geometry_msgs/Vector3 angular
          float64 x
          float64 y
          float64 z

现在我们已经知道了turtlesim节点想要的消息类型，然后就可以发布命令给turtle了。

### 4. 继续学习rostopic

既然我们已经了解了ROS消息，接下来我们结合消息来使用rostopic。

#### 4.1 使用rostopic pub

rostopic pub可以把数据发布到当前某个正在广播的话题上。

用法：

`$ rostopic pub [topic] [msg_type] [args]`

`$ rostopic pub -1 /turtle1/cmd_vel geometry_msgs/Twist -- '[2.0, 0.0, 0.0]' '[0.0, 0.0, 1.8]'`

以上命令会发送一条消息给turtlesim，告诉它以2.0大小的线速度和1.8大小的角速度移动。

turtle需要一个稳定的频率为1Hz的指令流才能保持移动状态。我们可以使用rostopic pub -r命令来发布源源不断的命令：

`$ rostopic pub /turtle1/cmd_vel geometry_msgs/Twist -r 1 -- '[2.0, 0.0, 0.0]' '[0.0, 0.0, -1.8]'`

可以看到，turtle正沿着一个圆形轨迹持续运动。我们可以在新终端中通过rostopic echo命令来查看turtlesim所发布的数据：

`$ rostopic echo /turtle1/pose`

#### 4.2 使用rostopic hz

rostopic hz报告数据发布的速率。

用法：

`$ rostopic hz [topic]`

我们看一下turtlesim_node发布/turtle/pose得有多快：

`$ rostopic hz /turtle1/pose`

我们也可以结合rostopic type和rosmsg show命令来获取关于某个话题的更深层次信息：

`$ rostopic type /turtle1/cmd_vel | rosmsg show`

到此我们已经使用rostopic调查了这些主题，接下来将使用另一个工具来查看turtlesim发布的数据。

### 5. 使用rqt_plot

rqt_plot命令可以在滚动时间图上显示发布到某个话题上的数据。这里我们将使用rqt_plot命令来绘制正被发布到/turtle1/pose话题上的数据。首先，在一个新终端中输入：

`$ rosrun rqt_plot rqt_plot`

这会弹出一个新窗口，可以在左上角的文本框里面添加任何想要绘制的话题。在里面输入/turtle1/pose/x后，之前不能按下的加号按钮将会变亮。按一下该按钮，并对/turtle1/pose/y重复相同的过程。现在你会在图中看到turtle的x-y位置。

到此我们已经理解了ROS话题是如何工作的，接下来我们开始学习理解ROS服务和参数。

## 七、理解ROS服务和参数

本教程介绍了ROS服务和参数的知识，以及命令行工具rosservice和rosparam的使用方法。

本教程假设上一教程启动的turtlesim_node仍在运行，现在我们来看看turtlesim提供了什么服务：

### 1. ROS服务

服务（Services）是节点之间通讯的另一种方式。服务允许节点发送一个请求（request）并获得一个响应（response）。

### 2. 使用rosservice

rosservice可以很容易地通过服务附加到ROS客户端/服务器框架上。rosservice有许多可用于服务的命令，如下所示：

用法：

    rosservice list         输出活跃服务的信息
    rosservice call         用给定的参数调用服务
    rosservice type         输出服务的类型
    rosservice find         按服务的类型查找服务
    rosservice uri          输出服务的ROSRPC uri

#### 2.1 rosservice list

    $ rosservice list

    /clear
    /kill
    /reset
    /rosout/get_loggers
    /rosout/set_logger_level
    /spawn
    /teleop_turtle/get_loggers
    /teleop_turtle/set_logger_level
    /turtle1/set_pen
    /turtle1/teleport_absolute
    /turtle1/teleport_relative
    /turtlesim/get_loggers
    /turtlesim/set_logger_level

我们使用rosservice type命令进一步查看clear（清除）服务：

#### 2.2 rosservice type

用法：

`$ rosservice type [service]`

来看看clear服务的类型：

    $ rosservice type /clear

    std_srvs/Empty

服务的类型为empty（空），这表明调用这个服务时不需要参数。下面我们使用rosservice call命令调用此服务：

#### 2.3 rosservice call

用法：

`$ rosservice call [service] [args]`

因为服务的类型为empty，所以进行无参数调用：

`$ rosservice call /clear`

跟想象的一样，它清除了turtlesim_node背景上的轨迹。

再让我们看看服务具有参数的情况。查看spawn（产卵）服务的信息：

    $ rosservice type /spawn | rossrv show

    float32 x
    float32 y
    float32 theta
    string name
    ---
    string name

这个服务能让我们可以在给定的位置和角度生成一只新的乌龟。name字段是可选的，这里我们不设具体的名字，让turtlesim自动创建一个。

`$ rosservice call /spawn 2 2 0.2 ""`

该调用返回了新产生的乌龟的名字：

    name: turtle2

### 3. 使用rosparam

rosparam能让我们在ROS参数服务器（Parameter Server）上存储和操作数据。参数服务器能够存储整型（integer）、浮点（float）、布尔（boolean）、字典（dictionaries）和列表（list）等数据类型。rosparam使用YAML标记语言的语法。一般而言，YAML的表述很自然：1是整型，1.0是浮点型，one是字符串，true是布尔型，[1, 2, 3]是整型组成的列表，{a: b, c: d}是字典。rosparam有很多命令可以用来操作参数，如下所示：

用法：

    rosparam set            设置参数
    rosparam get            获取参数
    rosparam load           从文件中加载参数
    rosparam dump           向文件中转储参数
    rosparam delete         删除参数
    rosparam list           列出参数名

我们来看看现在参数服务器上都有哪些参数：

#### 3.1 rosparam list

`$ rosparam list`

可以看到turtlesim节点在参数服务器上有3个参数用于设定背景颜色：

    /rosdistro
    /roslaunch/uris/host_nxt__43407
    /rosversion
    /run_id
    /turtlesim/background_b
    /turtlesim/background_g
    /turtlesim/background_r

让我们使用rosparam set来试着改变一个参数值：

#### 3.2 rosparam set和orosparam get

用法：

`$ rosparam set [param_name]`
`$ rosparam get [param_name]`

现在我们修改背景颜色的红色通道值：

`$ rosparam set /turtlesim/background_r 150`

上述指令修改了参数的值，现在我们需要调用clear服务使得参数的修改能生效：

`$ rosservice call /clear`

然后我们来查看参数服务器上其他参数的值。获取背景的绿色通道的值：

    $ rosparam get /turtlesim/background_g 

    86

也可以用rosparam get /来显示参数服务器上的所有内容：

    $ rosparam get /

    rosdistro: 'noetic

      '
    roslaunch:
      uris:
        host_nxt__43407: http://nxt:43407/
    rosversion: '1.15.5

      '
    run_id: 7ef687d8-9ab7-11ea-b692-fcaa1494dbf9
    turtlesim:
      background_b: 255
      background_g: 86
      background_r: 69

你可能希望将其存储在一个文件中，以便下次可以重新加载它。这通过rosparam很容易就可以实现：

#### 3.3 rosparam dump和rosparam load

用法：

`$ rosparam dump [file_name] [namespace]`
`$ rosparam load [file_name] [namespace]`

在这里，我们将所有的参数写入params.yaml文件：

`$ rosparam dump params.yaml`

你甚至可以将yaml文件重载入新的命名空间，例如copy_turtle：

    $ rosparam load params.yaml copy_turtle
    $ rosparam get /copy_turtle/turtlesim/background_b

    255

至此，我们已经了解了ROS服务和参数的工作原理。接下来，我们一同试试使用rqt_console和roslaunch。

## 八、使用rqt_console和roslaunch

本教程介绍在ROS中使用rqt_console和rqt_logger_level进行调试，以及使用roslaunch同时启动多个节点。

### 1. 预备工作

本教程会用到rqt和turtlesim这两个软件包。如果你发现没有安装，请先：

`$ sudo apt-get install ros-<distro>-rqt ros-<distro>-rqt-common-plugins ros-<distro>-turtlesim`

### 2. 使用rqt_console和rqt_logger_level

rqt_console连接到了ROS的日志框架，以显示节点的输出信息。
rqt_logger_level允许我们在节点运行时改变输出信息的详细级别，包括Debug、Info、Warn和Error。

现在让我们来看一下turtlesim在rqt_console中输出的信息，同时在使用turtlesim时切换rqt_logger_level中的日志级别。在启动turtlesim之前先在两个新终端中运行rqt_console和rqt_logger_level：

`$ rosrun rqt_console rqt_console`

`$ rosrun rqt_logger_level rqt_logger_level`

现在让我们在另一个新终端中启动turtlesim：

`$ rosrun turtlesim turtlesim_node`

现在让我们在rqt_logger_level窗口中刷新一下节点并选择Warn以修改日志级别,

然后让我们把乌龟撞到墙上，看看rqt_console上会显示什么：

    rostopic pub /turtle1/cmd_vel geometry_msgs/Twist -r 1 -- '{linear: {x: 2.0, y: 0.0, z: 0.0}, angular: {x: 0.0,y: 0.0,z: 0.0}}'

#### 2.1 日志记录器级别

日志级别的优先级按以下顺序排列：

    Fatal （致命）
    Error （错误）
    Warn  （警告）
    Info  （信息）
    Debug （调试）

Fatal是最高优先级，Debug是最低优先级。通过设置日志级别，你可以获得所有优先级级别，或只是更高级别的消息。比如，将日志级别设为Warn时，你会得到Warn、Error和Fatal这三个等级的日志消息。

现在按<kbd>Ctrl</kbd>+<kbd>C</kbd>退出turtlesim节点。接下来我们将使用roslaunch来启动多个turtlesim节点和一个模仿者节点，来让一个乌龟模仿另一个乌龟。

#### 2.2 使用roslaunch

roslaunch可以用来启动定义在launch（启动）文件中的节点。

用法：

`$ roslaunch [package] [filename.launch]`

先切换到我们之前创建和构建的beginner_tutorials软件包目录下：

`$ roscd beginner_tutorials`

然后创建一个launch目录：

`$ mkdir launch`
`$ cd launch`

#### 2.3 launch文件

现在一起创建一个名为turtlemimic.launch的launch文件并复制粘贴以下内容进去：

    1 <launch>
    2 
    3   <group ns="turtlesim1">
    4     <node pkg="turtlesim" name="sim" type="turtlesim_node"/>
    5   </group>
    6 
    7   <group ns="turtlesim2">
    8     <node pkg="turtlesim" name="sim" type="turtlesim_node"/>
    9   </group>
    10 
    11   <node pkg="turtlesim" name="mimic" type="mimic">
    12     <remap from="input" to="turtlesim1/turtle1"/>
    13     <remap from="output" to="turtlesim2/turtle1"/>
    14   </node>
    15 
    16 </launch>

#### 2.4 launch解析

* 第1行：表示这是个launch文件。
* 第3-9行：创建两个节点名相同的turtlesim分组，其命名空间不冲突。
* 第11-14行：启动模仿节点，话题的输入和输出分别重命名为turtlesim1和turtlesim2，这样turtlesim2就会模仿turtlesim1。
* 第16行：使得launch文件的XML标签闭合。

#### 2.5 使用roslaunch

现在让我们通过roslaunch命令来运行launch文件：

`$ roslaunch beginner_tutorials turtlemimic.launch`

现在将会有两个turtlesim被启动，然后我们在一个新终端中使用rostopic命令发送：

`$ rostopic pub /turtlesim1/turtle1/cmd_vel geometry_msgs/Twist -r 1 -- '[2.0, 0.0, 0.0]' '[0.0, 0.0, -1.8]'`

你会看到两个turtlesims同时开始移动，虽然发布命令只发送给了turtlesim1。

`$ rqt_graph`

到此，我们算是已经学会了rqt_console和roslaunch命令的使用，接下来我们开始学习使用rosed了解ROS的编辑器选择。现在你可以按Ctrl+C退出所有turtlesims节点了，因为在接下来的教程中你不会再用到它们。

## 九、使用rosed在ROS中编辑文件

本教程展示了如何使用rosed来简化编辑过程。

### 1. 使用rosed

rosed是rosbash套件的一部分。利用它可以直接通过软件包名编辑包中的文件，而无需键入完整路径。

用法：

`$ rosed [package_name] [filename]`

示例：

`$ rosed roscpp Logger.msg`

### 2. Tab补全

使用这个方法，在不知道准确文件名的情况下，你也可以轻松地查看和编辑包中的所有文件。

用法：

`$ rosed [package_name] <tab><tab>`

示例：

`$ rosed roscpp <tab><tab>`

    Empty.srv                   package.xml
    GetLoggers.srv              roscpp-msg-extras.cmake
    Logger.msg                  roscpp-msg-paths.cmake
    SetLoggerLevel.srv          roscpp.cmake
    genmsg_cpp.py               roscppConfig-version.cmake
    gensrv_cpp.py               roscppConfig.cmake
    msg_gen.py                  

### 3. 编辑器

rosed默认的编辑器是vim。其实Ubuntu默认还有一个初学者更友好的编辑器nano，你可以把下面这行加到~/.bashrc文件中来更改默认编辑器：

    export EDITOR='nano -w'

而如果想将默认编辑器设置为emacs，可以进行同样的操作：

    export EDITOR='emacs -nw'

打开一个新的终端，看看是否定义了EDITOR变量：

`$ echo $EDITOR`

    nano -w

or

    emacs -nw

现在你已经成功配置和使用了rosed，接下来我们将学习创建消息和服务。

## 十、创建ROS消息和服务

本教程介绍如何创建和构建msg和srv文件，以及rosmsg、rossrv和roscp命令行工具的使用。

### 1. msg和srv介绍

* msg（消息）：msg文件就是文本文件，用于描述ROS消息的字段。它们用于为不同编程语言编写的消息生成源代码。
* srv（服务）：一个srv文件描述一个服务。它由两部分组成：请求（request）和响应（response）。

### 2. 使用msg

#### 2.1 创建msg

下面，我们将在之前创建的软件包里定义一个新的消息。

`$ roscd beginner_tutorials`
`$ mkdir msg`
`$ echo "int64 num" > msg/Num.msg`

关键的一步：我们要确保msg文件能被转换为C++、Python和其他语言的源代码。

打开package.xml, 确保它包含以下两行且没有被注释。如果没有，添加进去：

    <build_depend>message_generation</build_depend>
    <exec_depend>message_runtime</exec_depend>

在CMakeLists.txt文件中，为已经存在里面的find_package调用添加message_generation依赖项，这样就能生成消息了。直接将message_generation添加到COMPONENTS列表中即可，如下所示：

    # 不要直接复制这一大段，只需将message_generation加在括号闭合前即可
    find_package(catkin REQUIRED COMPONENTS
    roscpp
    rospy
    std_msgs
    message_generation
    )

还要确保导出消息的运行时依赖关系：

    catkin_package(
    ...
    CATKIN_DEPENDS message_runtime ...
    ...)

找到如下代码块：

    # add_message_files(
    #   FILES
    #   Message1.msg
    #   Message2.msg
    # )

删除#符号来取消注释，然后将Message*.msg替换为你的.msg文件名，就像下边这样：

    add_message_files(
    FILES
    Num.msg
    )

手动添加.msg文件后，我们要确保CMake知道何时需要重新配置项目。

现在必须确保generate_messages()函数被调用：

    # generate_messages(
    #   DEPENDENCIES
    #   std_msgs
    # )

像这样：

    generate_messages(
      DEPENDENCIES
      std_msgs
    )

#### 2.2 使用rosmsg

以上就是创建消息的所有步骤。让我们通过rosmsg show命令看看ROS能否识别它。

用法：

`$ rosmsg show [message type]`

示例：

`$ rosmsg show beginner_tutorials/Num`

你会看到：

    int64 num

### 3. 使用srv

#### 3.1 创建srv

让我们使用之前创建的包再来创建服务：

`$ roscd beginner_tutorials`
`$ mkdir srv`

我们将从另一个包复制现有的srv定义，而不是手动创建新的srv。roscp是一个实用的命令行工具，用于将文件从一个包复制到另一个包。

用法：

`$ roscp [package_name] [file_to_copy_path] [copy_path]`

现在我们可以从rospy_tutorials包中复制一个服务：

`$ roscp rospy_tutorials AddTwoInts.srv srv/AddTwoInts.srv`

你也需要像之前对消息那样在package.xml中修改服务字段，因此请看上面描述的所需附加依赖项。

    # add_service_files(
    #   FILES
    #   Service1.srv
    #   Service2.srv
    # )

删除#符号来取消注释，然后将Service*.srv替换为你的.srv文件名，就像下边这样：

    add_service_files(
    FILES
    AddTwoInts.srv
    )

#### 3.2 使用rossrv

以上就是创建服务的所有步骤。让我们通过rossrv show命令看看ROS能否识别它。

用法：

`$ rossrv show <service type>`

示例：

`$ rossrv show beginner_tutorials/AddTwoInts`

你会看到：

    int64 a
    int64 b
    ---
    int64 sum

### 4. msg和srv的一般步骤

如果没做过上面的教程，请先修改下CMakeLists.txt：

    # generate_messages(
    #   DEPENDENCIES
    # #  std_msgs  # Or other packages containing msgs
    # )

取消注释，然后添加任意你的消息用到的包含.msg文件的软件包（本例中为std_msgs），如下所示：

    generate_messages(
    DEPENDENCIES
    std_msgs
    )

现在我们已经创建了一些新消息，所以需要重新make一下软件包：

    # In your catkin workspace
    $ roscd beginner_tutorials
    $ cd ../..
    $ catkin_make
    $ cd -

### 5. 获取帮助

我们已经接触到不少ROS工具了。有时候很难记住每个命令所需要的参数。好在大多数ROS工具都提供了自己的帮助。

尝试：

`$ rosmsg -h`

你可以看到一系列的rosmsg子命令。

    Commands:
    rosmsg show     Show message description
    rosmsg list     List all messages
    rosmsg md5      Display message md5sum
    rosmsg package  List messages in a package
    rosmsg packages List packages that contain messages

同样也可以获得子命令的帮助：

`$ rosmsg show -h`

这会显示rosmsg show所需的参数：

    Usage: rosmsg show [options] <message type>

    Options:
      -h, --help  show this help message and exit
      -r, --raw   show raw message text, including comments

### 6. 复习

总结一下到目前为止我们接触过的一些命令：

    rospack = ros+pack(age) : provides information related to ROS packages

    roscd = ros+cd : changes directory to a ROS package or stack

    rosls = ros+ls : lists files in a ROS package

    roscp = ros+cp : copies files from/to a ROS package
    rosmsg = ros+msg : provides information related to ROS message definitions
    rossrv = ros+srv : provides information related to ROS service definitions
    catkin_make : makes (compiles) a ROS package
    rosmake = ros+make : makes (compiles) a ROS package (if you're not using a catkin workspace) 

现在已经学习了如何创建ROS消息和服务，接下来就将学习如何编写简单的发布者和订阅者（Python）（C++）。

## 十一、编写简单的发布者和订阅者（C++）

本教程介绍如何用C++编写发布者和订阅者节点。

### 1. 编写发布者节点

“节点”是连接到ROS网络的可执行文件。在这里，我们将创建talker（发布者）节点，该节点将不断广播消息。

将当前目录切换到之前的教程中创建的beginner_tutorials包中：

`$ roscd beginner_tutorials`

#### 1.1 代码

首先让我们创建一个src目录来存放我们的源代码文件：

`$ mkdir src`
`$ cd src`

在src目录下创建talker.cpp文件并粘贴以下内容进去：

    #include "ros/ros.h"
    #include "std_msgs/String.h"

    #include <sstream>

    /**
    * This tutorial demonstrates simple sending of messages over the ROS system.
    */
    int main(int argc, char **argv)
    {
    /**
    * The ros::init() function needs to see argc and argv so that it can perform
    * any ROS arguments and name remapping that were provided at the command line.
    * For programmatic remappings you can use a different version of init() which takes
    * remappings directly, but for most command-line programs, passing argc and argv is
    * the easiest way to do it.  The third argument to init() is the name of the node.
    *
    * You must call one of the versions of ros::init() before using any other
    * part of the ROS system.
    */
    ros::init(argc, argv, "talker");

    /**
    * NodeHandle is the main access point to communications with the ROS system.
    * The first NodeHandle constructed will fully initialize this node, and the last
    * NodeHandle destructed will close down the node.
    */
    ros::NodeHandle n;

    /**
    * The advertise() function is how you tell ROS that you want to
    * publish on a given topic name. This invokes a call to the ROS
    * master node, which keeps a registry of who is publishing and who
    * is subscribing. After this advertise() call is made, the master
    * node will notify anyone who is trying to subscribe to this topic name,
    * and they will in turn negotiate a peer-to-peer connection with this
    * node.  advertise() returns a Publisher object which allows you to
    * publish messages on that topic through a call to publish().  Once
    * all copies of the returned Publisher object are destroyed, the topic
    * will be automatically unadvertised.
    *
    * The second parameter to advertise() is the size of the message queue
    * used for publishing messages.  If messages are published more quickly
    * than we can send them, the number here specifies how many messages to
    * buffer up before throwing some away.
    */
    ros::Publisher chatter_pub = n.advertise<std_msgs::String>("chatter", 1000);

    ros::Rate loop_rate(10);

    /**
    * A count of how many messages we have sent. This is used to create
    * a unique string for each message.
    */
    int count = 0;
    while (ros::ok())
    {
        /**
        * This is a message object. You stuff it with data, and then publish it.
        */
        std_msgs::String msg;

        std::stringstream ss;
        ss << "hello world " << count;
        msg.data = ss.str();

        ROS_INFO("%s", msg.data.c_str());

        /**
        * The publish() function is how you send messages. The parameter
        * is the message object. The type of this object must agree with the type
        * given as a template parameter to the advertise<>() call, as was done
        * in the constructor above.
        */
        chatter_pub.publish(msg);

        ros::spinOnce();

        loop_rate.sleep();
        ++count;
    }


    return 0;
    }

#### 1.2 解释

* 初始化ROS系统

* 向主节点宣告我们将要在chatter话题上发布std_msgs/String类型的消息

* 以每秒10次的速率向chatter循环发布消息

接下来我们要编写一个节点来接收消息。

### 2. 编写订阅者节点

#### 2.1 代码

在src目录下创建listener.cpp文件并粘贴以下内容进去：

    #include "ros/ros.h"
    #include "std_msgs/String.h"

    /**
    * This tutorial demonstrates simple receipt of messages over the ROS system.
    */
    void chatterCallback(const std_msgs::String::ConstPtr& msg)
    {
    ROS_INFO("I heard: [%s]", msg->data.c_str());
    }

    int main(int argc, char **argv)
    {
    /**
    * The ros::init() function needs to see argc and argv so that it can perform
    * any ROS arguments and name remapping that were provided at the command line.
    * For programmatic remappings you can use a different version of init() which takes
    * remappings directly, but for most command-line programs, passing argc and argv is
    * the easiest way to do it.  The third argument to init() is the name of the node.
    *
    * You must call one of the versions of ros::init() before using any other
    * part of the ROS system.
    */
    ros::init(argc, argv, "listener");

    /**
    * NodeHandle is the main access point to communications with the ROS system.
    * The first NodeHandle constructed will fully initialize this node, and the last
    * NodeHandle destructed will close down the node.
    */
    ros::NodeHandle n;

    /**
    * The subscribe() call is how you tell ROS that you want to receive messages
    * on a given topic.  This invokes a call to the ROS
    * master node, which keeps a registry of who is publishing and who
    * is subscribing.  Messages are passed to a callback function, here
    * called chatterCallback.  subscribe() returns a Subscriber object that you
    * must hold on to until you want to unsubscribe.  When all copies of the Subscriber
    * object go out of scope, this callback will automatically be unsubscribed from
    * this topic.
    *
    * The second parameter to the subscribe() function is the size of the message
    * queue.  If messages are arriving faster than they are being processed, this
    * is the number of messages that will be buffered up before beginning to throw
    * away the oldest ones.
    */
    ros::Subscriber sub = n.subscribe("chatter", 1000, chatterCallback);

    /**
    * ros::spin() will enter a loop, pumping callbacks.  With this version, all
    * callbacks will be called from within this thread (the main one).  ros::spin()
    * will exit when Ctrl-C is pressed, or the node is shutdown by the master.
    */
    ros::spin();

    return 0;
    }

#### 2.2 解释

* 初始化ROS系统

* 订阅chatter话题

* 开始spin自循环，等待消息的到达

* 当消息到达后，调用chatterCallback()函数

### 3. 构建节点

只需将这几行添加到CMakeLists.txt文件的底部：

    add_executable(talker src/talker.cpp)
    target_link_libraries(talker ${catkin_LIBRARIES})
    add_dependencies(talker beginner_tutorials_generate_messages_cpp)

    add_executable(listener src/listener.cpp)
    target_link_libraries(listener ${catkin_LIBRARIES})
    add_dependencies(listener beginner_tutorials_generate_messages_cpp)

现在可以运行catkin_make：

    # 在你的catkin工作空间下
    $ cd ~/catkin_ws
    $ catkin_make

现在你已经编写了一个简单的发布者和订阅者，让我们来测试发布者和订阅者。

## 十二、编写简单的发布者和订阅者（Python）

本教程介绍如何用Python编写发布者和订阅者节点。

### 1. 编写发布者节点

“节点”是连接到ROS网络的可执行文件。在这里，我们将创建talker（发布者）节点，该节点将不断广播消息。

将目录切换到之前的教程中创建的beginner_tutorials包中：

`$ roscd beginner_tutorials`

#### 1.1 代码

首先让我们创建一个scripts目录来存放我们的Python脚本：

`$ mkdir scripts`
`$ cd scripts`

复制下列文本内容到talker.py文件中：

    #!/usr/bin/env python
    # license removed for brevity
    import rospy
    from std_msgs.msg import String

    def talker():
        pub = rospy.Publisher('chatter', String, queue_size=10)
        rospy.init_node('talker', anonymous=True)
        rate = rospy.Rate(10) # 10hz
        while not rospy.is_shutdown():
            hello_str = "hello world %s" % rospy.get_time()
            rospy.loginfo(hello_str)
            pub.publish(hello_str)
            rate.sleep()

    if __name__ == '__main__':
        try:
            talker()
        except rospy.ROSInterruptException:
            pass

然后将以下内容添加到CMakeLists.txt文件。这样可以确保正确安装Python脚本，并使用合适的Python解释器。

    catkin_install_python(PROGRAMS scripts/talker.py
    DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
    )

### 2. 编写订阅者节点

#### 2.1 代码

复制下列文本内容到listener.py文件中

    #!/usr/bin/env python
    import rospy
    from std_msgs.msg import String

    def callback(data):
        rospy.loginfo(rospy.get_caller_id() + "I heard %s", data.data)
        
    def listener():

        # In ROS, nodes are uniquely named. If two nodes with the same
        # name are launched, the previous one is kicked off. The
        # anonymous=True flag means that rospy will choose a unique
        # name for our 'listener' node so that multiple listeners can
        # run simultaneously.
        rospy.init_node('listener', anonymous=True)

        rospy.Subscriber("chatter", String, callback)

        # spin() simply keeps python from exiting until this node is stopped
        rospy.spin()

    if __name__ == '__main__':
        listener()

然后，编辑你CMakeLists.txt中的catkin_install_python()调用，如下所示：

    catkin_install_python(PROGRAMS scripts/talker.py scripts/listener.py
    DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
    )

### 3. 构建节点

我们使用CMake作为构建系统。是的，即使是Python节点也必须使用它。这是为了确保能为创建的消息和服务自动生成Python代码。

回到catkin工作空间，然后运行catkin_make：

`$ cd ~/catkin_ws`
`$ catkin_make`

现在你已经编写了一个简单的发布者和订阅者，让我们来测试发布者和订阅者。

## 十三、检验简单的发布者和订阅者

本教程将介绍如何运行及测试发布者和订阅者。

### 1. 运行发布者

确保roscore已经开启：

`$ roscore`

在运行你的程序前，请确保你在调用catkin_make后已经source过工作空间的setup.*sh文件：

    # 在catkin工作空间中
    $ cd ~/catkin_ws
    $ source ./devel/setup.bash

上一教程中，我们制作了一个叫做talker的发布者，让我们运行它：

`$ rosrun beginner_tutorials talker      # (C++)`
`$ rosrun beginner_tutorials talker.py   # (Python)`

你会看到：

    [INFO] [WallTime: 1314931831.774057] hello world 1314931831.77
    [INFO] [WallTime: 1314931832.775497] hello world 1314931832.77
    [INFO] [WallTime: 1314931833.778937] hello world 1314931833.78
    [INFO] [WallTime: 1314931834.782059] hello world 1314931834.78
    [INFO] [WallTime: 1314931835.784853] hello world 1314931835.78
    [INFO] [WallTime: 1314931836.788106] hello world 1314931836.79

发布者节点已启动并运行。现在我们需要一个订阅者以接收来自发布者的消息。

### 2. 运行订阅者

上一教程中，我们也制作了一个叫做listener的订阅者，让我们运行它：

`$ rosrun beginner_tutorials listener     # (C++)`
`$ rosrun beginner_tutorials listener.py  # (Python)`

你会看到：

    [INFO] [WallTime: 1314931969.258941] /listener_17657_1314931968795I heard hello world 1314931969.26
    [INFO] [WallTime: 1314931970.262246] /listener_17657_1314931968795I heard hello world 1314931970.26
    [INFO] [WallTime: 1314931971.266348] /listener_17657_1314931968795I heard hello world 1314931971.26
    [INFO] [WallTime: 1314931972.270429] /listener_17657_1314931968795I heard hello world 1314931972.27
    [INFO] [WallTime: 1314931973.274382] /listener_17657_1314931968795I heard hello world 1314931973.27
    [INFO] [WallTime: 1314931974.277694] /listener_17657_1314931968795I heard hello world 1314931974.28
    [INFO] [WallTime: 1314931975.283708] /listener_17657_1314931968795I heard hello world 1314931975.28

完成后，按<kbd>Ctrl</kbd>+<kbd>C</kbd>停止listener和talker。

现在已经研究了简单的发布者和订阅者，让我们再编写简单的服务和客户端（Python）（C++）。

## 十四、编写简单的服务和客户端（C++）

本教程介绍如何用C++编写服务和客户端节点。

### 1. 编写服务节点

这里，我们将创建简单的服务（Service）节点add_two_ints_server，该节点将接收两个整数，并返回它们的和。

将当前目录切换到之前的教程中创建的beginner_tutorials包中：

`$ roscd beginner_tutorials`

#### 1.1 代码

在beginner_tutorials包中创建src/add_two_ints_server.cpp文件并粘贴以下内容进去：

    #include "ros/ros.h"
    #include "beginner_tutorials/AddTwoInts.h"

    bool add(beginner_tutorials::AddTwoInts::Request  &req,
            beginner_tutorials::AddTwoInts::Response &res)
    {
    res.sum = req.a + req.b;
    ROS_INFO("request: x=%ld, y=%ld", (long int)req.a, (long int)req.b);
    ROS_INFO("sending back response: [%ld]", (long int)res.sum);
    return true;
    }

    int main(int argc, char **argv)
    {
    ros::init(argc, argv, "add_two_ints_server");
    ros::NodeHandle n;

    ros::ServiceServer service = n.advertiseService("add_two_ints", add);
    ROS_INFO("Ready to add two ints.");
    ros::spin();

    return 0;
    }

#### 1.2 解释

### 2. 编写客户端节点

#### 2.1 代码

在beginner_tutorials包中创建src/add_two_ints_client.cpp文件并粘贴以下内容进去：

    #include "ros/ros.h"
    #include "beginner_tutorials/AddTwoInts.h"
    #include <cstdlib>

    int main(int argc, char **argv)
    {
    ros::init(argc, argv, "add_two_ints_client");
    if (argc != 3)
    {
        ROS_INFO("usage: add_two_ints_client X Y");
        return 1;
    }

    ros::NodeHandle n;
    ros::ServiceClient client = n.serviceClient<beginner_tutorials::AddTwoInts>("add_two_ints");
    beginner_tutorials::AddTwoInts srv;
    srv.request.a = atoll(argv[1]);
    srv.request.b = atoll(argv[2]);
    if (client.call(srv))
    {
        ROS_INFO("Sum: %ld", (long int)srv.response.sum);
    }
    else
    {
        ROS_ERROR("Failed to call service add_two_ints");
        return 1;
    }

    return 0;
    }

#### 2.2 解释

### 3. 构建节点

再来编辑一下beginner_tutorials里面的CMakeLists.txt文件，文件位于~/catkin_ws/src/beginner_tutorials/CMakeLists.txt，并将下面的代码添加在文件末尾：

    add_executable(add_two_ints_server src/add_two_ints_server.cpp)
    target_link_libraries(add_two_ints_server ${catkin_LIBRARIES})
    add_dependencies(add_two_ints_server beginner_tutorials_gencpp)

    add_executable(add_two_ints_client src/add_two_ints_client.cpp)
    target_link_libraries(add_two_ints_client ${catkin_LIBRARIES})
    add_dependencies(add_two_ints_client beginner_tutorials_gencpp)

现在可以运行catkin_make：

    # 在你的catkin工作空间下
    cd ~/catkin_ws
    catkin_make

现在你已经编写了一个简单的服务和客户端，开始检验简单的服务和客户端吧。

## 十五、编写简单的服务和客户端（Python）

本教程介绍如何用Python编写服务和客户端节点。

### 1. 编写服务节点

这里，我们将创建简单的服务（Service）节点add_two_ints_server，该节点将接收两个整数，并返回它们的和。

将当前目录切换到之前的教程中创建的beginner_tutorials包中：

`$ roscd beginner_tutorials`

#### 1.1 代码

在beginner_tutorials包中创建scripts/add_two_ints_server.py文件并粘贴以下内容进去：

    #!/usr/bin/env python

    from __future__ import print_function

    from beginner_tutorials.srv import AddTwoInts,AddTwoIntsResponse
    import rospy

    def handle_add_two_ints(req):
        print("Returning [%s + %s = %s]"%(req.a, req.b, (req.a + req.b)))
        return AddTwoIntsResponse(req.a + req.b)

    def add_two_ints_server():
        rospy.init_node('add_two_ints_server')
        s = rospy.Service('add_two_ints', AddTwoInts, handle_add_two_ints)
        print("Ready to add two ints.")
        rospy.spin()

    if __name__ == "__main__":
        add_two_ints_server()

别忘了给节点执行权限：

    chmod +x scripts/add_two_ints_server.py

然后将以下内容添加到CMakeLists.txt文件。这样可以确保正确安装Python脚本，并使用合适的Python解释器。

    catkin_install_python(PROGRAMS scripts/add_two_ints_server.py
    DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
    )

#### 1.2 解释

### 2. 编写客户端节点

#### 2.1 代码

在beginner_tutorials包中创建scripts/add_two_ints_client.py文件并粘贴以下内容进去：

    #!/usr/bin/env python

    from __future__ import print_function

    import sys
    import rospy
    from beginner_tutorials.srv import *

    def add_two_ints_client(x, y):
        rospy.wait_for_service('add_two_ints')
        try:
            add_two_ints = rospy.ServiceProxy('add_two_ints', AddTwoInts)
            resp1 = add_two_ints(x, y)
            return resp1.sum
        except rospy.ServiceException as e:
            print("Service call failed: %s"%e)

    def usage():
        return "%s [x y]"%sys.argv[0]

    if __name__ == "__main__":
        if len(sys.argv) == 3:
            x = int(sys.argv[1])
            y = int(sys.argv[2])
        else:
            print(usage())
            sys.exit(1)
        print("Requesting %s+%s"%(x, y))
        print("%s + %s = %s"%(x, y, add_two_ints_client(x, y)))

别忘了给节点执行权限：

    chmod +x scripts/add_two_ints_client.py

然后，在你的CMakeLists.txt中编辑catkin_install_python()调用，就像这样：

    catkin_install_python(PROGRAMS scripts/add_two_ints_server.py scripts/add_two_ints_client.py
    DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
    )

#### 2.2 解释

### 3. 构建节点

我们使用CMake作为构建系统。是的，即使是Python节点也必须使用它。这是为了确保能为创建的消息和服务自动生成Python代码。

切换当前目录到你的catkin工作空间，然后运行catkin_make：

    # 在你的catkin工作空间中
    $ cd ~/catkin_ws
    $ catkin_make

现在你已经编写了一个简单的服务和客户端，开始检验简单的服务和客户端吧。

## 十六、检验简单的服务和客户端

本教程将介绍如何运行及测试服务和客户端。

### 1. 运行服务

确保roscore已经开启：

`$ roscore`

让我们先开始运行服务：

`$ rosrun beginner_tutorials add_two_ints_server     # (C++)`
`$ rosrun beginner_tutorials add_two_ints_server.py  # (Python)`

你会看到：

    Ready to add two ints.

### 2. 运行客户端

现在，运行客户端并附带必要的参数：

`$ rosrun beginner_tutorials add_two_ints_client 1 3     # (C++)`
`$ rosrun beginner_tutorials add_two_ints_client.py 1 3  # (Python)`

你会看到：

    Requesting 1+3
    1 + 3 = 4

现在，你已经成功地运行了你的第一个服务和客户端，可以开始学习如何记录和回放数据了。

## 十七、录制和回放数据

教你如何将正在运行的ROS系统中的数据记录到一个bag文件中，然后通过回放这些数据来来重现相似的运行过程。

### 1. 录制数据（创建bag文件）

这一部分将指导你如何从正在运行的ROS系统中记录话题数据。话题数据将被积累到一个袋（bag）文件中。

首先，在不同的终端中分别执行：

终端1：

`$ roscore`

终端2：

`$ rosrun turtlesim turtlesim_node`

终端3：

`$ rosrun turtlesim turtle_teleop_key`

按键盘上的箭头键可以使乌龟在屏幕上移动。请注意，要移动乌龟，你必须选中启动turtle_teleop_key的终端窗口，而不是turtlesim。

#### 1.1 录制所有发布的话题

首先让我们来检查一下当前系统中发布的所有话题。打开一个新终端：

`$ rostopic list -v`

现在我们将记录发布的数据。打开一个新终端：

`$ mkdir ~/bagfiles`
`$ cd ~/bagfiles`
`$ rosbag record -a`

这里我们只是创建了一个临时目录来记录数据，然后运行rosbag record带选项-a，表明所有发布的话题都应该积累在一个bag文件中。

然后回到turtle_teleop节点所在的终端窗口并控制乌龟随意移动10秒钟左右。

在运行rosbag record的窗口中按Ctrl+C以退出。现在查看~/bagfiles目录中的内容，你应该会看到一个以年份、日期和时间开头且扩展名是.bag的文件。这就是传说中的袋文件，它包含rosbag record运行期间由任何节点发布的所有话题。

#### 1.2 检查并回放bag文件

现在我们已经使用rosbag record命令录制了一个bag文件，接下来我们可以使用rosbag info查看它的内容，或用rosbag play命令回放。首先我们来看看袋子里记录了什么。我们可以执行info命令检查bag文件的内容而不回放它。在bag文件所在的目录下执行以下命令：

`$ rosbag info <your bagfile>`

下一步是回放bag文件以再现系统运行过程。首先用<kbd>Ctrl</kbd>+<kbd>C</kbd>杀死之前运行的turtle_teleop_key，但让turtlesim继续运行。在终端中bag文件所在目录下运行以下命令：

`$ rosbag play <your bagfile>`

在这个窗口中你应该会立即看到如下类似信息：

    [ INFO] [1418271315.162885976]: Opening 2014-12-10-20-08-34.bag

    Waiting 0.2 seconds after advertising topics... done.

    Hit space to toggle paused, or 's' to step.

默认模式下，rosbag play命令在公告每条消息后会等待一小段时间（0.2秒）才真正开始发布bag文件中的内容。等待时间可以通过-d选项来指定。你可以通过-s参数选项让rosbag play不从bag文件的开头开始，而是从某个指定的时间开始。最后一个可能比较有趣的参数选项是-r选项，它允许你通过设定一个参数来改变消息发布速率。如果执行：

`$ rosbag play -r 2 <your bagfile>`

你应该会看到乌龟的运动轨迹有点不同了，这时的轨迹应该是相当于当你以两倍的速度通过按键发布控制命令时产生的轨迹。

### 2. 录制数据子集

如果还有turtlesim节点在运行，先退出他们，然后重新启动键盘控制节点相关的启动文件：

`$ rosrun turtlesim turtlesim_node`
`$ rosrun turtlesim turtle_teleop_key`

在bag文件所在目录下执行以下命令：

`$ rosbag record -O subset /turtle1/cmd_vel /turtle1/pose`

现在你已经学会了如何录制和回放数据，接下来我们可以从bag文件中读取消息。

## 十八、从bag文件中读取消息

了解从bag文件中读取所需话题的消息的两种方法，以及ros_readbagfile脚本的使用。

首先，您需要一个袋（bag）文件。可以按照上一教程自己生成，或从<https://webviz.io>下载一个示例文件：

    wget <https://open-source-webviz-ui.s3.amazonaws.com/demo.bag>

* ### 方法1：立即回放消息并在多个终端中查看输出

1. 你需要知道你想从bag文件中读取的准确话题名。那让我们看看袋子里有什么。在任何终端中用这个命令，来手动检查所有已发布的话题，以及向每个话题发布了多少消息：

        $ time rosbag info demo.bag  
        # 或者你已经知道话题名称的话：
        $ time rosbag info mybag.bag | grep -E "(topic1|topic2|topic3)"

    可以看到，有30条消息发布在/obs1/gps/fix话题上，有40条消息发布在/diagnostics_agg话题上。让我们把这些提取出来。

2. 在终端1（比如本终端）中，启动roscore，这样可以运行必需的ROS主节点：

    `$ roscore`

3. 打开另一个终端（试试按下Ctrl + Shift + T），订阅/obs1/gps/fix话题并复读该话题上发布的所有内容，同时用tee命令转储到一个yaml格式的文件中以便之后查看：

    `$ rostopic echo /obs1/gps/fix | tee topic1.yaml`

    你会看到：

        $ rostopic echo /obs1/gps/fix | tee topic1.yaml
        WARNING: topic [/obs1/gps/fix] does not appear to be published yet

4. 再打开一个新终端，订阅另一个话题/diagnostics_agg。

    `$ rostopic echo /diagnostics_agg | tee topic2.yaml`

    你会看到：

        $ rostopic echo /diagnostics_agg | tee topic2.yaml
        WARNING: topic [/diagnostics_agg] does not appear to be published yet

5. 对其他你感兴趣的话题重复这一步骤，每个话题必须有自己的终端。

6. 再打开另一个新终端来回放bag文件。这一次我们将尽可能快地回放bag文件（使用--immediate选项），只会发布我们感兴趣的话题。格式如下：

        time rosbag play --immediate demo.bag --topics /topic1 /topic2 /topic3 /topicN

    本例中，命令如下：

    `$ time rosbag play --immediate demo.bag --topics /obs1/gps/fix /diagnostics_agg`

    你会看到：

        $ time rosbag play --immediate demo.bag --topics /obs1/gps/fix /diagnostics_agg
        [ INFO] [1591916465.758724557]: Opening demo.bag

        Waiting 0.2 seconds after advertising topics... done.

        Hit space to toggle paused, or 's' to step.
        [RUNNING]  Bag Time: 1490150297.770734   Duration: 19.703405 / 19.703405               
            Done.

        real  0m1.570s
        user  0m0.663s
        sys 0m0.394s

7. 完成！现在看一下你的两个终端，每个终端都订阅了一个话题，每个话题类型的所有消息用YAML格式输出，每条消息之间用---分割。用你喜欢的文本编辑器（最好支持YAML的语法高亮，例如Visual Studio Code）来查看文件中的消息。例如，topic1.yaml中的最后两条消息是这样的：

        ---
        header: 
        seq: 4027
        stamp: 
            secs: 1490150296
            nsecs:  66947432
        frame_id: "gps"
        status: 
        status: 0
        service: 1
        latitude: 37.4008017844
        longitude: -122.108119889
        altitude: -6.4380177824
        position_covariance: [1.0, 0.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 1.0]
        position_covariance_type: 0
        ---
        header: 
        seq: 4028
        stamp: 
            secs: 1490150297
            nsecs: 744347249
        frame_id: "gps"
        status: 
        status: 0
        service: 1
        latitude: 37.4007565466
        longitude: -122.108159482
        altitude: -6.35130467023
        position_covariance: [1.0, 0.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 1.0]
        position_covariance_type: 0
        ---

* ### 方法2：使用ros_readbagfile脚本轻松地提取感兴趣的话题

注意：您可以杀死任何正在运行的进程。比如说连roscore都不需要运行。

1. 下载并安装[ros_readbag.py](https://github.com/ElectricRCAircraftGuy/eRCaGuy_dotfiles/blob/master/useful_scripts/ros_readbagfile.py)：

        # Download the file
        wget https://raw.githubusercontent.com/ElectricRCAircraftGuy/eRCaGuy_dotfiles/master/useful_scripts/ros_readbagfile.py
        # Make it executable
        chmod +x ros_readbagfile.py
        # Ensure you have the ~/bin directory for personal binaries
        mkdir -p ~/bin
        # Move this executable script into that directory as `ros_readbagfile`, so that it will
        # be available as that command
        mv ros_readbagfile.py ~/bin/ros_readbagfile
        # Re-source your ~/.bashrc file to ensure ~/bin is in your PATH, so you can use this
        # new `ros_readbagfile` command you just installed
        . ~/.bashrc

2. 通过rosbag info命令确定要从bag文件中读取的准确话题名，如上面方法1的第一步所示。

3. 然后使用ros_readbagfile，大体格式如下：

    `$ ros_readbagfile <mybagfile.bag> [topic1] [topic2] [topic3] [...]`

    要阅读上面方法1中显示的相同消息，请使用：

    `time ros_readbagfile demo.bag /obs1/gps/fix /diagnostics_agg | tee topics.yaml`

    就是这样！你会看到它快速打印出所有70条信息。以下是终端输出的最后部分：

                key: "Early diagnostic update count:"
                value: "0"
            - 
                key: "Zero seen diagnostic update count:"
                value: "0"
        =======================================
        topic:           /obs1/gps/fix
        msg #:           30
        timestamp (sec): 1490150297.770734310
        - - -
        header: 
        seq: 4028
        stamp: 
            secs: 1490150297
            nsecs: 744347249
        frame_id: "gps"
        status: 
        status: 0
        service: 1
        latitude: 37.4007565466
        longitude: -122.108159482
        altitude: -6.35130467023
        position_covariance: [1.0, 0.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 1.0]
        position_covariance_type: 0
        =======================================
        Total messages found = 70.
        DONE.

        real  0m2.897s
        user  0m2.457s
        sys 0m0.355s

    现在用你喜欢的文本编辑器打开topics.yaml，看看它从bag文件中提取的所有消息。

现在，您已经了解了如何从预先录制的包文件中读取消息，可以开始roswtf入门了。

## 十九、roswtf入门

简单介绍了roswtf工具的基本使用方法。

在开始本教程之前，请确保roscore没有运行。

对于Linux，您可以通过以下方式检查roscore是否在运行（如果看到类似这样的一行包含rosmaster，这是roscore的一部分，则说明ros​​core正在运行）

    $ ps -ef | grep -i rosmaster
    00:00:00 /usr/bin/python /opt/ros/kinetic/bin/rosmaster 

### 1. 安装检查

roswtf可以检查你的系统并尝试发现问题，我们来试试看：

`$ roscd rosmaster`
`$ roswtf`

你应该会看到（各种详细的输出信息）：

    Package: rosmaster
    ================================================================================
    Static checks summary:

    No errors or warnings
    ================================================================================

    ROS Master does not appear to be running.
    Online graph checks will not be run.
    ROS_MASTER_URI is [http://localhost:11311]

### 2. 在线检查

下一步，我们需要启动一个Master，所以要在新终端启动roscore，然后继续。

现在按照相同的顺序再次运行以下命令：

`$ roscd`
`$ roswtf`

你应该会看到：

    No package or stack in context
    ======================================================
    Static checks summary:

    No errors or warnings
    ======================================================
    Beginning tests of your ROS graph. These may take awhile...
    analyzing graph...
    ... done analyzing graph
    running graph rules...
    ... done running graph rules

    Online checks summary:

    Found 1 warning(s).
    Warnings are things that may be just fine, but are sometimes at fault

    WARNING The following node subscriptions are unconnected:
    * /rosout:
    * /rosout

### 3. 错误

roswtf会对一些系统中看起来异常但可能是正常的运行情况发出警告。也会对确实有问题的情况报告错误。

接下来我们给ROS_PACKAGE_PATH环境变量设置一个bad值，并退出roscore以简化检查输出信息。

`$ roscd`
`$ ROS_PACKAGE_PATH=bad:$ROS_PACKAGE_PATH roswtf`

这次我们会看到：

    Stack: ros
    ================================================================================
    Static checks summary:

    Found 1 error(s).

    ERROR Not all paths in ROS_PACKAGE_PATH [bad] point to an existing directory: 
    * bad

    ================================================================================

    Cannot communicate with master, ignoring graph checks
