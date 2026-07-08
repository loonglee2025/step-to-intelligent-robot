# OpenCV 核心功能参考手册
> C++ / ROS 2 开发常用库

---

## 模块索引

| 模块 | 代表函数 | 典型用途 |
|------|----------|----------|
| [图像读写与显示](#一图像读写与显示) | `imread / imwrite / VideoCapture` | 文件 I/O、摄像头采集 |
| [颜色空间与通道](#二颜色空间与通道操作) | `cvtColor / inRange / split` | 颜色提取、HSV 目标检测 |
| [滤波与形态学](#三图像滤波与形态学) | `GaussianBlur / Canny / dilate` | 去噪、边缘检测、掩码处理 |
| [几何变换](#四几何变换) | `resize / warpPerspective` | 缩放、旋转、透视矫正 |
| [特征检测与轮廓](#五特征检测与轮廓) | `findContours / HoughLines` | 形状分析、直线圆检测 |
| [绘图与标注](#六绘图与标注) | `rectangle / putText / line` | 可视化、调试标注 |
| [图像算术与掩码](#七图像算术与掩码) | `threshold / bitwise_and` | 二值化、ROI 提取 |
| [相机标定与3D视觉](#八相机标定与3d视觉) | `calibrateCamera / solvePnP` | 标定、位姿估计 |

---

## 一、图像读写与显示

### `cv::imread()`

从指定路径读取图像文件，返回 `cv::Mat` 对象。支持 PNG、JPEG、BMP、TIFF 等格式。

```cpp
cv::Mat cv::imread(
    const std::string &filename,
    int flags = IMREAD_COLOR
);
```

| 参数 | 说明 |
|------|------|
| `filename` | 图像文件路径 |
| `flags` | `IMREAD_COLOR`（BGR 彩色）/ `IMREAD_GRAYSCALE`（灰度）/ `IMREAD_UNCHANGED`（含 Alpha） |

> 读取失败时返回空 Mat，使用前务必用 `mat.empty()` 检查。

---

### `cv::imwrite()`

将 `cv::Mat` 保存为图像文件，格式由文件后缀决定。

```cpp
bool cv::imwrite(
    const std::string &filename,
    cv::InputArray img,
    const std::vector<int> &params = {}
);
```

| 参数 | 说明 |
|------|------|
| `filename` | 输出文件路径（如 `"out.jpg"`） |
| `params` | 编码参数，如 `{IMWRITE_JPEG_QUALITY, 95}` |

---

### `cv::imshow()` / `cv::waitKey()`

在命名窗口中显示图像。`waitKey(0)` 阻塞等待按键，参数为毫秒超时（0 = 永久等待）。

```cpp
cv::imshow("window_name", mat);
int key = cv::waitKey(0);   // 返回按键 ASCII 码
cv::destroyAllWindows();    // 销毁所有窗口
```

> ROS 2 节点中慎用 `imshow`，建议通过话题发布图像用 rviz2 显示。

---

### `cv::VideoCapture`

打开视频文件或摄像头设备，逐帧读取图像。

```cpp
cv::VideoCapture cap(0);    // 0=默认摄像头，或传视频路径
if (!cap.isOpened()) return -1;

cv::Mat frame;
while (cap.read(frame)) {
    cv::imshow("cam", frame);
    if (cv::waitKey(30) == 27) break;   // ESC 退出
}
```

---

### `cv::VideoWriter`

将帧序列编码写入视频文件。

```cpp
cv::VideoWriter writer(
    "output.mp4",
    cv::VideoWriter::fourcc('m','p','4','v'),
    30.0,               // fps
    cv::Size(640, 480)  // 帧尺寸
);
writer.write(frame);
```

---

## 二、颜色空间与通道操作

### `cv::cvtColor()`

在不同颜色空间之间转换图像，是最常用的预处理操作之一。

```cpp
cv::Mat gray, hsv;
cv::cvtColor(src, gray, cv::COLOR_BGR2GRAY);
cv::cvtColor(src, hsv,  cv::COLOR_BGR2HSV);
```

| 常用转换码 | 说明 |
|------------|------|
| `COLOR_BGR2GRAY` | 彩色→灰度（OpenCV 默认 BGR 顺序） |
| `COLOR_BGR2HSV` | BGR→HSV（色相/饱和/明度） |
| `COLOR_BGR2RGB` | BGR→RGB（用于 ROS / PIL 互转） |
| `COLOR_GRAY2BGR` | 灰度→三通道 BGR |

---

### `cv::split()` / `cv::merge()`

将多通道图像分离为单通道，或将多个单通道合并为多通道图像。

```cpp
std::vector<cv::Mat> channels;
cv::split(bgr_img, channels);   // channels[0]=B, [1]=G, [2]=R

// 修改某通道后重新合并
channels[2] = cv::Mat::zeros(channels[2].size(), CV_8U);
cv::Mat result;
cv::merge(channels, result);
```

---

### `cv::inRange()`

在 HSV 空间中按颜色范围生成二值掩码，常用于颜色目标检测。

```cpp
cv::Mat hsv, mask;
cv::cvtColor(src, hsv, cv::COLOR_BGR2HSV);

// 提取绿色区域
cv::inRange(hsv,
    cv::Scalar(35, 50, 50),    // 下界 (H, S, V)
    cv::Scalar(85, 255, 255),  // 上界
    mask);

cv::Mat result;
src.copyTo(result, mask);      // 只保留掩码区域
```

---

## 三、图像滤波与形态学

### `cv::GaussianBlur()`

高斯模糊，常用于去噪或边缘检测前的预处理。

```cpp
cv::GaussianBlur(src, dst,
    cv::Size(5, 5),  // 核尺寸（必须为奇数）
    0                // sigmaX=0 时自动计算
);
```

> 核越大，模糊越强。边缘检测前必须先模糊，以减少噪声干扰。

---

### `cv::medianBlur()`

中值滤波，对椒盐噪声效果优于高斯滤波，能较好保留边缘。

```cpp
cv::medianBlur(src, dst, 5);   // 5=核尺寸（奇数）
```

---

### `cv::Canny()`

Canny 边缘检测算法，输出边缘二值图。

```cpp
cv::Mat edges;
cv::Canny(gray_img, edges,
    50,   // 低阈值
    150   // 高阈值
);
```

> 低:高阈值比通常取 1:2 或 1:3。调用前建议先做高斯模糊。

---

### `cv::dilate()` / `cv::erode()`

膨胀使白色区域扩大，腐蚀使白色区域缩小。组合使用可实现开运算（去噪）、闭运算（填洞）。

```cpp
cv::Mat kernel = cv::getStructuringElement(
    cv::MORPH_RECT, cv::Size(3, 3));

cv::Mat dilated, eroded;
cv::dilate(mask, dilated, kernel);
cv::erode(mask,  eroded,  kernel);
```

---

### `cv::morphologyEx()`

高级形态学运算，封装了开运算、闭运算、形态学梯度等组合操作。

```cpp
cv::morphologyEx(src, dst,
    cv::MORPH_OPEN,    // 开运算 = 先腐蚀再膨胀（去除小噪点）
    kernel);
```

| 操作码 | 说明 |
|--------|------|
| `MORPH_OPEN` | 开运算，去除小噪点 |
| `MORPH_CLOSE` | 闭运算，填充小孔洞 |
| `MORPH_GRADIENT` | 形态学梯度，提取边缘 |
| `MORPH_TOPHAT` | 顶帽变换，提取亮细节 |

---

## 四、几何变换

### `cv::resize()`

缩放图像到指定尺寸或比例。

```cpp
cv::Mat resized;

// 指定目标尺寸
cv::resize(src, resized, cv::Size(640, 480));

// 按比例缩放（fx=0.5 缩小一半）
cv::resize(src, resized, cv::Size(), 0.5, 0.5, cv::INTER_LINEAR);
```

| 插值方法 | 适用场景 |
|----------|----------|
| `INTER_LINEAR` | 放大（双线性，默认） |
| `INTER_AREA` | 缩小（保持细节，推荐） |
| `INTER_CUBIC` | 高质量放大（速度较慢） |
| `INTER_NEAREST` | 最近邻（速度最快） |

---

### `cv::warpAffine()`

仿射变换，用于旋转、平移、缩放等操作。

```cpp
// 绕中心旋转 45 度，缩放因子 1.0
cv::Point2f center(src.cols / 2.0, src.rows / 2.0);
cv::Mat M = cv::getRotationMatrix2D(center, 45.0, 1.0);

cv::Mat rotated;
cv::warpAffine(src, rotated, M, src.size());
```

---

### `cv::warpPerspective()`

透视变换，用于文档扫描矫正、鸟瞰图变换等四点透视校正。

```cpp
std::vector<cv::Point2f> src_pts = { /* 4 个源角点 */ };
std::vector<cv::Point2f> dst_pts = { /* 目标矩形顶点 */ };

cv::Mat H = cv::getPerspectiveTransform(src_pts, dst_pts);
cv::Mat warped;
cv::warpPerspective(src, warped, H, cv::Size(400, 300));
```

---

### `cv::flip()` / `cv::rotate()`

翻转或以 90° 为单位旋转图像。

```cpp
cv::flip(src, dst, 0);   // 0=上下翻转, 1=左右翻转, -1=双向翻转
cv::rotate(src, dst, cv::ROTATE_90_CLOCKWISE);
```

---

## 五、特征检测与轮廓

### `cv::findContours()`

从二值图像中查找所有轮廓，是目标检测和形状分析的基础。

```cpp
std::vector<std::vector<cv::Point>> contours;
std::vector<cv::Vec4i> hierarchy;

cv::findContours(binary, contours, hierarchy,
    cv::RETR_EXTERNAL,       // 只找最外层轮廓
    cv::CHAIN_APPROX_SIMPLE  // 压缩冗余点
);

// 绘制所有轮廓
cv::drawContours(img, contours, -1, cv::Scalar(0, 255, 0), 2);
```

| 检索模式 | 说明 |
|----------|------|
| `RETR_EXTERNAL` | 只检索最外层轮廓 |
| `RETR_LIST` | 检索所有轮廓，不建立层级 |
| `RETR_TREE` | 检索所有轮廓并重建完整层级 |

---

### `cv::boundingRect()` / `cv::minAreaRect()`

获取轮廓的外接矩形。

```cpp
// 水平正矩形（计算快速）
cv::Rect rect = cv::boundingRect(contours[i]);
cv::rectangle(img, rect, cv::Scalar(255, 0, 0), 2);

// 最小外接矩形（可旋转）
cv::RotatedRect rr = cv::minAreaRect(contours[i]);
cv::Point2f pts[4];
rr.points(pts);
for (int j = 0; j < 4; j++)
    cv::line(img, pts[j], pts[(j+1)%4], cv::Scalar(0, 0, 255), 2);
```

---

### `cv::contourArea()` / `cv::arcLength()`

计算轮廓的面积与周长，常用于过滤噪声轮廓。

```cpp
double area = cv::contourArea(contours[i]);
double peri = cv::arcLength(contours[i], true);  // true=闭合轮廓

// 过滤面积过小的轮廓
if (area < 500) continue;
```

---

### `cv::HoughLinesP()` / `cv::HoughCircles()`

基于霍夫变换检测直线和圆。

```cpp
// 概率霍夫直线检测
std::vector<cv::Vec4i> lines;
cv::HoughLinesP(edges, lines, 1, CV_PI/180,
    50,   // 累加器阈值
    50,   // 最小线段长度
    10    // 最大间隙
);

// 圆检测
std::vector<cv::Vec3f> circles;
cv::HoughCircles(gray, circles,
    cv::HOUGH_GRADIENT, 1, 20, 100, 30, 5, 100);
// 参数依次：dp, minDist, param1, param2, minRadius, maxRadius
```

---

### `cv::goodFeaturesToTrack()`

Shi-Tomasi 角点检测，常用于光流追踪的初始特征点提取。

```cpp
std::vector<cv::Point2f> corners;
cv::goodFeaturesToTrack(gray, corners,
    100,   // 最大角点数
    0.01,  // 质量因子（越小检测点越多）
    10     // 角点间最小距离（像素）
);
```

> 常配合 `cv::calcOpticalFlowPyrLK()` 实现 KLT 光流追踪。

---

## 六、绘图与标注

### `cv::line()` / `cv::rectangle()` / `cv::circle()`

基本几何图形绘制。`cv::Scalar` 颜色顺序为 BGR。

```cpp
// 线段
cv::line(img, cv::Point(10, 10), cv::Point(200, 200),
    cv::Scalar(0, 255, 0), 2);         // 颜色BGR, 线宽

// 矩形（左上角, 右下角）
cv::rectangle(img, cv::Point(50, 50), cv::Point(200, 200),
    cv::Scalar(255, 0, 0), 2);

// 圆（圆心, 半径），-1 表示实心填充
cv::circle(img, cv::Point(100, 100), 50,
    cv::Scalar(0, 0, 255), -1);
```

---

### `cv::putText()`

在图像上绘制文字标注。

```cpp
cv::putText(img,
    "Hello OpenCV",           // 文字内容
    cv::Point(10, 30),        // 左下角坐标
    cv::FONT_HERSHEY_SIMPLEX, // 字体
    1.0,                      // 字体大小
    cv::Scalar(0, 255, 0),    // 颜色 BGR
    2                         // 笔画粗细
);
```

> OpenCV 原生不支持中文，中文渲染需借助 `freetype2` 扩展库。

---

### `cv::arrowedLine()` / `cv::ellipse()`

箭头线段与椭圆绘制。

```cpp
// 带箭头的线段（常用于可视化速度、方向向量）
cv::arrowedLine(img, cv::Point(50, 50), cv::Point(200, 50),
    cv::Scalar(0, 165, 255), 2);

// 椭圆（圆心、轴长、旋转角度、起止角度）
cv::ellipse(img, cv::Point(200, 200),
    cv::Size(100, 50), 45.0, 0, 360,
    cv::Scalar(255, 0, 0), 2);
```

---

## 七、图像算术与掩码

### `cv::addWeighted()`

两张图像加权叠加，常用于透明度混合效果。

```cpp
cv::Mat blend;
cv::addWeighted(
    img1, 0.6,   // 图像1及其权重
    img2, 0.4,   // 图像2及其权重
    0,           // gamma 偏移量
    blend
);
```

---

### `cv::bitwise_and()` / `bitwise_or()` / `bitwise_not()`

按位逻辑运算，用掩码提取感兴趣区域（ROI）的标准方式。

```cpp
cv::Mat fg, bg, result;

// 提取前景（掩码区域）
cv::bitwise_and(src, src, fg, mask);

// 提取背景（掩码取反）
cv::bitwise_and(background, background, bg, ~mask);

// 合成前景与背景
cv::add(fg, bg, result);
```

---

### `cv::threshold()` / `cv::adaptiveThreshold()`

图像二值化，将灰度图转为黑白二值图。

```cpp
cv::Mat binary;

// 全局固定阈值
cv::threshold(gray, binary, 127, 255, cv::THRESH_BINARY);

// Otsu 自动计算最优阈值
cv::threshold(gray, binary, 0, 255,
    cv::THRESH_BINARY | cv::THRESH_OTSU);

// 自适应阈值（应对光照不均的场景）
cv::adaptiveThreshold(gray, binary, 255,
    cv::ADAPTIVE_THRESH_GAUSSIAN_C,
    cv::THRESH_BINARY, 11, 2);
```

| 阈值类型 | 说明 |
|----------|------|
| `THRESH_BINARY` | 大于阈值→255，否则→0 |
| `THRESH_BINARY_INV` | 大于阈值→0，否则→255 |
| `THRESH_OTSU` | 自动计算最优阈值（联合使用） |

---

## 八、相机标定与3D视觉

### `cv::calibrateCamera()`

通过多张棋盘格标定图估计相机内参矩阵和畸变系数。

```cpp
cv::Mat camera_matrix, dist_coeffs;
std::vector<cv::Mat> rvecs, tvecs;

double rms = cv::calibrateCamera(
    object_points,    // 世界坐标（3D 角点，单位：米或毫米）
    image_points,     // 对应的图像坐标（2D 角点）
    image_size,       // 图像分辨率
    camera_matrix,    // 输出：3×3 内参矩阵
    dist_coeffs,      // 输出：畸变系数向量
    rvecs, tvecs      // 输出：每张图的旋转/平移向量
);
// rms < 1.0 通常认为标定质量良好
```

---

### `cv::undistort()`

利用内参矩阵和畸变系数对图像进行畸变矫正。

```cpp
cv::Mat undistorted;
cv::undistort(src, undistorted, camera_matrix, dist_coeffs);
```

> 批量处理时建议先用 `initUndistortRectifyMap` 预计算映射表，再用 `remap`，性能更优。

---

### `cv::solvePnP()`

已知 3D 世界坐标与对应 2D 图像坐标，求相机相对于物体的旋转向量和平移向量（即6自由度位姿）。ROS 机器人抓取中常用。

```cpp
cv::Mat rvec, tvec;
cv::solvePnP(
    object_points_3d,   // 物体世界坐标（如 ArUco 标记角点）
    image_points_2d,    // 对应的图像像素坐标
    camera_matrix,
    dist_coeffs,
    rvec,               // 输出：旋转向量（Rodrigues 形式）
    tvec                // 输出：平移向量（相机坐标系，单位与3D点一致）
);

// 旋转向量转旋转矩阵
cv::Mat R;
cv::Rodrigues(rvec, R);
```

---

### `cv::projectPoints()`

将 3D 世界坐标点投影到图像平面，常用于可视化位姿坐标轴。

```cpp
std::vector<cv::Point2f> image_points;
cv::projectPoints(
    world_points_3d,
    rvec, tvec,
    camera_matrix, dist_coeffs,
    image_points
);

// 典型用法：投影坐标轴端点以绘制 XYZ 轴
std::vector<cv::Point3f> axis = {
    {0,0,0}, {0.1f,0,0}, {0,0.1f,0}, {0,0,0.1f}
};
```

---

## 附录：常用头文件

```cpp
#include <opencv2/opencv.hpp>       // 一次性包含所有模块（推荐）
#include <opencv2/core.hpp>         // Mat、基本数据结构
#include <opencv2/imgproc.hpp>      // 图像处理（cvtColor、blur、Canny等）
#include <opencv2/imgcodecs.hpp>    // imread、imwrite
#include <opencv2/highgui.hpp>      // imshow、waitKey、VideoCapture
#include <opencv2/calib3d.hpp>      // 标定、solvePnP、立体视觉
#include <opencv2/features2d.hpp>   // 特征点检测（ORB、SIFT等）
#include <opencv2/video/tracking.hpp> // 光流、目标追踪
```

## 附录：CMakeLists.txt 配置（ROS 2）

```cmake
find_package(OpenCV REQUIRED)

target_include_directories(your_node PUBLIC
    ${OpenCV_INCLUDE_DIRS}
)

target_link_libraries(your_node
    ${OpenCV_LIBS}
)
```
