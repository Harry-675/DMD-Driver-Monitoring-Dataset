# 时间标注工具 (TaTo)

我们已经在DMD（驾驶员监控数据集）中获取了大量高质量且友好的驾驶员素材，目的是开发用于驾驶员监控的计算机视觉算法。但是没有相应标注的数据集有什么用呢？

我们开发了TaTo工具来标注视频序列中驾驶员执行的时间事件和动作。该工具被用于标注与分心相关的动作。然而，通过配置，也可以标注其他标签。

## 标注信息内容

基于对代码的分析，TaTo标注工具包含以下信息：

### 1. 标注级别和标签

TaTo支持多级标注，最多可以有7个不同的标注级别（标签组）。在默认的分心模式下包括以下级别：

- **级别0**: camera_occlusion（摄像机遮挡）
- **级别1**: gaze_on_road（目光在路上）
- **级别2**: talking（说话）
- **级别3**: hands_using_wheel（手使用方向盘）
- **级别4**: hand_on_gear（手在换挡杆上）
- **级别5**: objects_in_scene（场景中的物体）
- **级别6**: driver_actions（驾驶员动作）

每个标注级别内的标签是互斥的（即同一帧不能有两个相同级别的标签）。

### 2. 特殊标签

所有标注级别都有两个特殊标签：

- **"NAN"**: 表示没有来自用于标注该级别的摄像机流的帧。此标签由工具自动设置，无法手动更改。
- **"--"**: 表示缺少标签。某些级别不需要连续的标注标签，因此会出现这种"空"标签。

### 3. 标注模式

标注工具有三种标注模式：

- **逐帧标注**: 基本标注模式，用户为每一帧单独标注。
- **块标注**: 一次标注一个帧间隔的便捷方式。用户选择起始帧和结束帧，然后对所有选定的帧应用相同的标签。
- **自动标注**: 基于逻辑关系在不同级别的标注之间自动应用标注。例如，如果驾驶员正在执行"Texting left"（左手发短信）操作，则hands_using_wheel级别的标注应该是"Only Right"（仅右手）。

### 4. 帧验证状态

每一帧都有一个验证属性，指示其标注方式：

- **未更改**: 帧未被手动更改（值为0）
- **手动更改**: 帧通过逐帧标注进行了更改（值为1）
- **块更改**: 帧通过块标注进行了更改（值为2）

### 5. 静态标注和元数据

除了时间标注外，TaTo还支持静态标注和元数据：

- **静态标注**: 包括驾驶员信息和属性
- **元数据**: 包括相机流信息、帧计数和其他上下文数据

### 6. 保存格式

标注以VCD（Video Content Description）格式保存在JSON文件中。工具还提供自动保存功能，创建两个TXT文件作为备份。

## 工具特点

TaTo是一个基于Python和OpenCV的时间标注工具，具有以下特点：

1. 允许标注视频中的时间事件（不仅限于DMD）
2. 支持多达7个不同的标注级别，每个级别内的标签互斥
3. 可以通过配置文件定义标注级别和标签
4. 标注通过两个时间线以颜色表示，每个标签有自己的颜色
5. 可以逐帧或按帧块间隔输入标签
6. 每帧都有验证属性，指示其标注方式
7. 输出标注以VCD格式保存在JSON文件中
8. 对于DMD默认标注模式，可以应用自动标注
9. 为避免进度丢失，工具自动保存标注

TaTo提供了一个直观的界面，包括三个主要窗口：
- 主窗口显示视频和当前标注信息
- 标签窗口显示可用标签和键盘快捷键
- 时间线窗口以颜色可视化标注

该工具是一个专用于时间标注的应用程序，而不是数据分析可视化工具。它专为标注DMD视频而设计，但已修改为与一般视频兼容。 

## DMD数据标注格式说明

DMD数据集包含多种标注类型，每种类型关注驾驶员行为的不同方面。以下是主要标注类型的格式说明：

### Distraction（分心）标注格式

分心标注主要关注驾驶员执行的可能导致分心的动作。标注文件以VCD格式保存，包含以下7个级别：

1. **occlusion（遮挡）**
   - face_camera: 面部摄像头
   - body_camera: 身体摄像头
   - hands_camera: 手部摄像头
   - --: 无遮挡
   - NAN: 无帧信息

2. **gaze_on_road（目光在路上）**
   - looking_road: 看路
   - not_looking_road: 不看路
   - NAN: 无帧信息

3. **talking（说话）**
   - talking: 说话
   - --: 不说话
   - NAN: 无帧信息

4. **hands_using_wheel（手使用方向盘）**
   - both: 双手
   - only_right: 仅右手
   - only_left: 仅左手
   - none: 无手
   - NAN: 无帧信息

5. **hand_on_gear（手在换挡杆上）**
   - hand_on_gear: 手在换挡杆上
   - --: 手不在换挡杆上
   - NAN: 无帧信息

6. **objects_in_scene（场景中的物体）**
   - cellphone: 手机
   - hair_comb: 梳子
   - bottle: 瓶子
   - --: 无物体
   - NAN: 无帧信息

7. **driver_actions（驾驶员动作）**
   - safe_drive: 安全驾驶
   - texting_right: 右手发短信
   - phonecall_right: 右手打电话
   - texting_left: 左手发短信
   - phonecall_left: 左手打电话
   - radio: 操作收音机
   - drinking: 喝水
   - reach_side: 伸手侧面
   - hair_and_makeup: 整理头发和化妆
   - talking_to_passenger: 与乘客交谈
   - reach_backseat: 伸手后座
   - change_gear: 换挡
   - standstill_or_waiting: 停止或等待
   - unclassified: 未分类
   - NAN: 无帧信息

分心标注的特点是可以通过自动标注功能，基于driver_actions级别的标注自动推断其他级别的标注。例如，当标注"texting_left"时，系统会自动推断gaze_on_road为"not_looking_road"，hands_using_wheel为"only_right"等。

### Drowsiness（困倦）标注格式

困倦标注主要关注驾驶员的眼睛状态和打哈欠行为，标注文件包含以下4个级别：

1. **occlusion（遮挡）**
   - face_camera: 面部摄像头
   - body_camera: 身体摄像头
   - hands_camera: 手部摄像头
   - --: 无遮挡
   - NAN: 无帧信息

2. **eyes_state（眼睛状态）**
   - open: 睁眼
   - close: 闭眼
   - opening: 正在睁眼
   - closing: 正在闭眼
   - undefined: 未定义
   - NAN: 无帧信息

3. **blinks（眨眼）**
   - blinking: 眨眼
   - --: 无眨眼
   - NAN: 无帧信息

4. **yawning（打哈欠）**
   - Yawning with hand: 用手遮挡打哈欠
   - Yawning without hand: 不用手遮挡打哈欠
   - --: 无打哈欠
   - NAN: 无帧信息

困倦标注主要依赖于面部摄像头，所有与眼睛状态、眨眼和打哈欠相关的标注都基于面部视频流。

### Gaze（注视）标注格式

注视标注关注驾驶员目光的方向，标注文件包含以下3个级别：

1. **occlusion（遮挡）**
   - face_camera: 面部摄像头
   - body_camera: 身体摄像头
   - hands_camera: 手部摄像头
   - --: 无遮挡
   - NAN: 无帧信息

2. **gaze_zone（注视区域）**
   - left_mirror: 左侧镜子
   - left: 左侧
   - front: 前方
   - center_mirror: 中央镜子
   - front_right: 右前方
   - right_mirror: 右侧镜子
   - right: 右侧
   - infotainment: 信息娱乐系统
   - steering_wheel: 方向盘
   - not_valid: 无效
   - NAN: 无帧信息

3. **blinks（眨眼）**
   - blinking: 眨眼
   - --: 无眨眼
   - NAN: 无帧信息

注视标注主要依赖于面部摄像头，所有与注视区域和眨眼相关的标注都基于面部视频流。

### Hands（手部）标注格式

手部标注关注驾驶员手部动作，特别是与方向盘的交互，标注文件包含以下4个级别：

1. **occlusion（遮挡）**
   - face_camera: 面部摄像头
   - body_camera: 身体摄像头
   - hands_camera: 手部摄像头
   - --: 无遮挡
   - NAN: 无帧信息

2. **hands_on_wheel（手在方向盘上）**
   - both_hands: 双手
   - only_right: 仅右手
   - only_left: 仅左手
   - none: 无手
   - NAN: 无帧信息

3. **transition（转换）**
   - taking control: 接管控制
   - giving control: 放弃控制
   - --: 无转换
   - NAN: 无帧信息

4. **moving_hands（手部移动）**
   - moving: 移动中
   - not_moving: 不移动
   - NAN: 无帧信息

手部标注主要依赖于身体摄像头，所有与手部动作相关的标注都基于身体视频流。