---
name: orval
description: 编写或修改 API 请求时应用，确保正确使用 Orval 生成代码
---

# Orval API 代码生成

本项目使用 [Orval](https://orval.dev) 根据 OpenAPI 规范自动生成类型安全的 API hooks。生成代码位于 `src/shared/api/__generated__/`，**禁止手动修改**。配置文件：`orval.config.ts`。

## 生成产物

每个端点会生成以下内容：

**GET**：原始函数、Query key 工厂、Query options 工厂、React Hook
**POST/PUT/DELETE**：原始函数、Mutation options 工厂、React Hook
**类型**：响应 `XxxResponse`、查询参数 `XxxParams`、请求体 `XxxPayload`

## 使用规则

### 1. 导入来源

- hooks 和函数：`@/shared/api/__generated__/swagger`
- 类型：`@/shared/api/__generated__/swagger.schemas`

### 2. 封装后再使用

不要在 UI 中直接使用生成的 hook，在 FSD 切片的 `api/` 段中创建薄封装：

```ts
// entities/short-drama/api/use-short-play-detail.ts
import { useGetShortPlayFileDetail } from '@/shared/api/__generated__/swagger';

export function useShortPlayDetail(id: number | undefined) {
  const { data, isLoading, refetch } = useGetShortPlayFileDetail(
    { id: id! },
    { query: { enabled: !!id } }
  );
  return { detail: data, isLoading, refetch };
}
```

### 3. 缓存策略

根据业务场景判断是否需要缓存（实时性要求高可设置较短 `staleTime` 或禁用缓存），以及何时刷新：

- `refetch()`：重新请求当前 hook 的数据
- `queryClient.invalidateQueries()`：使缓存失效，触发所有消费该 query 的组件重新获取

```ts
import { useQueryClient } from '@tanstack/react-query';
import { getGetShortPlayFileDetailQueryKey } from '@/shared/api/__generated__/swagger';

const queryClient = useQueryClient();
queryClient.invalidateQueries({ queryKey: getGetShortPlayFileDetailQueryKey({ id }) });
```