# Skills

可分享、可复用的 **Claude Code / Claude Agent Skills**。每个 skill 是一个文件夹（含 `SKILL.md` 指令 +
可选的 `assets/`、`references/`、`scripts/`），这里的版本都已**泛化成跨平台**（macOS / Linux / Windows 通用）。

## 收录

| Skill | 作用 |
|---|---|
| [`html-slide-deck`](html-slide-deck/) | 做一套自包含的 HTML 演示（网页版 PPT）——单个 `index.html` + `assets/`，任意浏览器和 GitHub Pages 都能跑。自带 PDF 下载按钮、F 键全屏演示、翻页笔/键盘导航；固定 16:9 舞台 + 填满每页的阅读型版式；并综合了 Anthropic 官方 `pptx` / `frontend-slides` 的设计最佳实践。也支持把 `.pptx` 转成网页版。 |

## 如何安装使用

把想用的 skill 文件夹复制到 Claude 的 skills 目录即可，之后相关任务会自动触发：

```bash
# 个人级（对所有项目生效）
cp -r html-slide-deck ~/.claude/skills/
# 或 项目级（只对某个项目生效）
cp -r html-slide-deck /path/to/your-project/.claude/skills/
```

- **个人级目录**：macOS/Linux 为 `~/.claude/skills/`；Windows 为 `C:\Users\<你>\.claude\skills\`。
- 装好后无需手动调用——当你说"做个 HTML 版 PPT / 把这个 pptx 做成网页 / 做套幻灯片"时它会自动触发。
- 跨平台说明见各 skill 的 `references/` 文档（命令里会让你先把浏览器/LibreOffice 路径设成本机的）。

## 许可

自用与分享皆可。拿去改成你自己的 house style 也欢迎。
