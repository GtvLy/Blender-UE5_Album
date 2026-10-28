# 晴るる（はるる）— 专辑封面 3D 场景再现

> 以日本乐队 **あたらよ（Atarayo）** 歌曲《晴るる》的专辑封面为蓝本，用 **Blender + Unreal Engine 5.6** 还原「雨过天晴」的清新治愈氛围。

## 项目简介

本项目是一次 **「从 2D 插画到实时 3D」的视觉转译练习**，定位为**单帧静帧渲染**（非可交互游戏）。作品使用 Blender 手工建模核心资产（人物、透明伞、栅栏等），再从 Fab 获取蓝花楹（Jacaranda）植物素材，最终在 Unreal Engine 5.6 中完成场景搭建、天气与光照调试，并通过影视级摄像机（CineCamera）＋ Movie Render Queue 输出高质量静帧。

- **参考歌曲**：あたらよ《晴るる》 — [网易云音乐](https://music.163.com/#/song?id=1839931917)
- **制作工具**：Blender（建模）＋ Unreal Engine 5.6（场景 / 光照 / 渲染）
- **项目类型**：个人作品集（Portfolio / Gallery）

## 成品展示

最终通过 Movie Render Queue 输出 4 张静帧，规格均为 **2048 × 2048**（正方形 8-bit RGBA PNG）：

| 渲染成片 | 说明 |
| --- | --- |
| ![机位 1](Output/0.png) | 机位 1 |
| ![机位 2](Output/1.png) | 机位 2 |
| ![机位 2 修正版](Output/1-fix.png) | 机位 2 的修正版（重渲染） |
| ![机位 3](Output/2.png) | 机位 3 |

> `1-fix` 说明在首版渲染后，作者针对机位 2 的光影 / 构图做了调整并重新出图，体现了「渲染 → 审片 → 修正」的迭代流程。

## 技术亮点

| 模块 | 实现 |
| --- | --- |
| 全局光照 / 反射 | **Lumen** 动态全局光照与实时反射 |
| 天空与天气 | **Sky Atmosphere（天空大气）＋ Volumetric Cloud（体积云）＋ 体积雾**，云使用自定义云材质 `m_CustomCloud` |
| 光照 | 定向光（太阳）＋ **Sun Position 插件**精确控制太阳方位 + SkyLight 环境光 |
| 角色渲染 | 启用 **Skin Cache（皮肤缓存着色器）** 提升皮肤与头发质感 |
| 透明伞 | 半透明材质（Translucent）让天光云影穿透伞面 |
| 摄像机 | **CineCameraActor + Level Sequence**，对焦距 / 光圈 / 手动对焦做关键帧控制，营造浅景深 |
| 后期 | 关闭自动曝光 / 运动模糊 / Bloom，使用 **PostProcessVolume** 手动调色，整体高明度、低饱和、冷蓝 + 紫色点缀 |
| 输出 | **Movie Render Queue**，含空间 / 时间抗锯齿采样与体积云质量控制 |

## 目录结构

```
晴るる-あたらよ/
├── .blend/                        # Blender 源文件
│   ├── Character.blend(+.blend1)  #   人物模型
│   ├── Umbrella.blend(+.blend1)   #   伞（普通版）
│   └── Umbrella_J.blend(+.blend1) #   伞（精细版）
├── .fbx/                          # 导出中间格式
│   ├── Character.fbx
│   └── Umbrella.fbx
├── .uproject/Art/                 # UE5 工程（Art.uproject）
│   ├── Art.uproject
│   ├── Config/                    #   引擎 / 编辑器 / 平台配置
│   └── Content/                   #   内容资产（People / Umbrella / Tree 等）
├── Output/                        # 最终渲染成片
│   ├── 0.png / 1.png / 1-fix.png / 2.png
├── Reference/                     # 参考图
│   ├── Album-Cover.jpg
│   └── Character.png
├── .gitattributes                 # Git LFS 规则
└── .gitignore
```

## 资产制作管线

```
Blender 建模（人物 / 伞 / 栅栏）
        │ 导出
        ▼
FBX（.fbx 中间格式）
        │ Interchange 导入（UE5.6 默认管线）
        ▼
UE5 静态网格 + 材质（People / Umbrella）
```

- 角色与伞在 Blender 中建模后导出为 **FBX**，经 UE5.6 的 **Interchange** 管线导入。
- 材质在导入时由 `MF_PhongToMetalRoughness` 自动从传统 Phong 属性转换为金属 / 粗糙度工作流。
- 树木为 **Fab** 下载的蓝花楹素材，以网格部件形式进入 `Tree/` 目录。

## 引擎与插件

- **引擎版本**：Unreal Engine 5.6（`EngineAssociation: "5.6"`）
- **项目模板**：`TP_ME_BlankBP`（影视 / 虚拟制片 Blank 模板）
- **图形 API**：DirectX 12（SM6）
- **直接相关插件**：`MovieRenderPipeline`（出图）、`SunPosition`（太阳方位）、`HDRIBackdrop`、`SequencerScripting`

## 更多文档

- 设计文档与详细技术文档见 `docs/` 目录（另附，或参考项目简要说明）。
