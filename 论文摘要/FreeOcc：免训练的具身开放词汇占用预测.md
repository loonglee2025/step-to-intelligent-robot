# FreeOcc：免训练的具身开放词汇占用预测

<!-- 飞书 node_token: Eyyqw8RFcifu6gktWMacJktDnfb | obj_token: LvTJdPaBEot0W1xe0oScot0Vnuh -->

{
  "document": {
    "content": "\u003ctitle\u003eFreeOcc：免训练的具身开放词汇占用预测\u003c/title\u003e\u003ch1\u003eFreeOcc：免训练的具身开放词汇占用预测\u003c/h1\u003e\u003cp\u003e\u003c/p\u003e\u003cp\u003eFreeOcc 面向具身智能中的开放词汇占用预测问题，旨在从单目或 RGB-D 序列中构建具有语义理解能力的三维占用表示，而不依赖大规模三维标注、真实相机位姿或额外训练过程。\u003c/p\u003e\u003cp\u003e\u003c/p\u003e\u003ch2\u003e方法概述\u003c/h2\u003e\u003cp\u003eFreeOcc 通过四层流水线逐步生成全局一致的占用地图：\u003c/p\u003e\u003cul\u003e\u003cli\u003e\u003cb\u003eSLAM 位姿与几何估计\u003c/b\u003e：利用 SLAM backbone 估计相机位姿与稀疏几何结构。\u003c/li\u003e\u003cli\u003e\u003cb\u003eGaussian 地图构建\u003c/b\u003e：通过几何一致的 Gaussian 更新，构建稠密三维 Gaussian 地图。\u003c/li\u003e\u003cli\u003e\u003cb\u003e开放词汇语义关联\u003c/b\u003e：借助现成视觉语言模型，将开放词汇语义关联到 Gaussian primitives。\u003c/li\u003e\u003cli\u003e\u003cb\u003e占用预测生成\u003c/b\u003e：采用概率化的 Gaussian-to-occupancy 投影，生成稠密体素占用结果。\u003c/li\u003e\u003c/ul\u003e\u003cp\u003e与依赖体素级监督或真实位姿的传统方法不同，FreeOcc 的核心特点是免训练、位姿无关，并能够直接迁移到新环境中。\u003c/p\u003e\u003cp\u003e\u003c/p\u003e\u003ch2\u003e实验结果\u003c/h2\u003e\u003cp\u003e实验表明，FreeOcc 在 EmbodiedOcc-ScanNet 上相较既有自监督方法实现了超过 2 倍的 IoU 与 mIoU 提升。同时，在零样本迁移到新环境时，FreeOcc 显著优于监督和自监督基线方法。\u003c/p\u003e\u003cp\u003e\u003c/p\u003e\u003ch2\u003e数据集与资源\u003c/h2\u003e\u003cp\u003e论文还提出了 **ReplicaOcc**，这是一个面向室内开放词汇占用预测的基准数据集，提供 RGB-D 序列与场景级占用真值，用于评估具身占用预测系统。\u003c/p\u003e\u003cp\u003e\u003c/p\u003e\u003cp\u003e相关资源如下：\u003c/p\u003e\u003cul\u003e\u003cli\u003e\u003cb\u003e论文标题\u003c/b\u003e：FreeOcc: Training-Free Embodied Open-Vocabulary Occupancy Prediction\u003c/li\u003e\u003cli\u003e\u003cb\u003e论文地址\u003c/b\u003e：https://arxiv.org/abs/2604.28115\u003c/li\u003e\u003cli\u003e\u003cb\u003e项目主页\u003c/b\u003e：https://the-masses.github.io/freeocc-web/\u003c/li\u003e\u003cli\u003e\u003cb\u003e项目代码\u003c/b\u003e：https://github.com/the-masses/\u003c/li\u003e\u003cli\u003e\u003cb\u003eFreeOcc 数据集\u003c/b\u003e：https://huggingface.co/datasets/the-masses/ReplicaOcc\u003c/li\u003e\u003c/ul\u003e\u003cp\u003e\u003c/p\u003e\u003ch2\u003e总结\u003c/h2\u003e\u003cp\u003e总体而言，FreeOcc 将 SLAM、三维 Gaussian 表示、视觉语言语义关联和概率占用投影结合起来，为开放词汇三维场景理解提供了一种无需训练的新路径。该方法降低了具身占用预测对标注数据和训练流程的依赖，并展示了良好的跨环境泛化能力。\u003c/p\u003e",
    "document_id": "LvTJdPaBEot0W1xe0oScot0Vnuh",
    "revision_id": 11
  },
  "log_id": "2026070820473769E6C227C92865240B80"
}
