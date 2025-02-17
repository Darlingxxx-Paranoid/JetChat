# 一、图片选择器逻辑

## 1. `UserInput()`

* 参数`onImageSent: (Uri) ->Unit`：用于发送图片Uri到对话
* 初始化`pickMedia`：调用Android的`PickVisualMedia()`来选择图片，并赋值`onImageSent`

## 2. `SelectorExpanded()`

* 当点击PictureButton时，启动图片选择器协程，回调`pickMedia`

# 二、图片发送逻辑

## 1. `message类`

添加参数`image`用于储存图片消息的Uri

## 2. `ChatItemBubble()`

* 检测到`massage`为图片，则通过`message.image`展示图片

# 三、图片预览逻辑（防止截屏+倒计时关闭+双击缩放+单击关闭）

## 1.  `ConversationContent()`

* 添加状态变量`showImageDialog`来控制图片预览的显示和隐藏

## 2. 回调

在`Messages()`、`Message()`、`AuthorAndTextMessage()`、`ChatItemBubble`添加`onImageClick`参数，以绑定需要预览的图片Uri和设置`showImageDialog`为true

## 3. `ImageDialog`

### 3.1 防止截屏

通过设置活动窗口`window`的`FLAG_SECURE`字段实现

### 3.2 倒计时

* 使用`LaunchedEffect`启动一个倒计时协程

### 3.3 双击缩放+单击关闭

通过`detectTapGestures`监听用户手势实现

### 3.4 退出

退出时取消所有协程，并回调`onDismiss()`

# 四、图片模糊逻辑

## `ChatItemBubble()`

只需在未预览时，调用`BlurredEdgeTreatment`来将图片模糊处理即可

# 五、阅后即焚逻辑

## 1. `ChatItemBubble()`

在当前图片是已经焚毁的照片时，显示默认的焚毁图片，并且无法点击

## 2. 回调

在`Messages()`、`Message()`、`AuthorAndTextMessage()`、`ChatItemBubble`添加`burnedImages`参数，以返回所有已经焚毁的照片。

## 3. `ConversationContent`

* 在图片预览关闭，即调用`onDismiss()`时，将图片设置为已经焚毁的照片
* 在调用`Messages`，设置`onClick()`只有未焚毁的图片可点击
