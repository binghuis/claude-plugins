# binghuis-plugins

个人维护的 Claude Code 插件市场（marketplace），收录日常高频使用的 skill / hook 集合。

## 安装

在项目或用户级 `.claude/settings.json` 中添加：

```json
{
  "extraKnownMarketplaces": {
    "binghuis-plugins": {
      "source": {
        "source": "git",
        "url": "ssh://git@github.com/binghuis/binghuis-plugins.git"
      }
    }
  },
  "enabledPlugins": {
    "frontend-toolkit@binghuis-plugins": true
  }
}
```

## 更新

marketplace 源为 git 仓库，更新即拉取最新 commit：

```
/plugin marketplace update                          # 更新所有市场
/plugin marketplace update binghuis-plugins  # 仅更新本市场
```

CLI 等价命令：

```bash
claude plugin marketplace update
```

查看已安装插件及版本：

```
/plugin list
```

执行后立即生效，无需清理缓存或重启（缓存位于 `~/.claude/plugins/`）。

## 包含的插件

- **[frontend-toolkit](frontend-toolkit/README.md)** — 前端日常 skill 集合，覆盖分支管理、提交/推送、changelog、code review、bug 定位、注释、FSD 架构、React 设计与模式、skill 脚手架等场景
