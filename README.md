# binghuis-plugins

个人维护的 Claude Code 插件市场，收录日常高频使用的 skill 集合。

## 安装

### 用户级

在 Claude Code 中执行：

```
/plugin marketplace add binghuis/claude-plugins
```

随后 `/plugin` 选择启用所需插件。

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
    "frontend@binghuis-plugins": true,
    "talent@binghuis-plugins": true,
    "meta@binghuis-plugins": true
  }
}
```

## 包含的插件

- **[frontend](frontend/README.md)** — 前端日常 skill 集合，覆盖提交/推送、冲突解决、changelog、code review、bug 定位、注释、FSD 架构、React 设计与模式
- **[talent](talent/README.md)** — AI 时代工程师人才评估：从简历自动定级 + 出多轮面试方案（talent-model + interview-prep）
- **[meta](meta/README.md)** — Skill 元工具：新建 / 审查 SKILL.md（skill-gen / skill-improve）
