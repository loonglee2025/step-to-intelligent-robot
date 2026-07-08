# Autonomy Software Architecture

<!-- 飞书 node_token: FiExwiiYkitsxgkaSLDci6vPni3 | obj_token: PLcMdX4JMoZUhPx271qcFq7Pnuc -->

{
  "document": {
    "content": "\u003ctitle\u003eAutonomy Software Architecture\u003c/title\u003e\u003cimg name=\"图片.png\" href=\"https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ODZkMjhiMTcxOWYxZDE2YmNhNDAzNTA2ZDY5MWE3ODdfZmNmODhmZjQxZTI3NDM3M2RkMmIxMjAyMWFmMDBjMjVfSUQ6NzU2MDUyOTkwNDA4MzgyODczOV8xNzgzNTE0ODI4OjE3ODM1MTg0MjhfVjM\" mime=\"image/png\" scale=\"1.000000\" src=\"EviLb3dzFoZjI0xeI40cefplnXb\"/\u003e\u003cp\u003eThis diagram provides an overview of a relatively complex autonomy software architecture, often used in applications like robotaxis. It illustrates the integration of various components essential for autonomous systems, though some robots may only use a subset of these elements.\u003c/p\u003e\u003cul\u003e\u003cli\u003e\u003cb\u003ePerception and Localization\u003c/b\u003e: Utilizes sensors such as cameras and LIDAR, along with mapping systems, to assess the environment and determine the robot’s position and state.\u003c/li\u003e\u003cli\u003e\u003cb\u003ePlanning\u003c/b\u003e: Involves multiple stages:\u003cul\u003e\u003cli\u003e\u003cb\u003eGlobal Planning\u003c/b\u003e: Similar to setting a route on a map, defining the mission and waypoints to reach a goal.\u003c/li\u003e\u003cli\u003e\u003cb\u003eBehavior Planning\u003c/b\u003e: Determines actions, such as which object to manipulate and how.\u003c/li\u003e\u003cli\u003e\u003cb\u003eLocal Planning\u003c/b\u003e: Generates trajectories for specific tasks, guiding the robot through precise paths.\u003c/li\u003e\u003c/ul\u003e\u003c/li\u003e\u003cli\u003e\u003cb\u003eControl and Actuation\u003c/b\u003e: The controller tracks trajectory states to execute movements, whether for manipulation or navigation. Outcomes are tested in both virtual simulations and real-world scenarios.\u003c/li\u003e\u003cli\u003e\u003cb\u003eFeedback Loop\u003c/b\u003e: Continuously gathers data from sensors and localization to refine planning and improve system performance. This iterative process ensures adaptive and responsive robot behavior.\u003c/li\u003e\u003c/ul\u003e",
    "document_id": "PLcMdX4JMoZUhPx271qcFq7Pnuc",
    "revision_id": 6
  },
  "log_id": "202607082047084B8DB404669EAA22ACE1"
}
