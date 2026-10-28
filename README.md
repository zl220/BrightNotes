# BrightNotes

Bright 的知识总结与洞见 —— decks、笔记与文章。

## 内容

- **《人类、机器与智能》** — 基于 8 部经典著作的跨学科思考（含 2026 最新 AI 进展）
  👉 [在线阅读](https://zl220.github.io/BrightNotes/human-machine-intelligence/)
- **《Agent 解密与实践》** — 从"什么是 Agent"到"何时该做、怎么做"：一条分界线、一道成本公式、落地的判断与方法
  👉 [在线阅读](https://zl220.github.io/BrightNotes/agent-talk/)

## Skills（可复用工具）

- **[skills/](skills/)** — 可分享、泛化过的 Claude skills（如 `html-slide-deck`：一键做网页版 PPT）。复制到 `~/.claude/skills/` 即可使用。

## 说明

这个仓库用来公开发布我整理的学习总结、演示文稿（HTML deck）和文章。

## 如何添加内容

把要公开的 `.html` 文件放到本目录，然后：

```bash
git add .
git commit -m "add: <内容名称>"
git push
```

## 开启网站（GitHub Pages）

有内容后，到仓库 **Settings → Pages**，Source 选 `main` 分支、根目录，保存。
几分钟后内容就能通过网址访问：

```
https://zl220.github.io/BrightNotes/<文件名>.html
```
