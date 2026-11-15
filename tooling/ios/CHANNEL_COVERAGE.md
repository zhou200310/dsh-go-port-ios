# iOS 原生通道核对

Flutter 的 `MethodChannel` 能编译通过，即使 iOS 没有注册同名通道。2026-11-16 清点 `app/lib` 中直接声明的 43 条 `dsh/*` 通道：iOS 注册 34 条。语音通话和普通消息朗读共用的 `dsh/speech` 已补齐；`test-channel-contract.py` 检查语音方法和 iOS 分发器一致，也要求新增的共享通道有 iOS 注册或在下表明确归类，避免再把缺少通道误报为麦克风故障。

其余 9 条没有同名 iOS 通道，按源码调用路径处理：

| 通道 | 当前 iOS 路径 | 后续工作 |
| --- | --- | --- |
| `dsh/attachment-files` | OHOS 专用附件接口 | 无需移植；iOS 走自己的文件入口 |
| `dsh/dynamic_color` | 缺失时退回预设配色 | 壁纸取色未实现 |
| `dsh/media_video` | 聊天媒体卡仍可调用，缺原生解码/纹理 | **需实现或明确禁用视频预览** |
| `dsh/notify/actions` | Android 通知操作事件；iOS 有通知点击入口 | 核对 iOS 操作按钮是否需要独立事件 |
| `dsh/phone/preview` | Android 后台预览流 | iOS 后台预览能力未对齐 |
| `dsh/picture_shortcut` | 缺失时返回“不支持” | iOS 快捷方式用另一入口 |
| `dsh/secrets` | iOS 使用 Keychain 的 FlutterSecureStorage | 无需移植 OHOS 密钥接口 |
| `dsh/update` | Android 应用内安装器 | iOS 更新入口需单独设计，不能复用 APK 安装 |
| `dsh/web-files` | 仅 Android WebView 显式连接此选择器 | 核对 iOS WebKit 文件上传实际行为 |

这个清单只证明通道注册与静态调用关系，不证明真实设备上的麦克风、播放、后台或 WebKit 行为。新增共享 UI 功能若调用原生通道，应核对 iOS 注册、每个方法的返回类型和真机路径。
