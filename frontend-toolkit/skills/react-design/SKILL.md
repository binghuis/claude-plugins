---
name: react-design
description: 写/改 React 组件或 Hook 必读，严守单一职责与 UI/逻辑分离
---

在回答之前仔细思考并一步一步来；这个问题比看上去要难。

## 单一职责

一个组件/Hook 只解决一个问题。用一句话说不清，或要用「和」连接两件不相关的事，就拆。

## UI 与逻辑分离

组件只做 JSX 编排，逻辑提到 `use` + 组件名的 Hook：

```tsx
// ui/task-panel.tsx
function TaskPanel({ projectId }: Props) {
  const { tasks, isLoading, handleRetry } = useTaskPanel(projectId)
  if (isLoading) return <Skeleton />
  return <List items={tasks} onRetry={handleRetry} />
}

// ui/use-task-panel.ts
function useTaskPanel(projectId: string) {
  const { data, isLoading } = useTaskListQuery(projectId)
  const tasks = useMemo(() => data?.filter(t => t.active).sort(byPriority), [data])
  return { tasks, isLoading, handleRetry: useRetryTaskAction(projectId).execute }
}
```

多 Hook 协作时提取 action Hook（`use*Action`）：

```tsx
// model/use-retry-task.action.ts — 协调 mutation + 状态 + 刷新
function useRetryTaskAction(projectId: string) {
  const { refetch } = useTaskListQuery(projectId)
  const { mutateAsync } = useRetryTaskMutation()
  const [retryingId, setRetryingId] = useState<string | null>(null)

  const execute = useCallback(async (taskId: string) => {
    setRetryingId(taskId)
    await mutateAsync(taskId)
    await refetch()
    setRetryingId(null)
  }, [mutateAsync, refetch])

  return { retryingId, execute }
}
```

逻辑只有一两行时不拆。

## 组件 API

### 组合优于配置

纠结加新 prop 时，先想能否用 children：

```tsx
// ✗
<Card title="..." icon={<X />} actions={[...]} footer={<F />} />

// ✓
<Card>
  <Card.Header><Icon /><Title /></Card.Header>
  <Card.Body>{content}</Card.Body>
</Card>
```

### 多模式拆变体

3+ 个 boolean props 组合出不同模式，或内部满是条件分支时，拆成独立组件，共用部分提取基础组件。2 个模式且差异小不拆。

```tsx
// ✗ 3 个 boolean 组合，改一个模式要担心影响另外两个
function TaskDialog({ isCreate, isEdit, isDuplicate }: Props) {
  return (
    <Modal>
      {isCreate && <CreateForm />}
      {isEdit && <EditForm />}
      {isDuplicate && <DuplicateForm />}
      {isEdit && <DeleteButton />}
    </Modal>
  )
}

// ✓ 各模式独立，共享 Modal 基础组件
function CreateTaskDialog() {
  return <Modal><CreateForm /></Modal>
}

function EditTaskDialog() {
  return <Modal><EditForm /><DeleteButton /></Modal>
}

function DuplicateTaskDialog() {
  return <Modal><DuplicateForm /></Modal>
}
```

### Props 设计

| 规则 | 说明 |
|---|---|
| 最小化表面积 | 能从 context/hook 获取的不经 props |
| 命名 | 回调 `onXxx`，处理器 `handleXxx` |
| 透传原生属性 + ref | 封装原生元素时用 `ComponentPropsWithRef<'element'>` |

## Context 安全消费

Context 无合理默认值时，用 `null` 默认 + 自定义 Hook 守卫：

```tsx
const AuthCtx = createContext<AuthState | null>(null)

function useAuth() {
  const ctx = useContext(AuthCtx)
  if (!ctx) throw new Error('useAuth must be used within <AuthProvider>')
  return ctx
}
```

## 记忆化

代码结构能判断出开销时，直接加：

| 手段 | 何时用 |
|---|---|
| `React.memo` | 子组件渲染成本高（列表/图表/复杂 DOM）且父组件频繁更新 |
| `useMemo` | 数组 filter/map/sort、对象深层转换、或结果作为 memo 化子组件 props |
| `useCallback` | 传给 memo 化子组件的回调，或作为 effect 依赖 |

不需要 memo：渲染成本低的叶子组件、props 每次都变的组件。
