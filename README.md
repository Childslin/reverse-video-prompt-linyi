# reverse-video-prompt

把参考短视频反推出可直接用于 AI 视频模型的提示词，并支持基于生成结果持续迭代。

适合场景：

- TikTok / Reels / Shorts UGC 视频反推
- TikTok Shop / 电商带货视频复刻
- 原片 vs 生成片差异诊断
- 生成失败后的提示词迭代
- 把成功/失败经验沉淀成可复用 skill 规则

## 核心能力

- 识别产品类别、卖点、镜头意图和证明动作
- 锁定前 3 秒产品钩子和音画信息密度
- 分析生成片哪里不像原片
- 诊断画面、音频、物理逻辑、产品显著性、人物/场景真实感
- 输出新版可复制提示词
- 判断经验是否值得沉淀进 skill
- 支持 GitHub issue/PR 形式的社区迭代

## 基本用法

```text
使用 reverse-video-prompt 帮我把这个视频反推出可直接生成同类视频的提示词。
忽略 TikTok 水印。
```

## 安装

把仓库克隆到 Codex skills 目录：

```bash
git clone https://github.com/zhengzhentao86/reverse-video-prompt.git ~/.codex/skills/reverse-video-prompt
```

已经安装过时更新：

```bash
git -C ~/.codex/skills/reverse-video-prompt pull
```

安装后在 Codex 里直接说：

```text
使用 reverse-video-prompt 帮我反推这个视频提示词。
```

生成后继续迭代：

```text
这是我用上版提示词生成出来的视频。
你对比一下原片和生成片，看看哪里不像，给新版提示词。
如果这是通用问题，也帮我提炼成 skill 规则。
```

## 自迭代原则

不是每个失败都应该修改 skill。

每次生成失败后，先判断：

- 这是单条提示词的问题，还是通用规则缺失？
- 这个问题能否跨产品复用？
- 这个问题是否能通过视频/音频证据观察到？
- 这条规则是否足够短、可执行、不和已有规则冲突？

只有可复用的规律才进入 `SKILL.md` 或 `references/`。

## 自动上传吗？

默认不会；显式授权和配置后可以自动同步。

公开版本不应该悄悄上传使用记录、提示词、原片、生成片或任何素材。要开启 Feishu/Lark Base 后台同步，需要用户在本地创建 `config/lark-backend.local.json`，并同时设置：

- `enabled: true`
- `auto_upload: true`
- `consent_acknowledged: true`

开启后，skill 会把同一次复刻迭代持续写入同一条 Base 记录：

- 原视频
- 反推提示词 1
- 复刻视频 1
- 复刻视频 1 修改意见
- 反推提示词 2
- 复刻视频 2
- 复刻视频 2 修改意见

本地配置示例：

```bash
cp config/lark-backend.example.json config/lark-backend.local.json
python3 scripts/upload_lark_backend.py --help
```

公开协作也可以使用 GitHub issue/PR 流程：

1. 本地分析生成片和原片。
2. 本地生成迭代包。
3. 用户确认内容可公开。
4. 再手动或用脚本创建 GitHub issue/PR。

可用脚本：

```bash
python3 scripts/export_iteration_case.py \
  --title "案例标题" \
  --product-category "产品类别" \
  --observed-failure "具体问题" \
  --repo owner/reverse-video-prompt \
  --upload issue \
  --yes
```

不加 `--upload issue --yes` 时，只会导出本地文件，不会联网上传。

如果要让很多人统一上传到同一个中心后台，推荐做一个明确的安装/首次运行授权流程，或者用带 OAuth 的中转服务写入维护者的 Base。不要把维护者的本地授权、私有配置或访问凭证提交到公开仓库。

## 贡献案例

请优先使用 GitHub issue 模板提交：

- 产品类别
- 目标平台/模型
- 原提示词
- 原片描述或有权分享的素材
- 生成片描述或有权分享的素材
- 具体问题
- 你认为可复用的规则

不要公开上传无授权原始视频、客户素材、隐私素材或本地文件路径。

## 目录

```text
reverse-video-prompt/
├── SKILL.md
├── README.md
├── CONTRIBUTING.md
├── references/
│   ├── prompt-framework.md
│   ├── iteration-workflow.md
│   └── lark-backend.md
├── scripts/
│   ├── README.md
│   ├── export_iteration_case.py
│   └── upload_lark_backend.py
├── config/
│   └── lark-backend.example.json
├── evals/
│   └── evals.json
└── .github/
    ├── ISSUE_TEMPLATE/
    │   └── iteration-case.yml
    └── pull_request_template.md
```

## 重要文件

- `SKILL.md`: 触发说明和主工作流
- `references/prompt-framework.md`: 视频反推和提示词生成框架
- `references/iteration-workflow.md`: 生成后复盘、自迭代和社区贡献协议
- `references/lark-backend.md`: Feishu/Lark Base 后台同步协议
- `scripts/export_iteration_case.py`: 本地导出迭代案例，可选显式创建 GitHub issue
- `scripts/upload_lark_backend.py`: 授权后把复刻迭代数据写入 Lark Base
- `config/lark-backend.example.json`: Lark 后台配置模板，本地实际配置不要提交
- `evals/evals.json`: 回归测试案例种子
- `CONTRIBUTING.md`: 贡献说明
