---
name: react-patterns
description: 编写或修改任何 React 代码前必须先加载并严格遵守
---

# React 编码模式

## 1. 用 key 重置组件状态

key 变化自动重置所有内部状态，不要用 effect 手动清理：

```tsx
// Bad
useEffect(() => { setForm(init); setErrors({}) }, [userId])

// Good
<EditPanel key={userId} userId={userId} />
```

## 2. 禁止 any 和 as

唯一允许 `as` 的场景：第三方库类型不准确时作为临时绕过，必须加 `// TODO` 注释说明原因。

## 3. 联合类型消灭非法状态

不要用扁平结构让不可能的组合合法化：

```tsx
// Bad: status=success 但 data=null 是合法的
interface State { status: 'idle' | 'loading' | 'success' | 'error'; data: Data | null; error: Error | null }

// Good
type State =
  | { status: 'idle' }
  | { status: 'loading' }
  | { status: 'success'; data: Data }
  | { status: 'error'; error: Error }
```

## 4. 四态覆盖

每个数据驱动组件显式处理：**加载中、空数据、错误、正常**：

```tsx
function TaskList() {
  const { data, isLoading, error } = useQuery(...)
  if (isLoading) return <Skeleton />
  if (error) return <ErrorAlert error={error} />
  if (!data?.tasks.length) return <EmptyState />
  return <List items={data.tasks} />
}
```
