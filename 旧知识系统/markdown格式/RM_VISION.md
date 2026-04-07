# RM_VISION
- rm_vision
  - def
    - 总
  - content
    - 细分主题 1
- rm_auto_aim
  - def
    - RoboMaster 装甲板自瞄算法模块
  - content
    - armor_detector
      - def
        - 订阅
          - 相机参数
          - 图像流
        - act
          - 装甲板识别
          - 解算三维位置
        - 输出
          - 识别到的装甲板在输入frame下的三维位置
（一般是以相机光心为原点的相机坐标系）
      - Node
        - DetectorNode
          - including
            - Detector
              - function
                - preprocessImage
                  - ftr
                    - 通过灰度图二值化
                - findLights
                - matchLights
            - PnPSolver
        - NumberClassifier
          - function
            - extractNumbers
            - Classify
      - head
        - armor.hpp
          - Light(struct)
            - int color;
            - cv::Point2f top, bottom;
            - double length;
            - double width;
            - float tilt_angle;
          - Armor(struct)
            - Light pairs part
              - Light left_light, right_light;
              - cv::Point2f center;
              - ArmorType type;
            - Number part
              - cv::Mat number_img;
              - std::string number;
              - float confidence;
              - std::string classfication_result;
        - detector_node.hpp
          - ArmorDetectorNode(class)
            - public
              - ArmorDetectorNode(const rclcpp::NodeOptions & options);
            - ordinary
              - void imageCallback(const sensor_msgs::msg::Image::ConstSharedPtr img_msg);
              - std::unique_ptr<Detector> initDetector();
std::vector<Armor> detectArmors(const sensor_msgs::msg::Image::ConstSharedPtr & img_msg);
              - void createDebugPublishers();
              - void destroyDebugPublishers()
              - void publishMarkers();
            - armor detector
              - std::unique_ptr<Detector> detector_;
            - Detected armors publisher
              - auto_aim_interfaces::msg::Armors armors_msg_;
              - rclcpp::Publisher<auto_aim_interfaces::msg::Armors>::SharedPtr armors_pub_;
            - Visualization marker publisher(可视化标记)
              - visualization_msgs::msg::Marker armor_marker_;
              - visualization_msgs::msg::Marker text_marker_;
              - visualization_msgs::msg::MarkerArray marker_array_;
              - rclcpp::Publisher<visualization_msgs::msg::MarkerArray>::SharedPtr marker_pub_;
            - camara info
              - rclcpp::Subscription<sensor_msgs::msg::CameraInfo>::SharedPtr cam_info_sub_;
              - cv::Point2f cam_center_;
              - std::shared_ptr<sensor_msgs::msg::CameraInfo> cam_info_;
              - std::unique_ptr<PnPSolver> pnp_solver_;
            - image subscription
              - rclcpp::Subscription<sensor_msgs::msg::Image>::SharedPtr img_sub_;
            - debug information
              - std::shared_ptrrclcpp::ParameterEventHandler debug_param_sub_;
              - std::shared_ptrrclcpp::ParameterCallbackHandle debug_cb_handle_;
              - rclcpp::Publisher<auto_aim_interfaces::msg::DebugLights>::SharedPtr lights_data_pub_;
              - rclcpp::Publisher<auto_aim_interfaces::msg::DebugArmors>::SharedPtr armors_data_pub_;
              - image_transport::Publisher binary_img_pub_;
              - image_transport::Publisher number_img_pub_;
              - image_transport::Publisher result_img_pub_;
        - detector.hpp
          - Detector(class)
            - public
              - LightParams(struct)
                - double min_ratio;
                - double max_ratio;
                - double max_angle;
              - ArmorParams(struct)
                - double min_light_ratio;
                - double min_small_center_distance;
                - double max_small_center_distance;
                - double min_large_center_distance;
                - double max_large_center_distance;
                - double max_angle;
              - Detector(const int & bin_thres, const int & color, const LightParams & l, const ArmorParams & a);
              - std::vector<Armor> detect(const cv::Mat & input);
              - cv::Mat preprocessImage(const cv::Mat & input);
std::vector<Light> findLights(const cv::Mat & rbg_img, const cv::Mat & binary_img);
std::vector<Armor> matchLights(const std::vector<Light> & lights);
              - // For debug usage
cv::Mat getAllNumbersImage();
void drawResults(cv::Mat & img);
              - int binary_thres;
int detect_color;
LightParams l;
ArmorParams a;
              - std::unique_ptr<NumberClassifier> classifier;
              - // Debug msgs
cv::Mat binary_img;
auto_aim_interfaces::msg::DebugLights debug_lights;
auto_aim_interfaces::msg::DebugArmors debug_armors;
            - private
              - bool isLight(const Light & possible_light);

bool containLight(
const Light & light_1, const Light & light_2, const std::vector<Light> & lights);

ArmorType isArmor(const Light & light_1, const Light & light_2);
              - std::vector<Light> lights_;
std::vector<Armor> armors_;
        - number_classifier.hpp
          - NumberClassifier(class)
            - public
              - NumberClassifier(
const std::string & model_path, const std::string & label_path, const double threshold,
const std::vectorstd::string & ignore_classes = {});
              - void extractNumbers(const cv::Mat & src, std::vector<Armor> & armors);
              - void classify(std::vector<Armor> & armors);
              - double threshold;
            - private
              - cv::dnn::Net net_;
              - std::vectorstd::string class_names_;
              - std::vectorstd::string ignore_classes_;
        - pnp_solver.hpp
          - PnPSolver
            - public
              - PnPSolver(
const std::array<double, 9> & camera_matrix,
const std::vector<double> & distortion_coefficients);
              - // Get 3d position
bool solvePnP(const Armor & armor, cv::Mat & rvec, cv::Mat & tvec);
              - // Calculate the distance between armor center and image center
float calculateDistanceToCenter(const cv::Point2f & image_point);
            - private
              - cv::Mat camera_matrix_;
cv::Mat dist_coeffs_;
              - // Unit: mm
static constexpr float SMALL_ARMOR_WIDTH = 135;
static constexpr float SMALL_ARMOR_HEIGHT = 55;
static constexpr float LARGE_ARMOR_WIDTH = 225;
static constexpr float LARGE_ARMOR_HEIGHT = 55;
              - // Four vertices of armor in 3d
std::vectorcv::Point3f small_armor_points_;
std::vectorcv::Point3f large_armor_points_;
      - code
    - armor_tracker
      - 订阅
        - 识别节点发布的装甲板三维位置
        - 机器人的坐标转换信息
      - act
        - 装甲板三维位置 -(变换)> 指定惯性系（一般是以云台中心为原点，IMU上电时的Yaw朝向为X轴的惯性系）
        - 装甲板目标 -(送入)> 跟踪器
      - 输出
        - 跟踪机器人
在
指定惯性系下的
状态
    - auto_aim_interfaces
      - 定义
        - 识别节点接口
        - 处理节点接口
        - 用于debug的信息
    - auto_aim_bringup
      - content
        - 启动识别节点
        - 处理节点
- rm_gimbal(万向坐标系)_description
  - def
    - RoboMaster 视觉自瞄系统所需的 URDF（Unified Robot Description Format）
    - 机器人云台描述文件
- rm_serial_driver
  - def
    - 视觉系统与电控系统的串口通讯模块
- ros2_hik_camara
  - def
    - A ROS2 packge for Hikvision USB3.0 industrial camera
- Other
  - rm_buff
    - 能量机关自动瞄准算法模块
  - rm_auto_record
    - 自动包录制模块
