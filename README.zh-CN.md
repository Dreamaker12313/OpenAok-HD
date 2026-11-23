[English](README.md) | **简体中文**

# OpenAokHD

**《帝国时代 II：HD 版》的 Android 原生移植 / 触控增强版，基于 OpenAoK + Thor AoK。**

OpenAokHD 读取玩家自己的 **Age of Empires II: HD Edition（2013）** 数据，在 Android arm64 上运行，
支持 31 个文明、原版 + 征服者 + HD 三个资料片的战役，并为 AYN Thor 这类双屏掌机做了触控命令面板。

> 本项目不包含任何游戏数据。你需要自己拥有正版《帝国时代 II：HD 版》。
> 与 Microsoft / Forgotten Empires 无关联。

---

## 当前功能

### 游戏内容

- **31 个文明**（含 HD DLC 文明，Gaia 另计）
- **国王时代 / 征服者 / 非洲王国 / 蛮王崛起 / 失落帝国** 战役入口
- DLC 战役从 HD 的 `.cpx2` / `.aoe2campaign` 转换成引擎可读的 `.cpx`
- 征服者与 HD 的随机地图、真实世界地图
- 中文界面、中文单位/科技/文明名
- 中文文本按字换行，白色文字 + 黑色八方向描边

### HD 界面

- 解码 HD 的 **32 位 SLP**（`main_32.slp`、`xcam*_32.slp`、`scr*_32.slp` 等）
- 主菜单、战役书、战役地图、自定义战役页使用 HD 背景
- HD 背景作为独立图层绘制，原版按钮/招牌帧保留，避免换图后按钮失效
- 战役地图使用各 DLC 自己的 `*_campaign_render.jpg` / `maps/*.jpg`
- 失落帝国没有官方静态模块图，使用官方 `custom-campaign-fallback` 图

### Android / 触控

- arm64 Android 29+
- 双屏：上屏地图，下屏触控命令面板
- 单屏设备自动使用原版布局
- 数据导入：选择 `OpenAoK-data` 文件夹，校验 manifest 后复制到应用内部
- 应用名 **OpenAokHD**，国王头像圆角图标

---

## 数据包

仓库**不包含**游戏数据。请在自己的电脑上生成：

```sh
python3 -m pip install pillow numpy
python3 android/package.py --hd "/path/to/Age2HD"
```

输出：

```text
android/build/OpenAoK-data/
```

然后：

1. 把 `OpenAoK-data` 复制到手机（SD 卡或内部存储）；
2. 启动 OpenAokHD；
3. 选择 `OpenAoK-data` 文件夹；
4. 等待校验/复制完成，进入游戏。

数据包当前约 **1 GB**，包含：

- HD 图形/声音/地形/语言
- DLC 文明与单位数据
- 15 个 HD 战役（非洲 4、蛮王 4、失落 7）
- HD 界面背景与战役插画

---

## 构建

### Android

需要：

- Android SDK + NDK（arm64）
- CMake / Ninja
- 项目自带的 `android/` Gradle 工程

```sh
cd android
./gradlew assembleDebug
```

APK 输出在 `android/app/build/outputs/apk/debug/`。

> 发布版请使用自己的签名；不要提交 `local.properties`、`*.jks`、`build/`。

### Desktop

```sh
./build.sh
cd build && ./OpenAoK
```

桌面构建主要用于调试引擎逻辑；Android 触控面板只在 Android 构建启用。

---

## 源码结构

```text
src/
  ai/            电脑玩家
  core/          游戏规则、地图、输入
  ecs/           单位、建筑、战斗、触发器
  hal/           SDL、窗口、SLP、音频、网络
  rendering/     地图/单位/UI/文字
  screens/       主菜单、战役、设置、对话框
  scenario/      场景与战役读写
  save/          存档
tools/data/      HD 数据转换、SLP/DRS/战役转换
android/         Android 工程与数据打包
third_party/     SDL2、genieutils、FreeType 等
```

关键 HD 转换脚本：

- `tools/data/convert_hd.py`：HD 数据布局转换
- `tools/data/extract_interface_data.py`：界面/战役资源索引
- `tools/data/convert_dlc_campaigns.py`：HD DLC 战役转换
- `tools/data/slp.py`：8 位 SLP 读写
- `tools/data/encode_rect_slp.py`：RGB 图转 8 位 SLP
- `tools/data/slp32.cpp`：32 位 HD SLP 解码为 PPM

---

## 已知限制

- **DLC 战役 AI info 仍未转换**：触发器/地图/单位正常，电脑 AI 可能偏安静。
- **HD 界面布局仍是 800×600 逻辑坐标**：HD 背景 1366×768 会缩放到 800×600，按钮位置与 HD 图并非逐像素对齐。
- **失落帝国**没有官方静态模块背景/图标，当前使用 fallback 图。
- **战役开场**是分页文本 + 插画，尚未接入官方 `.mm` 多段旁白/音频。
- HD 单位/建筑素材仍是 2013 HD 的 8 位 SLP；不是决定版（DE）的 32 位单位。
- 数据包约 1 GB，首次导入较慢。

---

## 许可与致谢

- 引擎基于 [OpenAoK](https://github.com/Zaydme/openaok)（C++20 / SDL2）
- Thor 双屏/触控改造基于 **Thor AoK**
- 32 位 SLP 解码使用 [genieutils](https://github.com/Tapsa/genieutils)
- SLP/DRS 格式参考 [openage](https://github.com/SFTtech/openage)
- 《帝国时代 II》版权归 Microsoft / Forgotten Empires 所有

代码许可证见 `LICENSE`；第三方组件见 `THIRD_PARTY_NOTICES.md`。
