# image-prompt-architect · 生图提示词架构师

> 把一句「我想要一张……」变成可直接投产的视觉制作简报：主体、构图、光线、材质、版式、文字、比例、负面约束一次写清。

一个面向 AI 生图（文生图 / 图生图）的 **Agent Skill / 提示词工程知识库**。
给编码助手（Claude Code 等支持 `SKILL.md` 的 Agent）装上它，一句话需求就能稳定输出专业级 prompt，而不是「一个赛博朋克风格的猫，高清，8K」这类堆词。

English: A skill that turns a one-line image idea into a production-ready prompt brief — subject, composition, lighting, material, typography, aspect ratio and negative constraints — with type-specific recipes, a phrase bank, and a curated prompt library analysis.

---

## 它解决什么问题

| 常见问题 | 本技能的做法 |
|---|---|
| 提示词堆形容词，模型理解发散 | 先路由到 **12 个主类**，再按类型配方填充结构化字段 |
| 不知道商业图该怎么写 | 电商主图、产品 KV、包装、Logo/VI、海报、信息图、UI、摄影、插画、3D、角色、分镜等均有配方 |
| 出图中文字总是糊 | 独立的「文字渲染」章节：引号包围、字重字距、字数上限、排版锚点 |
| 多图/多格不一致 | 「参考图与身份」「多格与一致性」技术段：锁脸、锁版式、锁色 |
| 换模型效果就崩 | 「模型适配」章节说明不同模型对自然语言 / 标签 / 权重的偏好 |
| 改图靠玄学 | 附「迭代修复表」：症状 → 归因 → 改哪个字段 |

## 目录结构

```
.
├── SKILL.md                      # 技能主文件：触发条件 + 7 步工作流 + 质量闸门
└── references/
    ├── taxonomy.md               # 12 类提示词分类体系与快速路由表
    ├── recipes.md                # 分类型配方（电商/海报/Logo/信息图/UI/…）
    ├── phrasebank.md             # 词库与句式：景别、镜头、光线、色彩、材质
    ├── examples.md               # 7 个「一句话 → 完整提示词」实例
    ├── library-insights.md       # 1551+ 条真实提示词语料的写法分析
    └── website-api.md            # 可选：梦想织机生图接口说明
```

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

## 关于「直接出图」

`SKILL.md` 的「直接出图」一节对接了 [梦想织机](https://pic.1dream.cn) 的开放接口，用授权码鉴权并按积分结算。
**这是可选的**：只写提示词完全不需要授权码、不需要联网、不需要注册。
`references/website-api.md` 只提供接口协议说明，仓库内不含任何密钥或账号凭证。

想换成其它生图服务（OpenAI Images、即梦、ComfyUI、SD WebUI…），改 `SKILL.md` 里「直接出图」一节即可，其余部分与模型无关。

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
