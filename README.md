# frontend-claude-plugins

Claude Code 前端工具集（marketplace）。

## 安装

在项目 `.claude/settings.json` 中添加：

```json
{
  "extraKnownMarketplaces": {
    "frontend-claude-plugins": {
      "source": {
        "source": "git",
        "url": "ssh://git@github.com/binghuis/frontend-claude-plugins.git"
      }
    }
  },
  "enabledPlugins": {
    "frontend-toolkit@frontend-claude-plugins": true
  }
}
```

## 包含的插件

- **frontend-toolkit**：前端日常 skill/agent 集合（push、changelog、code-review、fsd、orval、react-design 等）
