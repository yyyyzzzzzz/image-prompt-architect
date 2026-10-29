# image-prompt-architect · 生图提示词架构师

**中文** | [English](README.en.md)

> 把一句「我想要一张……」变成可直接投产的视觉制作简报：主体、构图、光线、材质、版式、文字、比例、负面约束一次写清。

一个面向 AI 生图（文生图 / 图生图）的 **Agent Skill / 提示词工程知识库**，遵循通用 `SKILL.md` 目录约定，装进 Claude Code 等支持技能的编码助手即可使用。

装它之前，你可能在写「一个赛博朋克风格的猫，高清，8K」；装它之后，AI 拿到的是一份摄影指导也能照着拍的制作简报。

`12 类交付物配方` · `5 个参考文件` · `1563 条真实语料分析` · `MIT`

English: A skill that turns a one-line image idea into a production-ready prompt brief — subject, composition, lighting, material, typography, aspect ratio and negative constraints — with type-specific recipes, a phrase bank, and a curated prompt library analysis.

---

## 先看效果

以下六张图，写法均出自本技能的配方体系。特意挑了最容易翻车的地方看 —— **中文排版、密集文字、结构标注、多人自然互动**：

<table>
  <tr>
    <td align="center" width="33%">
      <img src="assets/showcase/poster-tea-brand.webp" width="250" alt="新中式茶饮海报"><br>
      <sub><b>海报 / 字体排版</b><br>新中式茶饮上新海报 · 3:4</sub>
    </td>
    <td align="center" width="33%">
      <img src="assets/showcase/infographic-hanfu.webp" width="250" alt="明代汉服拆解信息图"><br>
      <sub><b>信息图 / 知识可视化</b><br>明代汉服形制拆解 · 中文标注全对</sub>
    </td>
    <td align="center" width="33%">
      <img src="assets/showcase/ui-ecommerce-app.webp" width="250" alt="电商 App 首页界面"><br>
      <sub><b>UI / 商业界面</b><br>电商 App 首页高保真截图 · 9:16</sub>
    </td>
  </tr>
  <tr>
    <td align="center" width="33%">
      <img src="assets/showcase/poster-skincare.webp" width="250" alt="护肤品电商主视觉"><br>
      <sub><b>商业广告 / 产品视觉</b><br>护肤精华电商主图 · 价格与赠品层级</sub>
    </td>
    <td align="center" width="33%">
      <img src="assets/showcase/infographic-coffee.webp" width="250" alt="咖啡全流程中文信息图"><br>
      <sub><b>信息图 / 流程叙事</b><br>一杯咖啡如何来到你手里 · 五段式</sub>
    </td>
    <td align="center" width="33%">
      <img src="assets/showcase/photo-night-store.webp" width="250" alt="便利店夜景写实照片"><br>
      <sub><b>摄影 / 写实影像</b><br>夏夜便利店门口 · 多人自然互动</sub>
    </td>
  </tr>
</table>

> 示例取自项目自建的 1563 条真实提示词语料库（脚本统计 + 抽样精读），配方体系即由此提炼。图中品牌、人物、价格均为虚构示例。

## 它解决什么问题

| 常见问题 | 本技能的做法 |
|---|---|
| 提示词堆形容词，模型理解发散 | 先路由到 **12 个主类**，再按类型配方填充结构化字段 |
| 不知道商业图该怎么写 | 电商主图、产品 KV、包装、Logo/VI、海报、信息图、UI、摄影、插画、3D、角色、分镜等均有配方 |
| 出图中文字总是糊 | 独立的「文字渲染」章节：引号包围、字重字距、字数上限、排版锚点 |
| 多图/多格不一致 | 「参考图与身份」「多格与一致性」技术段：锁脸、锁版式、锁色 |
| 换模型效果就崩 | 「模型适配」章节说明不同模型对自然语言 / 标签 / 权重的偏好 |
| 改图靠玄学 | 附「迭代修复表」：症状 → 归因 → 改哪个字段 |

## 安装

技能遵循通用的 `SKILL.md` 目录约定，克隆到 Agent 的技能目录即可。

主仓库托管在 [GitHub](https://github.com/yyyyzzzzzz/image-prompt-architect)，[Gitee](https://gitee.com/zhang-wei678/image-prompt-architect) 为国内镜像。

**Claude Code 等**

```bash
# GitHub（主仓库）
git clone https://github.com/yyyyzzzzzz/image-prompt-architect.git \
  ~/.claude/skills/image-prompt-architect

# 国内网络可用 Gitee 镜像
git clone https://gitee.com/zhang-wei678/image-prompt-architect.git \
  ~/.claude/skills/image-prompt-architect
```

Windows 下把目标路径换成 `C:\Users\<用户名>\.claude\skills\image-prompt-architect`。
安装后重启会话，技能列表中出现 `image-prompt-architect` 即生效。

## 使用

直接用自然语言描述你要的图，技能会自动补齐参数并给出结构化提示词：

```
咖啡店开业海报，秋日暖色，要能放地址和二维码
耳机产品图，白底，电商主图风格
把这张照片改成吉卜力画风的插画头像
给我的独立游戏做一套 UI 界面提示词
```

输出包含：主提示词（可直接粘贴）、可替换位（`{主体}` `{背景}` 等）、变体方向、负面提示。

<details>
<summary><b>展开看一个完整例子：从一句话到可直接粘贴的提示词</b></summary>

**你只需要说**：做一张新中式茶饮的新品海报，3:4。

**技能补充的视觉决策**：主视觉（一杯冷泡茶 + 茶叶/柑橘/冰块/金箔）→ 配色（深绿 + 米白 + 金）→ 材质（宣纸纹理、留白、山水点缀）→ 版式（促销信息层级、价格优先级）→ 文字（全部固定，禁止模型自由发挥）。

**主提示词**

```text
设计一张 3:4 竖版海报，用于宣传中国新式茶饮新品。采用新中式视觉风格，整体感觉轻奢、克制。
配色使用深绿色、米白色和金色，带有宣纸纹理、留白、山水点缀和现代版式设计。
主体：一杯视觉上很吸引人的冷泡茶，包含茶叶、柑橘、冰块，以及少量金箔细节。

海报必须准确展示以下精确中文文案：
"山川茶事" "山柚观音" "冷泡系列" "新品上市" "一口清醒，半城入夏"
"限定尝鲜价" "中杯 16 元" "大杯 19 元" "门店活动" "第二杯半价"
"加 3 元升级轻乳版" "每日前 100 名赠限定杯套"
"推荐风味" "观音茶底 / 西柚果香 / 轻乳云顶 / 冰感回甘"
"活动时间 4月20日 至 5月10日" "扫码点单" "SHANCHUAN TEA"
小字："图片仅供参考，请以门店实际售卖为准"

在保证整体高级感的同时，保持清晰的促销层级，不要做得廉价或过于电商化。
尤其注意小字、数字、价格、信息模块以及中文排版美感。
```

注意最后两段的写法 —— **先把审美边界画出来（不要廉价/不要过度电商化），再点名易错项（小字、数字、中文排版）**。这是配方里最容易被忽略、却最影响成片的一段。

</details>

## 里面有什么

```
.
├── SKILL.md                      # 技能主文件：触发条件 + 工作流 + 质量闸门
└── references/
    ├── taxonomy.md               # 12 类提示词分类体系与快速路由表
    ├── recipes.md                # 分类型配方（电商/海报/Logo/信息图/UI/…）
    ├── phrasebank.md             # 词库与句式：景别、镜头、光线、色彩、材质
    ├── examples.md               # 7 个「一句话 → 完整提示词」实例
    ├── library-insights.md       # 1563 条真实提示词语料的写法分析
    └── website-api.md            # 可选：生图接口协议参考
```

## 可选：直接出图

本技能默认只负责**写提示词** —— 不联网、不需要授权码、不需要注册。

如果你已经接了某个生图服务（OpenAI Images、即梦、ComfyUI、SD WebUI 等），`SKILL.md` 的「直接出图」一节可以用来自动调用；`references/website-api.md` 给了一份接口协议参考（鉴权 → 提交 → 轮询），可按你自己的服务改。仓库内不含任何密钥或账号凭证。

只写提示词，完全不需要这一步。

## 隐私与安全

- 仓库内不含任何 API Key、授权码或用户数据。
- 使用第三方生图服务时，请遵守该服务的内容规范；涉及真实人物、品牌时请使用授权素材或改为虚构设定。

## 贡献

欢迎补充类型配方、词库与模型适配经验：

1. Fork 并新建分支；
2. 配方加到 `references/recipes.md`，词条加到 `references/phrasebank.md`，保持现有编号与「字段 → 取值」的表格格式；
3. 新类型需同时在 `references/taxonomy.md` 的路由表登记；
4. 提交 PR，说明新增内容适用于哪类模型、有无实测样例。

## License

[MIT](./LICENSE)

---

如果这套配方帮你少改了几轮图，点个 **Star** 让更多人看到 —— 这是对它最实在的支持。
