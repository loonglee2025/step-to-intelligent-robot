# ros开发库

<!-- 飞书 node_token: CsNTwE04ZiaEzVkOLbxc0zVan5e | obj_token: EwWXdqeeuojv1Nxw9tcclwOWnvu -->

{
  "document": {
    "content": "\u003ctitle\u003eros开发库\u003c/title\u003e\u003cp\u003e以下是 \u003ccode\u003ecv_bridge\u003c/code\u003e 库在 C++ / ROS 2 开发中的常用函数整理，含分类说明与示例。以上是 \u003ccode\u003ecv_bridge\u003c/code\u003e 的核心函数整理，点击每个卡片可展开详情。下面补充几个重要的使用要点：\u003c/p\u003e\u003cp\u003e\u003cb\u003e选择 \u003c/b\u003e\u003cb\u003e\u003ccode\u003etoCvShare\u003c/code\u003e\u003c/b\u003e\u003cb\u003e 还是 \u003c/b\u003e\u003cb\u003e\u003ccode\u003etoCvCopy\u003c/code\u003e\u003c/b\u003e\u003cb\u003e？\u003c/b\u003e\u003c/p\u003e\u003cul\u003e\u003cli\u003e只是读取图像（如检测、显示）→ 用 \u003ccode\u003etoCvShare()\u003c/code\u003e，零拷贝性能更好\u003c/li\u003e\u003cli\u003e需要修改图像（如绘制、滤波、改变像素）→ 必须用 \u003ccode\u003etoCvCopy()\u003c/code\u003e，否则会修改共享内存\u003c/li\u003e\u003c/ul\u003e\u003cp\u003e\u003cb\u003e编码格式注意事项\u003c/b\u003e\u003c/p\u003e\u003cul\u003e\u003cli\u003eOpenCV 内部使用 \u003cb\u003eBGR\u003c/b\u003e 顺序，而 ROS 标准是 \u003cb\u003eRGB\u003c/b\u003e，订阅时务必指定 \u003ccode\u003e\"bgr8\"\u003c/code\u003e 以保证颜色正确\u003c/li\u003e\u003cli\u003e深度图不要误用 \u003ccode\u003e\"bgr8\"\u003c/code\u003e 编码，应使用 \u003ccode\u003e\"32FC1\"\u003c/code\u003e 或 \u003ccode\u003e\"16UC1\"\u003c/code\u003e\u003c/li\u003e\u003c/ul\u003e\u003cp\u003e\u003cb\u003e异常处理\u003c/b\u003e\u003c/p\u003e\u003cp\u003e\u003ccode\u003etoCvShare\u003c/code\u003e 和 \u003ccode\u003etoCvCopy\u003c/code\u003e 在编码不匹配时会抛出 \u003ccode\u003ecv_bridge::Exception\u003c/code\u003e，生产代码中务必用 \u003ccode\u003etry-catch\u003c/code\u003e 包裹。\u003c/p\u003e\u003cp\u003e\u003cb\u003eCMakeLists.txt 依赖配置\u003c/b\u003e（ROS 2）\u003c/p\u003e\u003cpre caption=\"\u0026#xA;\" lang=\"CMake\"\u003e\u003ccode\u003efind_package(cv_bridge REQUIRED)\u003cbr/\u003etarget_link_libraries(your_node cv_bridge::cv_bridge)\u003c/code\u003e\u003c/pre\u003e\u003cp\u003e\u003c/p\u003e",
    "document_id": "EwWXdqeeuojv1Nxw9tcclwOWnvu",
    "revision_id": 6
  },
  "log_id": "202607082047275144372B671DEA21EBD8"
}
