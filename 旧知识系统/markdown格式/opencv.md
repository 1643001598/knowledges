# opencv
- modules
  - highgui
    - resizeWindow()
    - imshow
    - namedWindow
    - createTrackbar(
trackbarname,
winname,
...
)
    - getTrackBarPos
    - setMouseCallback
    - waitKeyEx
  - imgproc
    - categories
      - def
        - 像素级的操作
      - 阈值处理
        - threshold(
src
dest
thresh
maxval
type
)
          - 设置参数
            - thresh(阈值)
            - maxval(最大值，使用THRESH_BINARY等类型时的最大值)
            - type(阈值化类型)
          - categories
            - THRESH_BINARY
二进制阈值化
（非大即小）
              - cv::Mat src, dst;
// 像素值 > thresh 时设为 maxval，否则设为 0
cv::threshold(src, dst, 127, 255, cv::THRESH_BINARY);

效果
如果 src(x,y) > thresh: dst(x,y) = maxval
否则: dst(x,y) = 0
            - THRESH_BINARY_INV
反二进制阈值化
（=二进制阈值化的结果反过来）
              - cv::threshold(src, dst, 127, 255, cv::THRESH_BINARY_INV);

效果
如果 src(x,y) > thresh: dst(x,y) = 0
否则: dst(x,y) = maxval
            - THRESH_TRUNC (截断阈值化)
（大的赋值为阈值，小的不变）
              - cv::threshold(src, dst, 127, 255, cv::THRESH_TRUNC);

效果
如果 src(x,y) > thresh: dst(x,y) = thresh
否则: dst(x,y) = src(x,y)
            - THRESH_TOZERO (阈值化为0)
（大的不变，小的变为零）
              - cv::threshold(src, dst, 127, 255, cv::THRESH_TOZERO);

效果
如果 src(x,y) > thresh: dst(x,y) = src(x,y)
否则: dst(x,y) = 0
            - THRESH_TOZERO_INV (反阈值化为0)
（结果调换）
              - cv::threshold(src, dst, 127, 255, cv::THRESH_TOZERO_INV);

效果
如果 src(x,y) > thresh: dst(x,y) = 0
否则: dst(x,y) = src(x,y)
        - 自适应阈值处理
          - void cv::adaptiveThreshold(
    InputArray src,
    OutputArray dst,
    double maxValue,
    int adaptiveMethod,
    int thresholdType,
    int blockSize,
    double C
);
参数说明:
adaptiveMethod:
ADAPTIVE_THRESH_MEAN_C: 使用邻域均值
ADAPTIVE_THRESH_GAUSSIAN_C: 使用高斯加权和
blockSize: 邻域大小（奇数）
C: 从均值或加权均值中减去的常数
        - 二值化
        - Otsu's方法
        - inRange
      - 图像滤波
        - 均值模糊
          - boxFilter(
src
dst
ddepth
ksize
anchor = Point(-1, -1)
normalize = true
borderType = BORDER_DEFAULT
)
            - 设置参数
              - ddepth
                - 输出图像的期望深度
(-1:相同；CV_8U, CV_32F)
                - normalize(标准化标志)
true：与blur一样
false：不除面积，用于某些特定的计算机视觉算法中，比如在计算方差或协方差时，可以快速计算区域和。
          - blur(
src
dst
ksize
anchor = Point(-1, -1)
borderType = BORDER_DEFAULT
)
(归一化方框滤波)
            - 设置参数
              - ksize
                - 模糊核的大小：Size(w, h)
                - 或者整数
              - anchor
                - 锚点(（-1, -1）表示锚点位于核中心)
              - borderType
                - BORDER_DEFAULT
                  - 处理边框：镜像
                - BORDER_CONSTANT
                  - 处理边框：用常数值填充
        - 高斯加权平均
          - GaussianBlur(
src
dst
ksize
sigmaX
sigmaY = 0
borderType
)
            - 设置参数
              - sigmaX/Y
                - 标准差
                - sigmaY = 0的时候等于sigmaX
            - 核心工作原理
              - 使用一个符合正态分布的卷积核进行加权平均
        - 中值滤波
          - medianBlur(
src
dst
ksize
)
          - def
            - 选择窗口内的像素 排序后的中值的像素值作为其值
        - 双边滤波
          - bilateralFilter(
src
dst
d
sigmaColor
sigmaSpace
borderType = BORDER_DEFAULT
)
      - 形态学操作
        - 膨胀
          - dilate(
src, dst
kernal
anchor
iterations = 1
borderType
const cv::Scalar& borderValue = cv::morphologyDefaultBorderValue()
)
            - iteration
              - 执行的次数
            - borderValue 在borderType为BORDER_CONSTANT时有效
              - 图像边界的填充值（颜色）
          - 选择结构元素中的最大值作为锚点的值
          - (untitled)
          - core
            - B_h(z) ∩ A 非空 -> 交集不空，即有共同点
        - 腐蚀
          - erode
            - 同dilate
          - 选择结构元素中的最小值作为锚点的值
          - (untitled)
          - core
            - B(z) ∈ A，也就是B必须存在于A内
        - 获取卷积核
          - getStructuringElement(
shape
ksize
anchor = Point(-1, -1)
)
            - shape参数选项
              - MORPH_RECT
              - MORPH_ELLIPSE
              - MORPH_CROSS
        - 高级形态学操作
          - morphologyEx(
src, dst
op
kernel
anchor
iterations
borderType
borderValue
)
            - 开操作
              - op = MORPH_OPEN
              - 先腐蚀再膨胀
              - (untitled)
            - 闭操作
              - op = MORPH_CLOSE
              - 先膨胀再腐蚀
              - (untitled)
            - 梯度运算 - 获取物体边缘
              - MORPH_GRADIENT
            - 顶帽运算 - 原图与开运算结果的差
              - MORPH_TOPHAT
            - 黑帽运算 - 闭运算结果与原图的差
              - MORPH_BALCKHAT
          - 功能
          - 梯度and开闭操作
            - 基本形态学梯度
              - core
                - 膨胀值 - 腐蚀值
（膨胀 = 外边界 + 原物体；腐蚀 = 原物体 - 内边界，上式 = 外边界 + 内边界）
            - 内部梯度
              - core
                - 原图 - 腐蚀值 = 内边界
            - 外部梯度
              - core
                - 膨胀值 - 原图 = 外边界
            - 开操作
              - core
                - 腐蚀(膨胀) = 腐蚀(外边界 + 原物体) = 腐蚀(外边界) + 腐蚀(原物体) = 腐蚀(外边界) + 原物体 - 内边界？？？
            - 顶帽运算
              - 原物体 - 开操作 = 内边界 - 腐蚀(外边界)
            - 黑帽运算
              - 闭操作 - 原物体
            - 基本结构
              - 内部 - 边界 - 外部
内部 - 内边界 - 外边界 - 外部
              - 膨胀使得物体的边界向外拓展
腐蚀使得物体的边界向内收缩
      - 通道处理
        - 分割通道
          - vector<Mat> channels
split(image, channels)
        - 混合通道
          - merge()
        - 颜色空间转换
          - cvtColor(img, dst, type)
          - type的选项
            - COLOR_BGR2GRAY
            - COLOR_BGR2HSV
            - COLOR_BGR2Lab
            - ...
        - 检查通道数
          - .size()
            - 长宽：[1436 x 624]
          - .channels()
            - 通道数
          - .depth()
            - 每一个像素的数值类型
      - 图像变换
        - 等距变换（欧式/刚体变换）
（R为正交旋转矩阵
    t:平移向量）
          - def
            - 任意两点间距离不变，形状大小不变
也就是平移+旋转
            - 3个自由度
        - 相似变换
          - def
            - 保持形状不变（大小，方向，位置可变），可分解为等比例缩放，平移，旋转的组合
            - 4个自由度
        - 仿射变换
（A：可逆矩阵）
          - def
            - 保持直线和平行线不变
相比于相似变换，多了一个切变
            - 6个自由度
          - categories
            - 不变
            - 缩放
              - 等比例
              - 不等比例
            - 平移
            - 旋转
            - 翻转
            - 切边
          - 获取仿射变换矩阵
            - getAffineTransform(
const Point2f src[]
const Point2f det[]
)
(const的作用是保护参数不被修改)
            - getRotationMatrix2D(
Point2f center
angle
scale
)
          - 应用仿射变换
            - warpAffine(
src, dst
M
dsize
flags = INTER_LINEAR
borderMode
borderValue
)
              - M
                - 2x3的变换矩阵
              - dsize
                - 输出图像尺寸
              - flags
                - INTER_NEAREST：最近邻插值
                - INTER_LINEAR：双线性插值（默认）
                - INTER_CUBIC：双三次插值
                - INTER_LANCZOS4：Lanczos插值
              - borderMode
                - BORDER_CONSTANT
                - BORDER_REPLICATE
                - BORDER_REFLECT
                - BORDER_WRAP
                - BORDER_TRANSPARENT
        - 投影变换（MVP/射影变换/单应性变换）
          - def
            - 原理：光线是平行地投射到投影平面上。它完全忽略了物体与相机之间的距离。
          - categories
            - 透视投影
              - def
                - 完全模拟人眼或相机的工作原理。光线从一个点（视点或相机位置）出发，穿过一个平面（投影平面），最终形成一个锥形的可视区域（视锥体）。
                - 8个自由度
                - (untitled)
                - (untitled)
              - 获得透视变换所需的矩阵
                - 细分主题 1
              - warpPerspective
            - 正交（平行）投影
              - def
                - 原理：光线是平行地投射到投影平面上。它完全忽略了物体与相机之间的距离。
          - 相机相关
            - 小孔成像模型
              - 点
                - 相机坐标系下物体的坐标 P = (X, Y, Z)
                - 相机坐标系下相机平面下物体像的坐标:
P' = (X', Y', F), X' = f * X / Z; Y' = f * Y / Z
这里的坐标经过了对称，在自由度上与原坐标等价
                - 归一化成像平面下物体像的坐标：
Ps = (Xs, Ys, 1), Xs = X / Z; Ys = Y / Z
                - 像素坐标系下物体像的坐标：
K [Xs, Ys, 1]
其中K = 
[
[fx, 0, u0],
[0, fy, v0],
[0, 0, 1],
]
fx = f / dx; dx是每个像素在x方向的物理尺寸
            - 相机标定
              - def
                - 相机需要标定内参矩阵K，外参矩阵T，以及畸变参数k1 , k2 , k3 , p1 , p2
                - K
                - 畸变相关
              - 目的
                - 得到相机内参和畸变参数
              - functions
                - 核心标定函数
                  - findChessboardCorners / findCirclesGrid(
image
patternSize
corners
CALIB_CB_ADAPTIVE_THRESH
) -> corners()
                    - def
                      - 作用：用于在标定板图像中自动寻找角点（如棋盘格）或圆心。
                      - 说明：这是标定流程的第一步，用于获取2D图像点。
                    - args
                      - 输入参数：
                        - image：棋盘格图像，彩色图或灰度图；
                        - patternSize：棋盘格内每行每列的角点数量
                        - flags：默认参数为：CALIB_CB_ADAPTIVE_THRESH + CALIB_CB_NORMALIZE_IMAGE
                        - CALIB_CB_ADAPTIVE_THRESH使用自适应阈值将图像转换为黑色和白色，而不是固定阈值级别（根据平均图像亮度计算）。
                        - CALIB_CB_NORMALIZE_IMAGE在应用固定或自适应阈值之前使用equalizeHist对图像伽玛进行归一化。
                        - CALIB_CB_FILTER_QUADS使用附加标准（如轮廓面积、周长、正方形形状）过滤掉在轮廓检索阶段提取的假四边形。
                        - CALIB_CB_FAST_CHECK对寻找棋盘角的图像进行快速检查，如果未找到则快捷调用。当没有观察到棋盘时，这可以大大加快退化条件下的调用。
                      - 输出参数：
                        - corners：找到的角点（）
                  - TermCriteria criteria(TermCriteria::EPS + TermCriteria::MAX_ITER, 30, 0.001);

cornerSubPix(
img, 
corners, 
Size(11, 11)
size(-1, -1)
criteria
)
                    - def
                      - 作用：将找到的角点位置优化到亚像素精度，提高标定精度。
                      - 说明：通常在findChessboardCorners之后使用。
                      - args
                        - cv::InputArray image, // 输入图像
                        - cv::InputOutputArray corners, // 角点（既作为输入也作为输出）
                        - cv::Size winSize, // 区域大小为 NXN; N=(winSize*2+1)
                        - cv::Size zeroZone, // 类似于winSize，但是总具有较小的范围，Size(-1,-1)表示忽略
                        - cv::TermCriteria criteria // 停止优化的标准
                  - double cv::calibrateCamera(
InputArrayOfArrays  objectPoints,
InputArrayOfArrays  imagePoints,
Size   imageSize,
InputOutputArray   cameraMatrix,
InputOutputArray   distCoeffs,
OutputArrayOfArrays    rvecs,
OutputArrayOfArrays    tvecs,
int    flags = 0,

TermCriteria criteria = TermCriteria(TermCriteria::COUNT+TermCriteria::EPS, 30, DBL_EPSILON) 
);
                    - args
                      - objectPoints：标定板角点三维坐标；
                      - imagePoints ：图像上对应的二维坐标；
                      - imageSize   ：图像尺寸；
                      - cameraMatrix：相机内参矩阵；
                      - distCoeffs  ：镜头失真系数；
                      - rvecs       ：外参-旋转矩阵；
                      - tvecs       ：外参-平移矩阵；
                      - flags       ：标定参数；
                      - criteria    ：标定迭代终止条件。
                - 校正函数
                  - 细分主题 1
                - PnP相关
                  - solvePnP
                    - 两种形式
                      - bool solvePnP(
    InputArray objectPoints,      // 世界坐标系下的3D点集
    InputArray imagePoints,       // 图像坐标系下对应的2D点集
    InputArray cameraMatrix,      // 相机的内参矩阵
    InputArray distCoeffs,        // 相机的畸变系数
    OutputArray rvec,             // 输出的旋转向量
    OutputArray tvec,             // 输出的平移向量
    bool useExtrinsicGuess = false, 
    int flags = SOLVEPNP_ITERATIVE
);
                      - bool solvePnP(
    InputArray objectPoints,
    InputArray imagePoints,
    InputArray cameraMatrix,
    InputArray distCoeffs,
    OutputArray rvec,
    OutputArray tvec,
    OutputArray jacobian = noArray(),
    int flags = SOLVEPNP_ITERATIVE
);
                    - args
                      - 内参矩阵
            - PnP算法（Perspective-n-Point）
              - def
                - 根据n个三维世界坐标系的点拟合相机位姿（旋转R与平移t）
              - 函数
                - solvePnP()
      - 轮廓查找
        - findCounter
          - categories
            - findCounter(
img
ArrayOfArray counters
hierarchy
int mode
int method
Point offset = Point()
)
            - findCounter(
img
ArrayOfArrays counters
int mode
int method
Point offset = Point()
)
          - 参数
            - counters
              - 类型
                - vector<vector<Point>>
            - hierarchy
              - 类型
                - vector<Vec4i>
                - <Vec4i>含义
                  - [next, previous, first_child, parent]
                    - next: 同一层级的下一个轮廓索引
                    - previous: 同一个层级的上一个轮廓索引
                    - first_child: 第一个子轮廓索引
                    - parent: 父轮廓索引
            - mode
              - categories
                - RETR_EXTERNAL
                  - 只检测最外层轮廓
                - RETR_LIST
                  - 检测所有轮廓，不建立层次关系
                - RETR_CCOMP
                  - 检测所有轮廓，建立两层层次结构
                - RETR_TREE
                  - 检测所有轮廓，建立完整层次树
            - method
              - categories
                - CHAIN_APPROX_NONE
                  - 存储所有轮廓点
                - CHAIN_APPROX_SIMPLE
                  - 压缩水平，垂直，对角线，方向点
                - CHAIN_APPROX_TC89_L1
                  - 利用Teh-Chin链近似算法L1
                - CHAIN_APPROX_TC89_KCOS
                  - 利用Teh-Chin链近似算法 KCOS
            - offset
              - def
                - 偏移量
          - attention
            - 输入必须是八位单通道二值图像
            - 函数会修改输入图像
        - drawContours(
img
ArrayOfArray contours
int contourIdx
const Scalar& color
int thickness = 1
int lineType = LINE_8
hierarchy = noArray()
int maxLevel = INT_MAX
Point offset = Point()
)
          - 参数
            - contourIdx
              - 轮廓索引（负值则绘制所有轮廓）
            - color
              - Scalar(B, G, R)
            - thickness
              - 线宽
                - 负值则填充内部
            - lineType
              - LINE_4
                - 4连通线
              - LINE_8
                - 8连通线
              - LINE_AA
                - 抗锯齿线
            - maxLevel
              - 最大绘制层级
        - 计算轮廓面积
          - counterArea(
counter
oriented = False
)
            - oriented
              - True: 返回有向面积，顺时针轮廓为负
        - 拟合最小矩形
          - minAreaRect(
points
)
            - 返回值:RotatedRect
        - 拟合最小正矩形
          - boundingRect
        - 拟合外接椭圆
          - fitEcllipse -> 椭圆的内接旋转矩形
        - 找到最小面积的包含圆
          - minEnclosingCircle()
      - 作画
        - circle()
        - line()
      - 颜色空间转换
        - cvtColor()
        - split()
          - 通道拆分
        - subtract()
          - 将两张矩阵的每个元素相减
  - imgcodecs
    - imread
    - imwrite
  - vedioio
    - VideoCapture类
      - 存储一帧视频
        - cap >> frame
    - VideoWriter
  - ML
  - DNN
  - calib3d
    - 相机标定
  - tracking
    - 追踪？
  - 细分主题 9
- 基本数据类型
  - 基本数据类型
    - Mat
      - def
        - 保存图像的矩阵类型
      - 属性
        - 维度相关
          - dims(维度)
          - rows
          - cols
          - size
        - 数据类型相关
          - type()
            - CV_8UC3
          - depth()
            - CV_8U
          - channels()
            - 3
        - 内存管理
          - flags
          - size
          - step
          - u
            - 指向UMat数据的指针
        - 内存管理标志
          - empty()
          - isContinuous()
          - isSbumatrix()
      - 方法
        - 构造方法
          - Mat()
          - Mat(rows, cols, type)
          - Mat(size, type)
          - (从数组构造)Mat(rows, cols, type, void* data) 
(void*: 指向任意类型数据的指针)
          - // 复制构造函数
cv::Mat(const Mat& m);
          - // 从子区域构造
cv::Mat(const Mat& m, const Rect& roi);
          - Mat mat = (Mat_<uchar>(3, 4) << 1, 2, 3, 4,
                                    5, 6, 7, 8,
                                    9, 10, 11, 12);
            - 核心构造逻辑
              - cv::Mat mat = (cv::Mat_<数据类型>(行, 列) << 数据)
        - 数据访问方法
          - at(row, col)
        - 矩阵操作
          - reshape(cn, rows = 0)
          - t()
          - (求逆)inv(method  = DECOMP_LU)
          - mul(m)
          - (设置所有元素值)setTo(value)
        - 统计信息
          - sum()
          - mean()
          - minMaxLoc(minVal, maxVal,)???
          - constNonZero()
        - 图像通道分离
          - split(Mat, Mat)
    - Point
      - def
        - 表示点数据
      - categories
        - // 常用的 Point 类型
Point2i point_int;        // 整数坐标点 (最常用)
Point2f point_float;      // 浮点数坐标点

Point2d point_double;     // 双精度浮点数坐标点
Point3i point3d_int;      // 三维整数坐标点
Point3f point3d_float;    // 三维浮点数坐标点
Point3d point3d_double;   // 三维双精度浮点数坐标点
// 实际上，Point 是 Point2i 的别名
Point point;  // 等价于 Point2i
      - 构造函数
        - Point p1
        - Point p2(10, 20)
        - Point p3 = Point(30, 40)
        - Point p4(p3)
(拷贝构造函数)
        - Point pd(static_cast<int>(pf.x), static_cast<int>(pf.y));  // 类型转换
      - 成员变量
        - x/y
        - Point.inside(Rect)
      - 运算
        - .dot
        - .cross
    - Scalar
      - def
        - Scalar(B, G, R, alpha)
颜色数据
      - 构造函数
        - Scalar s1
        - Scalar s2(255)
        - // 3. 多值构造函数
cv::Scalar s3(0, 0, 255);     // B=0, G=0, R=255
cv::Scalar s4(0, 0, 255, 0);  // B=0, G=0, R=255, Alpha=0

// 4. 使用大括号初始化
cv::Scalar s5{100, 200, 50};
      - 颜色常量
        - .all(值)
    - Size
      - def
        - 大小数据
      - categories
        - ...
      - 属性
        - width
        - height
      - 基本操作
        - .area()
        - .empty()
    - Rect
      - def
        - 正矩形
      - 构造
        - Rect(x, y, width, height)
(起始点坐标，宽度，高度)
        - Rect(Point, Size)
        - Rect(Point1, Point2)
      - 属性
        - x,y, width, height
        - 左上角点：.tl -> Point
        - .br
        - .size -> Size
        - center -> Point
        - .area()
      - 操作
        - .contain(Point)
    - RotatedRect
      - def
        - 旋转矩形
      - 属性
        - center Point2f
        - size Size2f
        - angle float
（单位：度 [-90, 90]）
      - 构造函数
        - RotatedRect();
RotatedRect(const Point2f& center, const Size2f& size, float angle);
RotatedRect(const Point2f& point1, const Point2f& point2, const Point2f& point3);
      - 方法
        - 获取顶点
          - RotatedRect rect(Point2f(100, 100), Size2f(200, 100), 45.0);

// 获取四个顶点
Point2f vertices[4];
rect.points(vertices);
        - 获取边界矩形
          - // 获取整数边界矩形（可能不完全包含旋转矩形）
Rect boundingRect = rotatedRect.boundingRect();

// 获取浮点边界矩形（更精确）
Rect2f boundingRect2f = rotatedRect.boundingRect2f();
  - 视频
    - VideoCapture
      - 构造函数
        - // 从文件创建
cv::VideoCapture cap("video.mp4");
        - // 从摄像头创建
cv::VideoCapture cap(0);  // 0表示默认摄像头
        - // 从URL创建（需要FFmpeg支持）
cv::VideoCapture cap("rtsp://192.168.1.1/live");
      - 方法
        - 视频流相关
          - cap.open("xxx.mp4") -> bool
            - 检查能否打开
            - 绑定到cap
          - .read(frame) -> bool
            - 读取帧写入frame
            - 如果没帧，就返回0
          - cap >> frame
            - 用Mat.empty()来管理结束标志
        - 视频信息相关
          - 帧率
            - cap.get(cv::CAP_PROP_FPS);
          - 帧宽度
            - cv::CAP_PROP_FRAME_WIDTH
          - 帧高度
            - cv::CAP_PROP_FRAME_HEIGHT
          - 总帧数
            - cv::CAP_PROP_FRAME_COUNT
          - 当前位置(毫秒)
            - cv::CAP_PROP_POS_MSEC
        - 设置视频属性
          - 跳转到100帧
            - cap.set(cv::CAP_PROP_POS_FRAMES, 100);
          - 跳转到秒数
            - cv::CAP_PROP_POS_MSEC（单位：毫秒）
          - 设置帧宽/高
            - CAP_PROP_FRAME_WIDTH/HEIGHT
        - 释放资源
          - cap.release()
      - 属性
        - .isOpened()
  - 文件存储
    - FileStorage
      - 初始化
        - cv::FileStorage fs("config.yml", cv::FileStorage::WRITE);
      - 写入
        - 写入数据
          - fs << "int_value" << 100;
fs << "float_value" << 3.14f;
fs << "string_value" << "Hello OpenCV";
fs << "current_time" << cv::getTickCount();
        - 写数组
          - fs << "array" << "[" << 1.1 << 2.2 << 3.3 << "]";
        - 写映射
          - fs << "mapping" << "{" << "x" << 100 << "y" << 200 << "width" << 640 << "height" << 480 << "}";
        - 写Mat对象
          - cv::Mat cameraMatrix = (cv::Mat_<double>(3,3) << 1000, 0, 320, 0, 1000, 240, 0, 0, 1);
cv::Mat distCoeffs = (cv::Mat_<double>(5,1) << 0.1, 0.01, -0.001, 0, 0);
fs << "camera_matrix" << cameraMatrix;
fs << "distortion_coefficients" << distCoeffs;
      - 读取
        - 检查文件打开
          - if (!fs.isOpened()) {
    std::cerr << "Failed to open config.yml" << std::endl;
    return -1;
}
        - 读数据
          - int int_value;
float float_value;
std::string string_value;
double current_time;

fs["int_value"] >> int_value;
fs["float_value"] >> float_value;
fs["string_value"] >> string_value;
fs["current_time"] >> current_time;
        - 读Mat
          - cv::Mat cameraMatrix, distCoeffs;
fs["camera_matrix"] >> cameraMatrix;
fs["distortion_coefficients"] >> distCoeffs;
      - 释放文件
        - // 释放文件
fs.release();
    - (untitled)
- 拓展
  - 图像金字塔
    - def
      - 图像金字塔是图像中多尺度表达的一种，最主要用于图像的分割，是一种以多分辨率来解释图像的有效但概念简单的结构。图像金字塔最初用于机器视觉和图像压缩，一幅图像的金字塔是一系列以金字塔形状排列的分辨率逐步降低，且来源于同一张原始图的图像集合。其通过梯次向下采样获得，直到达到某个终止条件才停止采样。金字塔的底部是待处理图像的高分辨率表示，而顶部是低分辨率的近似。我们将一层一层的图像比喻成金字塔，层级越高，则图像越小，分辨率越低。
    - categories
      - 高斯金字塔
        - def
          - 用来向下/降采样，主要的图像金字塔
        - core
          - 丢失细节获得更小的图
      - 拉普拉斯金字塔
        - core
          - 记录丢失的细节
        - def
          - 用来从金字塔低层图像重建上层未采样图像，在数字图像处理中也即是预测残差，可以对图像进行最大程度的还原，配合高斯金字塔一起使用。
    - functions
      - pyrDown
        - 创建高斯金字塔的下一层
        - def
          - 模糊： 首先用一个高斯核（类似于一个模糊滤波器）对图像进行卷积，这可以防止后续缩小操作产生锯齿和混叠效应。
          - 降采样： 然后，丢弃每隔一个像素（行和列），将图像的宽和高都缩小为原来的一半。因此，总的像素数变为原来的四分之一。
      - pyrUp
        - 为创建拉普拉斯金字塔做准备
        - def
          - 升采样： 将图像的宽度和高度各扩大一倍。新增的像素通常先用0（黑色）填充。
          - 模糊： 再用同一个高斯核（但权重可能不同）对放大后的图像进行卷积，以填补新增像素的值，使其看起来平滑。注意：这个模糊操作并不能找回在 pyrDown 中丢失的细节！
