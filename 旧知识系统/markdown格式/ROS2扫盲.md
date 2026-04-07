## 画布 1: ROS2扫盲

# ROS2扫盲

- 安装
  - 安装ros2本体
    - 启用 Universe 库：
Bash
sudo apt install software-properties-common
sudo add-apt-repository universe
添加密钥：
Bash
sudo apt update && sudo apt install curl -y
sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg
添加存储库：
Bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive
  - 细分主题 2
- 工具
  - rqt

## 画布 2: 导航

# 导航

- 坐标变换工具
  - 代码结构
    - 命令行指令
      - ros2
        - run
          - tf2_ros
            - 功能：发布A到B的变换
              - static_transform_publisher
                - --x
                - --y
                - --z
                - --roll
                - --pitch
                - --yaw
                - --frame-id
                  - 父坐标系名A
                - --child-frame-id
                  - 子坐标系名B
            - 功能：查询A到B之间的关系
              - tf2_echo
                - A
                - B
            - 功能：查看所有坐标变换框架
              - tf2_monitor
                - --frames
          - tf2_tools
            - 功能：查看tf树
              - view_frames
        - topic
          - 功能：查看所有话题
            - list
          - 功能：查看某个具体话题的内容
            - info /tpc
          - 功能：订阅某个话题
            - echo /tpc
        - interface
          - 功能：查看特点消息接口A的定义
            - show A
        - pkg
          - 功能：创建功能包（在ws文件夹下使用）
            - create
              - <name>
              - --build-type
                - ament_python
              - --dependencies
                - rclpy（ros2的客户端库，提供诸如Node节点这类基础数据类型）
                - geometry_msgs
                  - 几何信息格式（Pose位姿，Point等）
                - tf2_ros
                  - 坐标变换库（将坐标变换发布到/tf或/tf_static）
                - tf_transformations
                  - 信息格式转换（RPY->四元数）
              - --license
                - Apache-2.0
        - node
          - 功能：查看所有节点
            - list
        - bag
          - 功能：记录指定话题的消息数据流，保存文件
            - record
              - <topic_name>
          - 功能：回访数据流
            - play
              - <folder_name>
      - colcon
        - 功能：构建
          - build
            - --package-select
        - 功能：查看已有的包
          - list
    - 程序
      - .py
        - packages
          - rclpy
            - node
              - Node
            - .init()（初始化）
            - .spin(Node object) （运行节点）
            - .shutdown()（结束运行）
          - tf2_ros
            - StaticTranformBroadcaster(静态 变换 广播)
          - geometry_msgs
            - msg
              - TransformStamped（变换 印记 -> 变换消息格式）
          - tf_transformations
            - quaternion_from_euler（欧拉角RPY -> 四元数）
          - math
            - radians()
        - classes
          - Node("name")
          - StaticTranformBroadcaster(Node object)
            - .sendTransformer(transform)
（使用 静态坐标发布者 发布 变换）
（创建封装对象 - 使用对象 分离）
          - TransformStamped()
            - 成员
              - header
                - frame_id(父坐标系)
                - stamp（时间戳）
                  - self.get_clock().now().to_msg()
              - child_frame_id(子坐标系)
              - transform
                - translation（平移部分）
                  - x
                  - y
                  - z
                - rotation（旋转部分，四元数）
                  - x
                  - y
                  - z
                  - w
        - functions
          - 获取四元数
            - radians(180)
              - 角度转弧度
            - quaternion_from_euler(radians(180), 0, 0)
              - RPY -> 四元数（可索引对象）
      - .cpp
        - packages
          - <memory>（智能指针）
            - shared_ptr
            - make_shared
          - rclcpp（Node类）
            - 核心类
              - Node
            - 日志
              - RCLCPP_INFO()
            - 初始化与回收
              - init
              - spin
              - shutdown
            - 时间相关
          - tf2_ros（节点的核心组件）
            - static_transform_broadcaster.h
              - tf2_ros::StaticTransformBroadcaster
            - transform_broadcaster.h
              - tf2_ros::TransformBroadcaster
            - transform_listener.h
              - tf2_ros::TransformListener
            - buffer.h
              - tf2_ros::Buffer
          - geometry_msgs
            - msg
              - transform_stamped.hpp
          - tf2
            - LinearMath
              - Quaternion.h
            - exceptions.h
              - tf2::TransformException
            - utils.h
              - 细分主题 1
        - functions
          - RCLCPP_INFO
            - <logger>
              - this->get_logger()
            - <format>
              - 输出的内容
          - RCLCPP_WARN
            - <logger>
              - this->get_logger()
            - <format>
              - 输出的内容
        - classes
          - geometry_msgs
            - geometry_msgs::msg::TransformStamped
（一个刚体变换）
              - header
                - stamp
                  - <time>
                    - this -> now()
                - frame_id
              - child_frame_id
              - translation
                - x
                - y
                - z
              - rotation
                - x
                  - <x>
                    - tf2::Quaternion.x()
                - y
                - z
                - w
          - tf2
            - tf2::Quaternion
（四元数）
              - 方法
                - setRPY(r, p, y)
                  - 基于四元数赋值r，p，y
            - tf2::Matrix3x3
（3x3矩阵）
              - 初始化
                - tf2::Quaternion q(qx, qy, qz, qw);
tf2::Matrix3x3 m(q);
              - 方法
                - getRPY(r, p, y)
                  - 基于3x3矩阵赋值r，p，y
            - tf2::TimePointZero
（tf时间类型，表示此刻）
          - rclcpp
            - rclcpp::Node
（ros2节点）
              - create_wall_timer()
                - <period>
                  - std::chrono::milliseconds(static_cast<int>(timer_period_ * 1000))
                  - using namespace std::chrono_literals
&
500ms
                - <callback>
                  - std::bind(&function, this)
              - get_clock ()
                - [return]
                  - rclcpp::Clock::SharedPtr
            - rclcpp::TimerBase::SharedPtr timer_
（定时器类型）
              - <time_period>
              - <callback>
          - tf2_ros
（节点的核心组件：各种发布/订阅者）
            - tf2_ros::StaticTransformBroadcaster(this)
（静态的坐标变换发布器）
              - sendTransform()
                - <transform>
            - tf2_ros::TransformBroadcaster(this)
（静态的坐标变换发布器）
              - sendTransform()
                - <transform>
              - 注意
                - 每次发布的时候需要更新时间戳
            - tf2_ros::Buffer
（缓冲器类型）
              - args
                - <clock>
                  - this->get_clock()
              - method
                - lookupTransform
（查找从<source_frame>到<target_frame>的变换）
                  - <target_frame>
                  - <source_frame>
                  - <time>
                    - tf2::TimePointZero
                  - <timeout>
                    - 100ms
              - return
                - geometry_msgs::msg::TransformStamped
            - tf2_ros::TransformListener
（监听器类型）
              - <buffer_object>
                - *<sharedptr_buffer>
      - .cmake
        - functions
          - cmake
            - 功能：配置功能包名称
              - project()
            - 功能：设置编译选项
              - add_compile_options()
                - -Wall
                  - 启用一组常见的警告信息，帮助发现代码中的潜在问题。
                - -Wextra
                  - 启用额外的警告，比 -Wall 更严格，能捕获更多可疑代码。
                - -Wpedantic
                  - 要求代码严格遵循 ISO C/C++ 标准，禁用非标准特性（如 GCC/Clang 扩展）。
            - 功能：查找库+设置路径等变量+创建CMake目标（.o文件）便于链接使用
              - find_package
                - <packages>*
                - [version]
                - [REQUIRED]
                  - 必需依赖，找不到则报错
                - [COMPONENTS comp1 comp2...]
                  - 指定需要的组件（如 Boost 的 filesystem、system）
            - 功能：创建可执行文件
              - add_executable
                - <target_name>（功能包下的单个功能）
                - <source>.cpp*
            - 功能：定义安装规则，指定项目构建后如何安装到系统目录
              - install
                - [option1: type]
                  - TARGETS
                    - <target>（安装可执行文件）
                    - <library>（安装库文件）
                  - DIRECTORY
                    - include/
（安装在include目录下）
                  - FILES
                    - <file>*
                - 组合：TRAGETS + DESTINATION
            - 功能：提供头文件搜索路径
          - ament_cmake
            - 功能：cmake宏，自动创建库/可执行程序
              - ament_auto_add_executable
                - <project_name>
                - <src>.cpp*
              - ament_auto_add_library
                - <project_name>
                - SHARED
                  - 有：动态库
无：静态库
                - DIRECTORY
                  - 指定目录
            - 功能：ROS2/ament 构建系统专用，为指定目标自动添加ROS 2 包的依赖
              - ament_target_dependencies
                - <target>（与单个功能配套）
                - <dependency>*
            - 功能：ROS2/ament 构建系统专用，声明本包的依赖需要传递给下游包
              - ament_export_dependencies
            - 功能：导出依赖等
              - ament_package
        - 流程
          - 构建前
            - 命名
            - 导出依赖
              - set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
            - 寻找依赖
              - find_package(... REQUIRED)
          - 构建
            - 提供头文件搜索路径
（基础路径：当前cmake文件所在的路径）
              - include_directories(include)
            - 创建 可执行文件 目标
              - add_executable(<name> <file>*)
            - 链接依赖
              - ament_target_dependencies(<target> <deps>*)
            - 安装目标：安装在ws/install/<name>目录下
              - install(TARGETS <target>*)
              - install(DIRECTORY include/
  DESTINATION include/
)
  - 文件系统
    - 核心
      - 工作区ws（cpp）
        - build
        - install
          - setup.zsh
        - log
        - src
          - 功能包<name>
            - include
              - <name>
            - src
            - CMakeList.txt
              - project(<name>)功能包
            - package.xml
    - 其他
      - py
        - setup.py
  - 笔记
    - tf变换的本质
      - 话题：特定的消息格式
      - 而查询则是将：/tf_static中的所有话题串联起来
      - kw：缓冲区
      - 区别
        - /tf
          - 不断发布
        - /tf_static
    - 手眼坐标变换
      - ftr
        - 动态发布
      - 需要用到的库
  - 资源下载
    - 3D旋转可视化
      - 下载
        - sudo apt install ros-humble-mrpt2 -y
      - 使用
        - 3d-rotation-converter

## 画布 3: old

# old

- 头文件集合
  - opencv
    - cv_bridge/cv_bridge.h
      - func
        - 将 ROS 的 sensor_msgs/Image 或 sensor_msgs/CompressedImage 消息转换为 OpenCV 的 cv::Mat 格式
        - 将 OpenCV 的 cv::Mat 格式转换回 ROS 图像消息
    - opencv2
      - core.hpp
        - def
          - 基本数据类型
      - imgproc.hpp
        - def
          - 图像处理
  - ros2系列
    - rclcpp（ros client library）
      - duration.hpp
        - def
          - 时间持续
      - qos.hpp
        - def
          - 服务质量策略
    - rmw（ROS MiddleWare）
      - def
        - rclcpp的下层
        - 让ROS2使用不同的DDS实现
      - 子项
        - qos_profiles.h
    - ament_index_cpp
      - get_package_share_directory.hpp
        - def
          - 在运行时获取 ROS2 包的安装路径（share 目录）
    - img_transport
      - img_transport.hpp
        - def
          - 核心Subscription类和Publisher类
  - tf系列
    - tf2
      - LinearMath/Matrix3x3.h
        - 对3x3矩阵的特殊优化
      - convert.h
        - 实现tf2到ROS2的数据类型的转换
    - tf2_geometry_msgs
      - tf2_geometry_msgs.hpp
- OTH
  - DDS（Data Distribution Service）
    - def
      - DDS 本身是一个独立的国际标准（OMG组织制定），专为实时分布式系统设计。
ROS 2不直接实现通信，而是构建在DDS之上，通过DDS实现节点间的发现、连接和数据传输。
    - categories
      - Fast DDS（原Fast RTPS）：ROS 2默认实现
      - Cyclone DDS：轻量、高性能
      - Connext DDS：商业级，功能最全
      - OpenSplice：另一个开源实现
  - ROS2架构
    - 你的ROS 2 C++代码 (使用 rclcpp)
        ↓
rclcpp (C++客户端库)
        ↓
rcl (C语言客户端库，提供通用ROS API)
        ↓
rmw (中间件抽象层) ← 你问的这个
        ↓
具体的DDS实现 (Fast DDS、Cyclone DDS、Connext等)
        ↓
操作系统网络栈
  - yaml文件格式
  - 相机成像体系
    - content
      - 相机位姿
        - core
          - 等价于刚体变换矩阵T
          - T = [R t]
      [ 0 1]
          - 是描述从世界坐标系到相机坐标系的一个变换，接受[X, Y, Z, 1]^T作为输入，产出相机坐标系下的坐标
  - 基变换 - 线性变换
    - 核心公式
      - v = A * [v]_A = B * [v]_B
      - B = AP
        - 其中P的含义是从基A到基B的过渡矩阵：
B的每个基向量在A基下的坐标按列组成P
- 具体类
  - rclcpp::Node
    - declare_parameter(
param_name,
default_value
)
      - 声明一个参数并获取其值
  - visualization_msgs::msg::Marker
    - def
      - 用于在 RViz 等可视化工具中绘制各种形状和标注的消息类型
    - core
      - publish Maker <=> update image
    - properties
      - header
        - std_msgs/Header
          - ??
      - ns
        - 命名空间
      - id
        - 唯一id，在ns中不能重复
      - type
        - 形状类型
      - action
        - 操作（ADD, DELETE, DELETE ALL等）
      - geometry_msgs/Pose pose
        - 位置和姿态（位姿）
      - geometry_msgs/Vector3 scale
        - 尺寸缩放
      - std_msgs/ColorRGBA color
        - 颜色
      - duration lifetime
        - 自动删除时间（0=不自动删除）
      - bool frame_locked
        - 是否固定到坐标系
      - Point[] points
        - 用于线条，点云等
      - ColorRGBA[] colors
        - 每个点的颜色
      - string text
        - 文本内容
      - string mesh_resource
        - 网格文件路径
      - bool mesh_use_embedded_materials
        - 使用网格内嵌材质
    - OTH
      - MarkerArray
        - def
          - 多个Marker的集合
  - rclcpp::Duration
    - func
      - from_seconds(0.1)
        - 创建一个持续0.1s的Duration对象
  - rclcpp::ParameterEventHandler
    - def
      - 监听和处理节点参数的动态变化（自动触发回调函数）
    - method
      - 初始化
        - param_event_handler_ = std::make_shared<rclcpp::ParameterEventHandler>(this);
      - 绑定回调
        - add_parameter_callback(
param_name,
callback(const rclcpp::Parameter &param)
)
        - add_parameter_event_callback
    - OTH
      - rclcpp::Parameter
      - 外置设置参数
        - 直接设置
          - ros2 param set /node_name parameter_name value
        - 通过文件设置
          - ros2 param load /node_name params.yaml
        - 导出参数
          - ros2 param dump /node_name > my_params.yaml
  - sensor_msgs::msg::CameraInfo
    - params
      - 信息头
        - std_msgs/Header header
      - 图像尺寸
        - uint32 height
          - 高度
        - uint32 width
          - 宽度
      - 畸变模型
        - string distortion_model
          - 畸变模型名称
            - "plumb_bob"（Radial-Tangential）
            - "rational_polynomial"
        - float64[] D
          - 畸变系数
      - 相机内参矩阵
        - float64[9] K
          - K = [fx 0 cx
        0  fy cy
        0   0  1]
          - 参数解释
            - fx/fy
              - 每毫米的物体在图像上占据多少像素
            - cx/cy
              - 主点的像素坐标
          - 作用
            - u = fx * Xc/Zc + cx
            - v = fy * Yc/Zc + cy
            - 参数解释
              - u/v
                - 图像像素坐标
              - Xc/Yc/Zc
                - 相机坐标系中的三维点坐标
            - 核心变换公式
              - Xc/Zc = (u - cx) / fx
                - u - cx：u是在像素坐标系下的坐标，cx和fx也是，fx是x方向的缩放比将焦距映射的结果
