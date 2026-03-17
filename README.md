# webim-uniapp-demo-vue3

# 介绍

demo 包含以下核心功能

- 会话列表
- 系统通知
- 联系人
- 添加好友
- 群组创建
- 群组详情
- 我的
- 用户属性
- 单人聊天
- 群组聊天
- 文本、图片、语音、附件、个人名片收发。
- 原生端声网音视频拨打功能。

# 在本地跑起来

拉取代码，并执行`npm install`,在 HBuliderX 工具点击运行至想要的平台中即可运行起来。

# 项目结构核心目录说明

```shell
|- components 自定义组件目录
    |-Agora-RTC-JS AgoraRtc js API组件
    |-emChat 聊天页面核心组件（消息列表、输入框相关代码）
    |-emCallKit AgoraRtc 核心业务逻辑组件
    |-swipedelete 测滑删除组件
|-static/images demo中用到的图片 还有表情
|-EaseIM 环信IM核心逻辑文件
    |-config IM相关配置
    |-constant 相关常量
    |-imApis 项目中所用IM SDK api方法
    |-listener IM监听回调
    |-utils 相关工具
    |-index.js 核心sdk初始化在此js文件中完成并导出
|-layout 布局（tab-bar）
|-recorderCore H5录音包
|-pages 功能页面
    |-login 登录页
    |-home home页面
    |-conversation 会话列表页面
    |-contacts 联系人页
    |-me 我的页面
    |-addNewFriend 加好友页
    |-addGroups 创建新群
    |-groups 群组列表页
    |-groupSetting 群组设置页
    |-notificaton 通知入口页（群组、单人通知）
    |-notificatonFriendDetail 加好友通知页
    |-notificatonGroupDetail 加群组通知页
    |-moreMenu 更多功能页面
    |-profile 用户属性展示页
    |-searchMsg 消息搜索页面
    |-settingGeneral 设置功能
    |-emChatContainer emChat聊天容器组件
    |-emCallKitPages AgoraRtc 相关页面组件
|-utils 工具类和sdk的一些配置
|-stores pinia store 全局状态管理
|-uni_modules uni插件包
|-node_modules 这个相信不需要特别说明（IMSDK在此中）
|-app.vue 项目根组件（注册IM监听事件、处理连接跳转）
|-app.json 注册页面以及全局的一些配置
|-app.css 一些全局样式
```

# 📞 音视频通话(CallKit)集成指南

> ⚠️ **重要声明**
> 
> **本项目仅为环信 IM 与声网 RTC 结合的一种实现示例，并非官方最佳实践，仅供参考学习。**
> 
> - 代码实现侧重于功能演示，生产环境使用前请根据实际需求进行优化调整
> - 音视频通话的架构设计、信令交互、异常处理等方面可以根据业务场景进一步优化
> - 建议结合官方文档和实际业务需求，设计适合自己项目的方案
> - 如有疑问，建议咨询环信官方技术支持获取更专业的指导

---

## 📖 简介

CallKit 是基于环信 IM 和声网 Agora RTC 开发的音视频通话组件，支持以下功能：

| 功能 | 说明 |
|------|------|
| 1对1语音通话 | 两人之间的纯语音通话 |
| 1对1视频通话 | 两人之间的视频通话，支持切换摄像头 |
| 多人音视频通话 | 最多支持 16 人同时在线的群通话 |
| 通话邀请管理 | 支持接听、拒绝、忙线、超时等状态 |
| 多端同步 | 支持多设备登录时的通话状态同步 |

> 💡 **适用场景**：社交应用、在线客服、远程医疗、在线教育等需要实时音视频通话的场景。

---

## ⚠️ 重要变更说明

### 本地插件引入（必须）

**本项目已升级为本地插件引入方式，不再支持云端插件引入。**

| 项目 | 说明 |
|------|------|
| 插件版本 | Agora RTC v3.7.2 |
| 引入方式 | 本地 nativeplugins |
| 下载地址 | [Agora-Uniapp-SDK v3.7.2](https://github.com/AgoraIO-Community/Agora-Uniapp-SDK/releases/tag/v3.7.2) |
| 支持平台 | iOS / Android |

> ❌ **注意**：云端插件已停止支持，DCloud 插件市场的 Agora 云端插件已下架。

> ⚠️ **云打包限额提示**：
> 
> 由于本项目引入了声网原生插件（Agora RTC），插件体积较大，**云打包时可能超出 DCloud 云打包的免费/付费限额**。
> 
> **解决方案**：
> 1. 自行购买或调整云打包限额（参考 [uniApp 云打包文档](https://uniapp.dcloud.net.cn/dev/app/cloud-build.html)）
> 2. 使用**离线打包**方式（推荐），参考 [uniApp 离线打包文档](https://uniapp.dcloud.net.cn/dev/app/android/offline.html)
> 
> > 📌 原生插件体积较大，目前暂无更好的优化方案，请根据实际需求选择合适的打包方式。

### RTC Token 获取方式升级

从本分支开始，RTC Token 获取方式已升级为使用**环信 SDK 内置方法**。

| 对比项 | 旧方式 | 新方式 |
|--------|--------|--------|
| 获取方式 | 自行部署后端接口 | SDK 内置方法 |
| 适用版本 | 任意 | `easemob-websdk@4.17.0+` |
| 实现复杂度 | 高（需要后端开发） | 低（直接调用 SDK） |
| 维护成本 | 高 | 低 |

**新方式使用的 API**：
- `getRTCToken(channelName)` - 获取 RTC Token ([官方文档](https://doc.easemob.com/apidoc/web/modules/Contact.html#getRTCToken))
- `getUserIdByRTCUIds(uidList)` - 获取 UID 与环信 ID 映射 ([官方文档](https://doc.easemob.com/apidoc/web/modules/Contact.html#getUserIdByRTCUIds))

---

## 🚀 快速开始

### 步骤 1：开通实时音视频服务

从环信 IM 4.17.0 版本开始，声网 AppID 可以通过环信控制台直接开通，无需单独注册声网账号。

1. 登录 [环信控制台](https://console.easemob.com/)
2. 选择你的应用，进入**即时通讯** -> **功能配置** -> **功能配置总览**
3. 找到 **实时音视频** 服务，点击**开通**
4. 开通后，系统会自动分配声网 AppID 和相关配置
5. 在 `components/emCallKit/config/index.js` 中配置：

```javascript
export const AGORA_APP_ID = '从环信控制台获取的声网AppID';
```

> 💡 **优势**：
> - 无需单独注册声网账号
> - AppID 与环信应用自动关联
> - 可直接使用 SDK 内置方法获取 RTC Token

### 步骤 2：配置本地插件

#### 2.1 下载插件

从 [Agora-Uniapp-SDK v3.7.2](https://github.com/AgoraIO-Community/Agora-Uniapp-SDK/releases/tag/v3.7.2) 下载完整的插件包。

#### 2.2 放置插件文件

将解压后的 `Agora-RTC` 文件夹复制到项目根目录的 `nativeplugins/` 目录下：

```
项目根目录/
└── nativeplugins/
    └── Agora-RTC/
        ├── ios/                    # iOS 平台插件
        ├── android/                # Android 平台插件
        └── package.json
```

#### 2.3 配置 manifest.json

1. 在 HBuilderX 中打开 `manifest.json` 文件
2. 点击 **安卓/iOS 原生插件配置**
3. 点击 **选择本地插件**，勾选 **Agora音视频插件**

#### 2.4 制作自定义基座

⚠️ **重要**：使用本地插件**必须**制作自定义基座，标准基座不包含此插件。

在 HBuilderX 中点击：`运行 -> 运行到手机或模拟器 -> 制作自定义调试基座`

#### 2.5 验证插件配置

制作好自定义基座后，运行以下代码验证：

```javascript
const AgoraRtcEngine = uni.requireNativePlugin('Agora-RTC-AgoraRtcEngineModule');
console.log('Agora Plugin:', AgoraRtcEngine ? '✅ 加载成功' : '❌ 加载失败');
```

### 步骤 3：集成 CallKit 组件

#### 3.1 复制组件文件

将以下文件/目录复制到你的项目中：

| 来源 | 目标 | 说明 |
|------|------|------|
| `components/emCallKit/` | `components/emCallKit/` | CallKit 核心组件 |
| `components/Agora-RTC-JS/` | `components/Agora-RTC-JS/` | Agora RTC 封装组件 |
| `pages/emCallKitPages/` | `pages/emCallKitPages/` | 通话页面 |

#### 3.2 配置页面路由

在 `pages.json` 中添加通话页面路由：

```json
{
  "pages": [
    // ... 其他页面
    {
      "path": "pages/emCallKitPages/alertScreen",
      "style": { "navigationBarTitleText": "来电提醒" }
    },
    {
      "path": "pages/emCallKitPages/singleCall",
      "style": { "navigationBarTitleText": "通话中" }
    },
    {
      "path": "pages/emCallKitPages/multiCall",
      "style": { "navigationBarTitleText": "多人通话" }
    }
  ]
}
```

#### 3.3 初始化 CallKit

在应用启动时（如 `App.vue` 或登录成功后），初始化 CallKit：

```javascript
import { useInitCallKit } from "@/components/emCallKit";

// 在适当的时机调用（如登录成功后）
const { setCallKitClient } = useInitCallKit();

// EMClient: 环信 IM 实例
// EaseSDK.message: 消息构建方法
setCallKitClient(EMClient, EaseSDK.message);
```

### 步骤 4：订阅通话事件

CallKit 通过事件机制通知应用通话状态变化，你需要订阅这些事件来处理界面跳转等逻辑：

```javascript
import useCallKitEvent from "@/components/emCallKit/callKitManage/useCallKitEvent";

const { 
  EVENT_NAME,           // 事件名称常量
  CALLKIT_EVENT_CODE,   // 事件类型编码
  SUB_CHANNEL_EVENT     // 订阅方法
} = useCallKitEvent();

// 订阅通话事件
SUB_CHANNEL_EVENT(EVENT_NAME, (params) => {
  const { type, ext, callType, eventHxId } = params;
  console.log("📞 收到通话事件:", type.code, params);
  
  switch (type.code) {
    case CALLKIT_EVENT_CODE.ALERT_SCREEN:
      // 收到通话邀请，跳转到接听页面
      uni.navigateTo({
        url: "/pages/emCallKitPages/alertScreen",
      });
      break;
      
    case CALLKIT_EVENT_CODE.CALLEE_ACCPET:
      // 对方已接听，跳转到通话页面
      if (callType === 0 || callType === 1) {
        // 单人通话
        uni.navigateTo({
          url: "/pages/emCallKitPages/singleCall",
        });
      } else {
        // 多人通话
        uni.navigateTo({
          url: "/pages/emCallKitPages/multiCall",
        });
      }
      break;
      
    case CALLKIT_EVENT_CODE.CALLEE_REFUSE:
      // 对方拒绝接听
      uni.showToast({ title: "对方拒绝接听", icon: "none" });
      break;
      
    case CALLKIT_EVENT_CODE.CALLEE_BUSY:
      // 对方忙线中
      uni.showToast({ title: "对方正忙", icon: "none" });
      break;
      
    case CALLKIT_EVENT_CODE.TIMEOUT:
      // 通话超时未接听
      uni.showToast({ title: "通话超时", icon: "none" });
      break;
      
    case CALLKIT_EVENT_CODE.CALLER_CANCEL:
      // 对方取消呼叫
      uni.showToast({ title: "对方已取消", icon: "none" });
      break;
  }
});
```

### 步骤 5：发起通话

#### 发起 1对1 语音通话

```javascript
import useAgoraChannelStore from "@/components/emCallKit/stores/channelManger";
import { CALL_TYPES } from "@/components/emCallKit/contants";

const agoraChannelStore = useAgoraChannelStore();

// targetId: 对方环信 ID
await agoraChannelStore.sendInviteMessage(
  targetId, 
  CALL_TYPES.SINGLE_VOICE  // 语音通话
);
```

#### 发起 1对1 视频通话

```javascript
await agoraChannelStore.sendInviteMessage(
  targetId, 
  CALL_TYPES.SINGLE_VIDEO  // 视频通话
);
```

#### 发起多人通话

```javascript
// 在群聊中发起多人音视频通话
const memberList = ['user1', 'user2', 'user3']; // 群成员 ID 列表
const groupId = '群ID';

await agoraChannelStore.sendInviteMessage(
  memberList,
  CALL_TYPES.MULTI_VIDEO,  // 多人视频
  groupId
);
```

---

## 📚 事件类型说明

| 事件编码 | 说明 | 触发时机 |
|----------|------|----------|
| `ALERT_SCREEN` | 显示来电提醒 | 收到通话邀请时 |
| `TIMEOUT` | 通话超时 | 30秒内未接听 |
| `CALLEE_ACCPET` | 对方已接听 | 被叫方点击接听 |
| `CALLEE_REFUSE` | 对方已拒绝 | 被叫方点击拒绝 |
| `CALLEE_BUSY` | 对方忙线 | 被叫方正在通话中 |
| `CALLER_CANCEL` | 对方已取消 | 主叫方取消呼叫 |
| `CANCEL` | 已取消 | 通话被取消 |
| `OTHER_HANDLE` | 其他设备已处理 | 多端登录时，其他设备处理了通话 |

---

## 🔄 通话状态流转

```
┌─────────────┐
│    idle     │  <-- 初始状态/通话结束
│   (空闲)     │
└──────┬──────┘
       │ 发起邀请 / 收到邀请
       ▼
┌─────────────┐
│   alerting  │  <-- 响铃中
│   (响铃)     │
└──────┬──────┘
       │ 被叫方发送 confirmRing
       ▼
┌─────────────┐
│   inviting  │  <-- 等待接听
│   (邀请中)   │
└──────┬──────┘
       │ 被叫方接听
       ▼
┌─────────────┐
│ receivedAnswerCall │  <-- 收到接听响应
│  (收到应答)   │
└──────┬──────┘
       │ 主叫方确认
       ▼
┌─────────────┐
│ confirmCallee │  <-- 通话连接中
│   (确认被叫)  │
└──────┬──────┘
       │ 加入频道成功
       ▼
┌─────────────┐
│ answerCall  │  <-- 通话中
│   (通话中)   │
└──────┬──────┘
       │ 挂断
       ▼
┌─────────────┐
│    idle     │  <-- 回到空闲
│   (空闲)     │
└─────────────┘
```

---

## 🛠️ 高级功能

### 通话中操作

在通话页面中，你可以使用以下方法控制通话：

```javascript
import useAgoraChannelStore from "@/components/emCallKit/stores/channelManger";
import { CALLSTATUS } from "@/components/emCallKit/contants";

const agoraChannelStore = useAgoraChannelStore();

// 挂断通话
agoraChannelStore.handleCancelCall();
agoraChannelStore.updateLocalStatus(CALLSTATUS.idle);

// 获取当前通话状态
const status = agoraChannelStore.callKitStatus.localClientStatus;

// 获取频道信息
const channelInfo = agoraChannelStore.callKitStatus.channelInfos;
```

### 自定义通话邀请扩展信息

如果需要传递额外的业务数据（如订单号、用户信息等），可以在发起邀请时添加扩展字段。

---

## ❓ 常见问题

### Q1: 插件加载失败？

**A**: 请检查：
1. 是否已制作自定义基座
2. `nativeplugins/Agora-RTC/` 目录结构是否正确
3. `manifest.json` 中是否勾选了 Agora 插件

### Q2: 通话接通后没有声音/画面？

**A**: 请检查：
1. 是否已申请麦克风/摄像头权限
2. 声网 App ID 是否配置正确
3. RTC Token 是否获取成功

### Q3: 如何切换为旧版 Token 获取方式？

**A**: 如果你需要自行部署后端接口获取 Token，可以修改 `components/emCallKit/stores/channelManger.js` 中的 `requestRtcChannelToken` 方法，改为 HTTP 请求方式。

### Q4: 支持哪些平台？

**A**: 目前支持 iOS 和 Android App 端，暂不支持 H5 和小程序。

### Q5: 如何从短信验证码方式登录切换为用户 id+密码登陆？

**A**: 在 `login/loginState` 中将 `usePwdLogin` 配置项改为 `false` 即可。

# 常见问题

- 如何从短信验证码方式登录切换为用户 id+密码登陆？
  > 答：在 login>loginState>usePwdLogin 此配置项改为 false 即可。
