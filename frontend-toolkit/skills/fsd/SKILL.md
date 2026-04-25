---
name: fsd
description: 改 src/ 时遵守 FSD 层级与导入规则
---

优先快速回应而不是深入思考。如有疑问，直接回应。

## 术语

| 概念 | 定义 |
|---|---|
| 层（Layer） | 顶层职责单位，由上至下排列（app → shared） |
| 切片（Slice） | 层内按业务领域划分的模块（如 `entities/user`、`features/auth`）；`app/` 和 `shared/` 无切片 |
| 段（Segment） | 切片内按技术职责划分的目录（`ui/`、`api/`、`model/`、`lib/`、`config/`） |

## 层级体系

| 层 | 用途 | 有切片 |
|-------|---------|------------|
| `app/` | 初始化、路由、Provider、全局样式 | 否 |
| `pages/` | 基于路由的页面（每个路由一个切片） | 是 |
| `widgets/` | 大型自给自足的 UI 区块 | 是 |
| `features/` | 有业务价值的用户交互 | 是 |
| `entities/` | 业务领域模型 | 是 |
| `shared/` | 与项目无关的基础设施 | 否 |

```
src/
├── app/                        # 无切片
│   ├── providers/
│   ├── routes/
│   └── styles/
├── pages/{页面名}/
│   ├── ui/
│   ├── api/                    # 按需
│   ├── model/                  # 按需
│   └── index.ts
├── widgets/{组件名}/
│   ├── docs/                   # 按需
│   ├── ui/
│   ├── api/ model/ lib/        # 按需
│   └── index.ts
├── features/{功能名}/
│   ├── docs/                   # 按需
│   ├── ui/
│   ├── api/
│   ├── model/                  # {名}.type.ts .store.ts .schema.ts
│   └── index.ts
├── entities/{实体名}/
│   ├── docs/                   # 按需
│   ├── ui/
│   ├── api/
│   ├── model/                  # {名}.type.ts .schema.ts .const.ts .logic.ts
│   └── index.ts
└── shared/                     # 无切片
    ├── ui/                     # 设计系统组件
    ├── api/                    # API 客户端、拦截器
    ├── lib/                    # 工具函数
    └── config/                 # 环境变量、常量
```

`widgets/` `features/` `entities/` 三层切片下可挂一个 `docs/`，沉淀该切片的业务说明，与代码段平级但不参与导入。

## 导入规则

**只能从更低的层导入，不允许同层或向上。**

```
层级（上 → 下）：          可导入：
─────────────────────    ────────────────────────────────────
app/                     pages, widgets, features, entities, shared
pages/                   widgets, features, entities, shared
widgets/                 features, entities, shared
features/                entities, shared
entities/                shared（跨实体使用 @x）
shared/                  仅外部包
```

| 违规类型 | 示例 | 修复方式 |
|-----------|---------|-----|
| 同层跨切片 | `features/auth` → `features/user` | 提取到 `entities/` 或 `shared/` |
| 向上导入 | `entities/user` → `features/auth` | 下移到更低的层 |
| shared 向上导入 | `shared/` → `entities/` | 去掉对内部层的依赖 |

**例外：** `app/` 和 `shared/` 没有切片，允许内部跨导入。

## 决策树

### 代码放在哪？

```
├─ 不含业务逻辑的可复用基础设施？ → shared/
├─ 业务领域对象/数据模型？ → entities/
├─ 有业务价值的用户交互？
│  ├─ 跨页面复用或逻辑复杂 → features/
│  └─ 仅单页面且简单 → 留在 pages/ 切片内
├─ 大型自给自足的 UI 区块？ → widgets/
├─ 路由/页面组件？ → pages/
└─ 全局初始化/配置？ → app/
```

### Feature 还是 Entity？

| 类型 | 含义 | 示例 |
|---|---|---|
| Entity（名词） | 有身份标识的事物 | user、product、order |
| Feature（动词） | 有副作用的动作 | auth、add-to-cart、checkout |

### 放在哪个段？

```
ui/      → 组件、样式
api/     → 后端请求、数据获取
model/   → 类型、Schema、Store、业务逻辑、Action
lib/     → 工具函数（切片内或 shared/ 中）
config/  → 功能开关、常量
```

## 命名

文件名、目录名统一 kebab-case，如 `user-card.tsx`、`task-status.const.ts`、`workflow-panel/`。

单文件能完成的用文件（如 `shared/lib/time.ts`），多个相关文件组成的用目录（如 `shared/lib/polling-scheduler/`）。

文件后缀只用下表，禁止自创（`.util.ts`、`.helpers.ts` 等）。同构目录（如 `ui/`）不需要后缀。

| 后缀 | 用途 | 示例 |
|---|---|---|
| `.type.ts` | 类型定义 | `user.type.ts` |
| `.schema.ts` | 校验规则 | `user.schema.ts` |
| `.store.ts` | Zustand Store | `auth.store.ts` |
| `.const.ts` | 常量、映射 | `task-status.const.ts` |
| `.logic.ts` | 纯函数 | `order.logic.ts` |
| `.action.ts` | 协调多个 Hook 的操作函数 | `task-retry.action.ts` |

## 公共 API

每个切片必须通过 `index.ts` 暴露公共 API，外部只能从此文件导入。

```typescript
// ✅ 正确
import { UserCard, type User } from '@/entities/user';

// ❌ 错误 — 穿透内部路径
import { UserCard } from '@/entities/user/ui/user-card';

// ❌ 错误 — 通配符导出
export * from './ui';

// ✅ 正确 — 显式具名导出
export { UserCard } from './ui/user-card';
```

切片内使用相对导入；从自身 index 导入会造成循环依赖。

## Hook 与 Action 放置

按功能放，不按"是什么"放。

| Hook 职责 | 放置位置 | 示例文件 |
|---------------------|---------|---------|
| 管理/派生状态 | 所属切片的 `model/` | `features/auth/model/auth.store.ts` → `useAuthStore` |
| 获取/修改数据 | 所属切片的 `api/` | `entities/user/api/queries.ts` → `useUserQuery` |
| UI 交互逻辑 | 与组件同级的 `ui/` | `features/auth/ui/use-login-form.ts` |
| 通用工具（无业务） | `shared/lib/` | `shared/lib/use-debounce.ts` |

## 跨实体引用（@x）

实体间引用通过 `@x` 目录：

```
entities/product/@x/order.ts    → 专为 order 实体暴露的类型
entities/order/model/           → import from '@/entities/product/@x/order'
```

跨引用过多视为信号：合并实体而非继续暴露 `@x`。

## 反模式

| 反模式 | 修复 |
|--------------|-----|
| 泛化段名 `components/` `hooks/` | 用 `ui/` `lib/` `model/` |
| shared 中放业务逻辑 | 移到 `entities/` |
| 简单组合做成 widget | 简单组合直接在页面中完成 |
| 所有交互都做成 feature | 仅复用或逻辑复杂的交互做 feature |
