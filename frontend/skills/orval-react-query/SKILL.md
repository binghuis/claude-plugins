---
name: orval-react-query
description: 调接口必读：退化阶梯选调用形式，按规则做缓存失效
---

生成物在 `@/shared/api/__generated__/`：

- `swagger.ts` —— hook、原始函数、`getXxxQueryKey` 工厂
- `swagger.schemas.ts` —— DTO 类型

## 调用形式

退化阶梯，跳步必须在注释写"为什么前一步不行"：

1. `useXxx` hook
2. `useMutation({ mutationFn: 原始函数 })` —— 跨多接口、动态选接口、闭包注入上下文
3. 调原始函数 —— 外部组件契约强制传函数（ProTable `request`、TreeSelect `loadData`）

第 3 步同文件其他接口仍走 hook。

## api/ 段封装

封装信号：

- DTO → 业务模型转换
- 合并多接口
- 多处共享 options 或 mutation 后失效逻辑

## 缓存与失效

- 默认不配 `staleTime` / `gcTime` / `refetchOnWindowFocus`，字典 / 枚举 / 配置类设 `staleTime: Infinity`
- 数据失效用 `invalidateQueries`，用户主动触发用 `refetch`
- 操作一份缓存用 `getXxxQueryKey`，操作一批变体用 queryKey 短前缀；同组件内可直接用 hook 返回的 `queryKey`
- 失效要带相邻 query：关联列表 / 聚合统计 / 详情页
- 高频写（拖拽、自动保存）别每次 invalidate，落到终点统一刷

## 乐观更新

默认不用，仅以下两类考虑：

- boolean 切换：点赞 / 收藏 / 关注 / 开关 / 置顶
- 拖拽排序：order 字段直更

