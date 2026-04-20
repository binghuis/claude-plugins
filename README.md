# binghuis-plugins

个人维护的 Claude Code 插件市场（marketplace），收录日常高频使用的 skill / hook 集合。

## 安装

### 用户级

在 Claude Code 中执行：

```
/plugin marketplace add binghuis/claude-plugins
```

随后 `/plugin` 选择启用 `frontend-toolkit`。

### 项目级

在项目 `.claude/settings.json` 中添加：

```json
{
  "extraKnownMarketplaces": {
    "binghuis-plugins": {
      "source": {
        "source": "github",
        "repo": "binghuis/claude-plugins"
      }
    }
  },
  "enabledPlugins": {
    "frontend-toolkit@binghuis-plugins": true
  }
}
```

## 包含的插件

- **[frontend-toolkit](frontend-toolkit/README.md)** — 前端日常 skill 集合，覆盖分支管理、提交/推送、changelog、code review、bug 定位、注释、FSD 架构、React 设计与模式、skill 脚手架等场景
