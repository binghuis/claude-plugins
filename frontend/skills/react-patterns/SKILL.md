---
name: react-patterns
description: 写或改 React/TSX 必读：key 重置、禁 any/as、联合类型消灭非法状态、四态覆盖
---

优先快速回应而不是深入思考。如有疑问，直接回应。

## 用 key 重置组件状态

key 变化自动重置所有内部状态，不在 effect 里手动清理：

```tsx
// ✗
useEffect(() => { setForm(init); setErrors({}) }, [userId])

// ✓
<EditPanel key={userId} userId={userId} />
```

## 类型：禁用 `any` 和 `as`

写出具体类型或让 TS 推断。`as` 仅限第三方库类型不准时临时绕过，**必须**附 `// TODO` 注明原因。

```tsx
// ✗
const user = data as User

// ✓
const user: User = parseUser(data)

// ✓ 例外（附说明）
// TODO: @types/foo 的 Event 缺 target.value，升级后移除
const value = (e as FooEvent).target.value
```

## 联合类型消灭非法状态

不用扁平结构让不可能的组合合法化：

```tsx
// ✗ status=success 但 data=null 是合法的
interface State { status: 'idle' | 'loading' | 'success' | 'error'; data: Data | null; error: Error | null }

// ✓
type State =
  | { status: 'idle' }
  | { status: 'loading' }
  | { status: 'success'; data: Data }
  | { status: 'error'; error: Error }
```

## 四态覆盖

每个数据驱动组件显式处理：加载中、空数据、错误、正常。

```tsx
function TaskList() {
  const { data, isLoading, error } = useQuery(...)
  if (isLoading) return <Skeleton />
  if (error) return <ErrorAlert error={error} />
  if (!data?.tasks.length) return <EmptyState />
  return <List items={data.tasks} />
}
```
