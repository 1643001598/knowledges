# ROS2
- 基础：配置/编译
  - 标准编译流
    - 创建pkname/src
    - 编写源文件
    - colcon build
      - 特殊
        - 编译特定包
          - colcon build --packages-select your_package_name
        - 编译时启用调试信息
          - colcon build --cmake-args -DCMAKE_BUILD_TYPE=Debug
        - 并行编译加速
          - colcon build --parallel-workers 8
        - 编译并运行测试
          - colcon build --event-handlers console_direct+
colcon test
        - 清理编译结果
          - colcon build --cmake-clean-first
    - source ~/ros2_ws/install/setup.bash
    - ros2 run my_cpp_package my_node
  - cmake设置
  - 常用命令
    - 检查包依赖
      - rosdep install -i --from-path src --rosdistro humble -y
    - 列出工作空间中的包
      - colcon list
    - 清理特定包
      - colcon build --packages-select my_package --cmake-clean-first
    - 查看包信息
      - ros2 pkg list
ros2 pkg prefix my_package
  - 文件结构
- 核心包/函数
  - 核心包
    - rclcpp(ROS Client Library for C++)
      - 核心头文件
        - <rclcpp/rclcpp.hpp>： 最常用的头文件，包含了大部分常用组件。
        - <rclcpp/node.hpp>： Node 类的定义。
        - <rclcpp/publisher.hpp>： Publisher 类的定义。
        - <rclcpp/subscription.hpp>： Subscription 类的定义。
        - <rclcpp/service.hpp>： Service 和 Client 类的定义。
        - <rclcpp/action/server.hpp> 和 <rclcpp/action/client.hpp>： Action 相关的类。
      - 细分主题 2
    - rcl
  - 核心函数
    - RCLCPP_INFO(this -> get_logger(), "msg")
    - rclcpp::init(argc, argv);
      - 接收参数的主函数
        - int main(int argc, char * argv[])
          - char *[]的解释
            - 字符指针数组
          - argv[]的解释
            - argv[0]: 程序名称
            - argv[1] 到 argv[argc-1]: 用户传入的命令行参数
          - argc的解释
            - 命令行参数的数量
    - auto node = std::make_shared<PublisherNode>()
      - 作用
        - 初始化PublisherNode
      - 等价于
        - std::shared_ptr<PublisherNode>(new PublisherNode())
    - rclcpp::spin(node)
      - 启动节点
    - rclcpp::shutdown()
- 核心概念
  - 节点
  - 话题
    - 异步单向/发布-订阅
  - 服务
    - 同步双向/请求-响应
  - 动作
    - 异步双向/长时间请求-反馈-结果
  - DDS
    - 通信底层
- 核心类
  - rclcpp::Node
    - 构造方法
      - 公有继承Node
      - 构造函数中，使用初始化列表初始化父类Node(name)，
    - 其他方法
      - create_wall_timer(
time,
std::bind(&PublisherNode::publish_message, this)
)
        - 对象类型
          - rclcpp::TimerBase::SharedPtr
        - std::bind的解释
          - 不立刻执行，而是提供可执行对象
      - 节点基本信息
        - getname()
          - 获取节点名称
        - get_namespace()
          - 获取命名空间
        - get_fully_qualified_name()
          - 获取全称：命名空间+节点名称
      - 发布者相关
        - 创建发布者
          - template<typename MessageT, typename AllocatorT = std::allocator<void>>
typename rclcpp::Publisher<MessageT, AllocatorT>::SharedPtr
create_publisher(
    const std::string &topic_name,
    const rclcpp::QoS &qos,
    const rclcpp::PublisherOptionsWithAllocator<AllocatorT> &options = ...
);
            - 解释
              - template<typename MessageT, typename AllocatorT = std::allocator<void>>
                - 创建一个模板（在调用时用create_publisher<typename>来使用模板），参数为MessageT; AllocatorT(已被指定)
注：这个参数会延申到下面的返回值中
              - typename rclcpp::Publisher<MessageT, AllocatorT>::SharedPtr
                - 返回类型
                - 使用typename的原因
                  - 使之成为模板上下文的返回结构
              - 参数列表前两个是必要的，后面一个是可选的
          - create_publisher<msg_type>("topic", length)
            - <std_msgs::msg::String>的解释
              - <>是模板参数
            - 对象类型
              - rclcpp::Publisher<std_msgs::msg::String>::SharedPtr
        - 统计发布者数量
          - size_t count_publishers(const std::string &topic_name) const;
      - 订阅者相关
        - 创建订阅者
          - // 创建订阅者
template<typename MessageT, typename CallbackT, typename AllocatorT = std::allocator<void>>
typename rclcpp::Subscription<MessageT, AllocatorT>::SharedPtr
create_subscription(
    const std::string &topic_name,
    const rclcpp::QoS &qos,
    CallbackT &&callback,
    const rclcpp::SubscriptionOptionsWithAllocator<AllocatorT> &options = ...
);
            - callback说明
              - 回调函数必须使用bind，否则将直接触发
              - std::bind(&SubscriberNode::listener_callback, this, std::placeholders::_1)
              - 这里的this和_1最终会传递给listener_callback函数
        - 统计订阅者数量
          - // 统计订阅者数量
size_t count_subscribers(const std::string &topic_name) const;
      - 参数相关
        - // 声明参数
template<typename ParameterT>
void declare_parameter(
    const std::string &name,
    const ParameterT &default_value,
    const rcl_interfaces::msg::ParameterDescriptor &parameter_descriptor = ...
);

// 获取参数
template<typename ParameterT>
ParameterT get_parameter(const std::string &name) const;

// 设置参数
template<typename ParameterT>
void set_parameter(const rclcpp::Parameter &parameter);

// 获取所有参数
std::vector<rclcpp::Parameter> get_parameters(
    const std::vector<std::string> &names) const;
      - 定时器相关
        - 创建定时器
          - // 创建定时器
template<typename DurationRepT = int64_t, typename DurationT = std::milli, typename CallbackT>
typename rclcpp::WallTimer<CallbackT>::SharedPtr
create_wall_timer(
    std::chrono::duration<DurationRepT, DurationT> period,
    CallbackT callback,
    rclcpp::CallbackGroup::SharedPtr group = nullptr);
            - callback参数说明
              - std::bind(&PublisherNode::publish_message, this)
  - TimeBase
    - rclcpp::TimerBase::SharedPtr timer_;是timer的类型
  - Publisher
    - publish(
message
)
      - message
        - 类型
          - std_msgs::msg::String()
        - 设置内容
          - message.data =
  - Subscription
- 项目结构
  - 工作空间结构
    - ros2_ws(workspace)
      - src
        - 源码
      - build
        - def
          - 构建目录
          - CMake 的缓存和配置：CMakeLists.txt 被处理后的结果。
          - 编译对象文件：由 C++ 源代码编译生成的 .o 或 .obj 文件。
          - 临时构建文件：编译器、链接器在构建过程中生成的各种临时文件。
          - 特定于包的构建配置：例如，你为某个包设置的 --cmake-args 或 --ament-cmake-args 会在这里生效。
        - ftr
          - 可丢弃性
          - 开发者关注
      - install
        - core
          - 成品仓库
        - def
          - 安装目录
          - 这是编译完成后的“成品仓库”。colcon 会执行一个“安装”步骤，将构建好的最终成品（可执行文件、库、Python 模块、启动文件、接口定义等）按照 FHS 标准整理并集中放置到这个目录中。
        - ftr
          - 最终产物
          - 环境隔离：这个目录是自包含的。要使用你构建的包，你必须“源”这个目录下的环境设置脚本（source install/setup.bash）。这个操作会将 install 目录的路径添加到 ROS_PACKAGE_PATH 等环境变量中，让 ROS 2 能够找到它们。
          - 可分发性（理论上）：这个 install 目录的结构与系统通过 apt 安装的 ROS 2 包的结构是一致的，这使得从源码构建的包能够无缝地与二进制安装的包一起工作。
      - log
        - 日志目录
  - 源码目录结构
    - include
      - exp
        - include/my_robot_package/node1.hpp
        - include/my_robot_package/utils.hpp
    - src
      - exp
        - src/node1.cpp
        - src/main.cpp
        - src/utils.cpp
    - launch
      - def
        - launch 目录是 ROS2 包中用于存放启动文件的专用目录，它负责定义和管理节点的启动配置。
      - exp
        - launch/demo.launch.py
      - func
        - 集中管理多个节点
        - 参数配置管理
        - 环境空间，命名空间配置
    - config
      - exp
        - config/params.yaml
    - msg
    - srv
    - test
    - CMakeLists.txt
    - package.xml
  - 程序结构
    - exp
      - 发布者 - 订阅者
        - 发布者
        - 订阅者
  - 编译和运行
    - 细分主题 1# 在workspace根目录
colcon build --packages-select my_robot_package
source install/setup.bash

# 运行单个节点
ros2 run my_robot_package node1

# 使用启动文件
ros2 launch my_robot_package demo.launch.py
