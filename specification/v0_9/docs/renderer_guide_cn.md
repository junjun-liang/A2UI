# 统一架构与实现指南

本文档描述了 A2UI 客户端实现的架构。该设计将关注点分离到不同的层中，以最大化代码复用、确保内存安全，并在添加自定义组件时提供简化的开发者体验。

核心数据结构和渲染组件完全与正在渲染的特定 UI 无关。相反，它们与**目录**交互。在目录内，实现遵循结构化的分层：从纯粹的**组件模式**一直到绘制像素的**框架特定适配器**。

## 实现拓扑
由于 A2UI 跨越多种语言和 UI 范式，这些架构边界的严格程度和位置将根据目标生态系统而有所不同。

### 动态语言（例如 TypeScript / JavaScript）
在 Web 等高度动态的生态系统中，架构通常分为多个包，以在多样化的 UI 框架（React、Angular、Vue、Lit）之间最大化代码复用。
*   **核心库 (`web_core`)**：实现核心数据层、组件模式和通用绑定层。由于 TS/JS 具有强大的运行时反射能力，核心库可以提供通用绑定器，自动处理所有数据绑定而无需框架特定代码。
*   **框架库 (`react_renderer`、`angular_renderer`)**：实现框架特定适配器和实际的视图实现（React 的 `Button`、`Text` 等）。

### 静态语言（例如 Kotlin、Swift、Dart）
在静态类型语言（以及 Dart 等 AOT 编译语言）中，运行时反射通常受限或因性能原因不被推荐。
*   **核心库（例如 `kotlin_core`）**：实现核心数据层和组件模式。核心库通常为标准基础目录组件提供手动实现的**绑定层**。这确保即使在静态环境中，基础组件也有标准化的、框架无关的响应式状态定义。
*   **代码生成（未来/可选）**：虽然核心库从手动绑定器开始，但最终可能提供代码生成（例如 KSP、Swift Macros）来自动创建自定义组件的绑定器。
*   **自定义组件**：在没有代码生成的情况下，实现新的临时组件的开发者通常使用**"无绑定器"实现**流程，允许直接绑定到数据模型而无需中间样板代码。
*   **框架库（例如 `compose_renderer`）**：使用预定义的绑定器连接到原生 UI 状态，并实现实际的视觉组件。

### 组合核心 + 框架库（例如 Swift + SwiftUI）
在由单一 UI 框架主导的生态系统（如 iOS 的 SwiftUI）中，开发者通常构建单一的统一库，而不是将核心和框架分为单独的包。
*   **宽松的边界**：核心库和框架库之间的严格分离可以放宽。通用的 `ComponentContext` 和框架特定的适配器逻辑通常紧密集成。
*   **为什么保留绑定层？**：即使在组合库中，定义中间绑定层仍然强烈推荐。它标准化了 A2UI 数据如何解析为响应式状态。这允许采用该库的开发者轻松编写知名组件的替代实现，而无需重写复杂的、样板繁重的 A2UI 数据订阅逻辑。

---

## 1. 核心数据层（框架无关）

数据层负责接收传输协议（JSON 消息）、解析它们，并维护长期存在的可变状态对象。此层在所有编程语言中遵循完全相同的设计（仅有微小的语法差异），在移植到新框架时**不需要重新设计**。

> **关于语言和框架的说明**：虽然本文档中的示例以 TypeScript 提供以保持清晰，但 A2UI 数据层旨在可以用任何语言（例如 Java、Python、Swift、Kotlin、Rust）实现，并保持完全独立于任何特定的 UI 框架。

它由三个子组件组成：处理层、模型和上下文层。

### 前提条件

要有效地实现数据层，您的目标环境需要两个基础工具：模式库和可观察库。

#### 1. 模式库
为了表示和验证组件和函数 API，数据层需要一个**模式库**。

*   **理想选择**：一个库（如 TypeScript 中的 **Zod** 或 Python 中的 **Pydantic**），允许以编程方式定义模式，并能够根据这些定义验证原始 JSON 数据。
*   **能力生成**：该库最好支持将这些编程定义导出为标准 JSON 模式，用于 `getClientCapabilities` 负载。
*   **备选方案**：如果目标语言没有合适的编程库，可以使用原始的 **JSON 模式字符串**、`Codable` 结构体或 `kotlinx.serialization` 类。

#### 2. 可观察库
A2UI 依赖标准的观察者模式来响应式地更新 UI。数据层和客户端函数必须能够返回流或响应式变量，这些变量持有初始值并发出后续更新。

*   **要求**：您需要一个类似 "BehaviorSubject" 或有状态流的响应式机制——它必须在订阅时同步提供当前值，并通知监听器未来的变更。关键是，订阅必须提供明确的**取消订阅**机制（例如 `dispose()` 方法或返回的清理函数），以防止组件移除时的内存泄漏。
*   **各平台示例**：
    *   **Web (TypeScript/JavaScript)**：RxJS (`BehaviorSubject`)、Signals 或简单的自定义 `EventEmitter` 类。
    *   **Android (Kotlin)**：Kotlin Coroutines (`StateFlow`) 或 Android `LiveData`。
    *   **iOS (Swift)**：Combine (`CurrentValueSubject`) 或 SwiftUI `@Published` / `Binding`。
*   **指导**：如果您的生态系统没有轻量级的内置选项，您可以轻松实现一个带有 `subscribe` 和 `unsubscribe` 方法的简单观察者类，保持较低的外部依赖。

### 设计原则

为确保一致性和可移植性，数据层实现依赖标准模式而非框架特定的库。

#### 1. "添加"组合模式
我们严格将**构造**与**组合**分离。父容器不作为子组件的工厂。这种解耦允许子类在不破坏父类的情况下演化其构造函数签名。它还通过允许注入模拟子组件来简化测试。

*   **模式：**
    ```typescript
    // 父组件对子组件的构造函数选项一无所知
    const child = new ChildModel(config); 
    parent.addChild(child); 
    ```

#### 2. 标准观察者模式（可观察性）
模型必须为渲染层提供观察变更的机制。

**原则：**
1.  **低依赖**：优先使用"最小公分母"机制而非复杂的响应式库。
2.  **多播**：该机制必须支持同时注册多个监听器。
3.  **取消订阅模式**：必须有明确的方式停止监听并防止内存泄漏。
4.  **负载支持**：该机制必须传达特定的数据更新和生命周期事件。
5.  **一致性**：此模式在整个状态模型中统一使用。

#### 3. 细粒度响应性
模型设计为通过细粒度更新而非全 Surface 刷新来支持高性能渲染。
*   **结构变更**：`SurfaceComponentsModel` 在添加/移除项时通知。
*   **属性变更**：`ComponentModel` 在其特定配置变更时通知。
*   **数据变更**：`DataModel` 仅通知订阅了变更路径的订阅者。

### 关键接口和类
*   **`MessageProcessor`**：接收原始 JSON 流的入口点。
*   **`SurfaceGroupModel`**：所有活动 Surface 的根容器。
*   **`SurfaceModel`**：表示单个 UI Surface 的状态。
*   **`SurfaceComponentsModel`**：组件配置的扁平集合。
*   **`ComponentModel`**：特定组件的原始配置。
*   **`DataModel`**：应用数据的专用存储。
*   **`DataContext`**：围绕数据模型、可用函数和组件基础路径的抽象，允许组件实现通过简单的 API 获取和订阅动态值。从相同组件 ID 实例化但具有不同基础路径（例如因为它们是*模板*的不同实例）的不同组件实例可以有不同的 `DataContext` 实例。
*   **`ComponentContext`**：将组件与其数据作用域配对的绑定对象。

### 模型
这些类被设计为数据的"简单容器"。它们持有 A2UI 状态的快照，并包含实现可观察性的逻辑。它们可以验证变更以防止系统进入不一致状态。解码 A2UI 消息和更新模型层的逻辑应在 MessageProcessor 中。解包数据模型和函数引用的逻辑应在上下文层中。

**关键特征：**
*   **可变**：其属性可以随时间更新。
*   **可观察**：它们提供监听这些更新的机制。
*   **封装组合**：父模型持有对子模型的引用，但不构造它们。

它们根据 A2UI 中数据和组件树的结构层次化组织，例如 SurfaceGroup、Surface、Component。在每个 SurfaceModel 中，ComponentModels 表示为扁平列表，视图层次结构的构建在每个 UI 框架的 Surface 渲染逻辑中处理。

#### SurfaceGroupModel 与 SurfaceModel
活动 Surface 及其目录、数据和组件的根容器。

```typescript
interface SurfaceLifecycleListener<T> {
  onSurfaceCreated?: (s: SurfaceModel<T>) => void; // 注册新 Surface 时调用
  onSurfaceDeleted?: (id: string) => void; // 移除 Surface 时调用
}

class SurfaceGroupModel<T> {
  addSurface(surface: SurfaceModel<T>): void;
  deleteSurface(id: string): void;
  getSurface(id: string): SurfaceModel<T> | undefined;
  
  readonly onSurfaceCreated: EventSource<SurfaceModel<T>>;
  readonly onSurfaceDeleted: EventSource<string>;
  readonly onAction: EventSource<ActionEvent>;
}

interface ActionEvent {
  surfaceId: string;
  sourceComponentId: string;
  name: string;
  context: Record<string, any>;
}

type ActionListener = (action: ActionEvent) => void | Promise<void>; // 用户交互处理器

class SurfaceModel<T> {
  readonly id: string;
...
  readonly catalog: Catalog<T>; // 包含组件实现的目录
  readonly dataModel: DataModel; // 作用域应用数据
  readonly componentsModel: SurfaceComponentsModel; // 扁平组件映射
  readonly theme?: any; // 主题参数（根据 catalog.theme 验证）

  readonly onAction: EventSource<ActionEvent>;
  dispatchAction(action: ActionEvent): Promise<void>;
}
```
#### `SurfaceComponentsModel` 与 `ComponentModel`
管理扁平映射中组件的原始 JSON 配置，每个组件 ID 对应一个条目。这表示 ChildList 模板解析之前的原始组件数据，模板可以实例化具有相同 ID 的单个组件的多个实例。

```typescript
class SurfaceComponentsModel {
  get(id: string): ComponentModel | undefined;
  addComponent(component: ComponentModel): void;
  
  readonly onCreated: EventSource<ComponentModel>;
  readonly onDeleted: EventSource<string>;
}

class ComponentModel {
  readonly id: string;
  readonly type: string; // 组件名称（例如 'Button'）
  
  get properties(): Record<string, any>; // 当前原始 JSON 配置
  set properties(newProps: Record<string, any>);
  
  readonly onUpdated: EventSource<ComponentModel>; // 任何属性变更时调用
}
```
#### `DataModel`
Surface 应用数据的专用存储（MVVM 中的"模型"）。

```typescript
interface Subscription<T> {
  readonly value: T | undefined; // 最新求值的值
  unsubscribe(): void; // 停止监听
}

class DataModel {
  get(path: string): any; // 将 JSON Pointer 解析为值
  set(path: string, value: any): void; // 在路径处原子更新
  subscribe<T>(path: string, onChange: (v: T | undefined) => void): Subscription<T>; // 响应式路径监控
  dispose(): void; // 生命周期清理
}
```

#### JSON Pointer 实现规则
为确保各实现之间的对等性，`DataModel` 必须遵循以下规则：

**1. 自动类型化（自动激活）**
在嵌套路径（例如 `/a/b/0/c`）设置值时，如果中间段不存在，模型必须创建它们：
*   查看路径中的*下*一段。
*   如果下一段是数字（例如 `0`、`12`），将当前段初始化为**数组** `[]`。
*   否则，将其初始化为**对象** `{}`。
*   **错误情况**：如果更新尝试穿越原始值（例如在 `/a` 已经是字符串时设置 `/a/b`），则抛出异常。

**2. 通知策略（冒泡与级联）**
特定路径的变更必须触发相关路径的通知以确保 UI 一致性：
*   **精确匹配**：通知所有订阅了修改路径的订阅者。
*   **祖先通知（向上冒泡）**：通知所有父路径的订阅者。例如，更新 `/user/name` 必须通知 `/user` 和 `/` 的订阅者。
*   **后代通知（向下级联）**：通知嵌套在修改路径*下*的所有路径的订阅者。例如，替换整个 `/user` 对象必须通知 `/user/name` 的订阅者。

**3. Undefined 处理**
*   **对象**：将键设置为 `undefined` 应从对象中移除该键。
*   **数组**：将索引设置为 `undefined` 应保持数组的长度，但将该特定索引设置为 `undefined`（稀疏数组支持）。

#### 类型强制转换标准
为确保数据层在所有平台（例如 TypeScript、Swift、Kotlin）上行为一致，解析动态值时必须遵循以下强制转换规则：

| 输入类型                 | 目标类型 | 结果                                                                  |
| :------------------------- | :---------- | :---------------------------------------------------------------------- |
| `String` ("true", "false") | `Boolean`   | `true` 或 `false`（不区分大小写）。任何其他字符串映射为 `false`。 |
| `Number` (非零)        | `Boolean`   | `true`                                                                  |
| `Number` (0)               | `Boolean`   | `false`                                                                 |
| `Any`                      | `String`    | 区域设置无关的字符串表示                                    |
| `null` / `undefined`       | `String`    | `""`（空字符串）                                                     |
| `null` / `undefined`       | `Number`    | `0`                                                                     |
| `String` (数字)         | `Number`    | 解析的数值或 `0`                                             |


### 上下文层（瞬态窗口）
**上下文层**由在渲染过程中按需创建的短生命周期对象组成，用于解决"作用域"和绑定解析的问题。

由于数据层是组件的扁平列表和原始数据树，它本身不知道层次结构或当前数据作用域（例如，在列表迭代内部）。上下文层弥合了这一差距。适当的"窗口"由结构父组件（如 `List`）确定，它们为其子组件生成特定的 `DataContext` 作用域。

#### `DataContext` 与 `ComponentContext`

```typescript
class DataContext {
  constructor(dataModel: DataModel, path: string);
  readonly path: string;
  set(path: string, value: any): void;
  resolveDynamicValue<V>(v: any): V;
  subscribeDynamicValue<V>(v: any, onChange: (v: V | undefined) => void): Subscription<V>;
  nested(relativePath: string): DataContext;
}

class ComponentContext {
  constructor(surface: SurfaceModel<T>, componentId: string, basePath?: string);
  readonly componentModel: ComponentModel; // 实例配置
  readonly dataContext: DataContext; // 实例的数据作用域
  readonly surfaceComponents: SurfaceComponentsModel; // 逃生舱口
  dispatchAction(action: any): Promise<void>; // 将操作传播到 Surface
}
```

#### 组件间依赖（"逃生舱口"）
虽然 A2UI 组件设计为自包含的，但某些渲染逻辑需要了解子组件或兄弟组件的属性。

**Weight 示例**：在标准目录中，`Row` 或 `Column` 容器通常需要知道其子组件是否具有 `weight` 属性，以便在 Flutter 或 SwiftUI 等框架中正确应用 `Flex` 或 `Expanded` 逻辑。

**用法**：组件实现可以使用 `ctx.surfaceComponents` 检查同一 Surface 中其他组件的元数据。
> **指导**：此模式通常不被鼓励，因为它增加了耦合。仅当框架的布局引擎无法仅通过显式组件属性满足时，才将其作为必要的逃生舱口使用。


### 处理层 (`MessageProcessor`)
**处理层**充当"控制器"。它接收原始的 A2UI 消息流（`createSurface`、`updateComponents` 等），解析它们，并相应地修改底层数据模型。

它还通过 `getClientCapabilities()` 处理生成客户端能力负载。

```typescript
class MessageProcessor<T> {
  readonly model: SurfaceGroupModel<T>; // 所有 Surface 的根状态容器
  
  constructor(catalogs: Catalog<T>[], actionHandler: ActionListener);

  processMessages(messages: any[]): void; // 接收原始 JSON 消息流
  addLifecycleListener(l: SurfaceLifecycleListener<T>): () => void; // 监控 Surface 生命周期
  getClientCapabilities(options?: CapabilitiesOptions): any; // 生成通告负载
}
```

#### 组件生命周期：更新 vs. 重建
处理 `updateComponents` 时，处理器必须小心处理现有 ID：
*   **属性更新**：如果组件 `id` 存在且 `type` 与现有实例匹配，则更新 `properties` 记录。这会触发组件的 `onUpdated` 事件。
*   **类型变更（重建）**：如果消息中的 `type` 与现有实例的类型不同，处理器必须从模型中移除旧组件实例并创建新的。这确保框架渲染器正确重置其内部状态和组件类型。

#### 生成客户端能力和模式类型

要动态生成 `a2uiClientCapabilities` 负载（特别是 `inlineCatalogs` 数组），渲染器需要将其内部的组件和主题模式转换为符合 A2UI 协议的有效 JSON 模式。

A2UI 严重依赖共享的模式定义（如 `common_types.json` 中的 `DynamicString`、`DataBinding` 和 `Action`）。然而，大多数模式验证库（如 Zod）本身不支持开箱即用地发出外部 JSON 模式 `$ref` 指针。

为解决此问题，通用类型必须在 JSON 模式转换过程中是**可检测的**。这通常通过使用其 `description` 属性"标记"模式来实现（例如 `REF:common_types.json#/$defs/DynamicString`）。

当 `getClientCapabilities()` 转换内部模式时：
1. 它将定义转换为原始 JSON 模式。
2. 它遍历模式树，查找以 `REF:` 标记开头的字符串描述。
3. 它剥离标记并将整个节点替换为有效的 JSON 模式 `$ref` 对象。

---

## 2. 目录 API 与绑定（框架无关）

A2UI 中的组件和函数组织为**目录**。目录定义了可用于渲染的组件以及可以执行的客户端逻辑。

### 目录 API
目录将组件定义（以及可选的函数定义）组合在一起，以便 `MessageProcessor` 可以验证消息并向服务器提供能力。

```typescript
class Catalog<T> {
  readonly id: string; // 唯一目录 URI（例如 "https://mycompany.com/catalog.json"）
  readonly components: ReadonlyMap<string, T>;
  readonly functions?: ReadonlyMap<string, FunctionImplementation>;
  readonly theme?: Schema; // 主题参数的模式（例如 Zod 对象）

  constructor(id: string, components: T[], functions?: FunctionImplementation[], theme?: Schema) {
    // 初始化属性
  }
}
```

### 创建自定义目录
可扩展性是 A2UI 的核心特性。通过扩展现有目录、将自定义组件与标准集组合来创建新目录应该是轻而易举的。

*组合自定义目录的示例：*
```python
# 伪代码
myCustomCatalog = Catalog(
  id="https://mycompany.com/catalogs/custom_catalog.json",
  functions=basicCatalog.functions,
  components=basicCatalog.components + [MyCompanyLogoComponent()],
  theme=basicCatalog.theme # 继承主题模式
)
```

### 第 1 层：组件模式（API 定义）
此层定义组件的确切 JSON 足迹，不包含任何渲染逻辑。它作为组件契约的唯一真实来源。

在没有高级模式反射库的静态类型语言中，这可能简单地定义为基本接口或类：

```kotlin
// 简单的静态定义（Kotlin 示例）
interface ComponentApi {
    val name: String
    val schema: Schema // 表示正式的属性定义
}

// 在核心库中，定义标准组件 API
abstract class ButtonApi : ComponentApi {
    override val name = "Button"
    override val schema = ButtonSchema // 表示定义的常量
}
```

#### 动态语言优化（例如 Zod）
在 TypeScript 等动态语言中，我们可以使用 Zod 等工具来表示模式并直接从中推断类型。

```typescript
// basic_catalog_api/schemas.ts
export interface ComponentDefinition<PropsSchema extends z.ZodTypeAny> {
  name: string;
  schema: PropsSchema;
}

const ButtonSchema = z.object({
  label: DynamicStringSchema,
  action: ActionSchema,
});

export const ButtonDef = {
  name: "Button" as const,
  schema: ButtonSchema
} satisfies ComponentDefinition<typeof ButtonSchema>;
```

### 第 2 层：绑定层
A2UI 组件严重依赖 `DynamicValue` 绑定，这些绑定必须被解析为响应式流。

**绑定层**是一个框架无关的层，承担了这一职责。它接收原始组件属性和 `ComponentContext`，并将响应式 A2UI 绑定转换为单一的、内聚的强类型 `ResolvedProps` 流。

#### 订阅生命周期和清理
绑定的一个关键职责是跟踪它对底层数据模型创建的所有订阅。框架适配器（第 3 层）管理绑定的生命周期。当组件从 UI 中移除时，框架适配器必须调用绑定的 `dispose()` 方法。绑定然后遍历其内部跟踪的订阅列表并断开它们，确保没有悬空的监听器附加到全局 `DataModel`。

#### 通用接口概念

```typescript
// 表示活动连接的通用绑定接口
export interface ComponentBinding<ResolvedProps> {
  // 完全解析的、可渲染 props 的有状态流。
  // 它必须持有当前值，以便框架可以同步读取初始状态。
  readonly propsStream: StatefulStream<ResolvedProps>; // 例如 BehaviorSubject, StateFlow
  
  // 清理所有底层数据模型订阅
  dispose(): void;
}

// 结合模式 + 绑定逻辑的绑定器定义
export interface ComponentBinder<ResolvedProps> {
  readonly name: string;
  readonly schema: Schema; // 用于验证和能力的正式模式
  bind(context: ComponentContext): ComponentBinding<ResolvedProps>;
}
```

#### 动态语言优化：通用绑定器
对于动态语言，您可以编写一个通用工厂，自动检查模式并创建所有必要的订阅，避免为每个组件手动编写绑定逻辑。

```typescript
// 说明性通用绑定器工厂
export function createGenericBinding<T>(schema: Schema, context: ComponentContext): ComponentBinding<T> {
  // 1. 遍历模式以查找所有 DynamicValue 属性。
  // 2. 将它们映射到 `context.dataContext.subscribeDynamicValue()`
  // 3. 存储返回的 `DataSubscription` 对象。
  // 4. 将所有可观察对象组合为单个有状态流。
  // 5. 返回一个 ComponentBinding，其 `dispose()` 方法取消所有存储的订阅。
}
```

#### 替代方案：无绑定器实现（直接绑定）
对于不太动态的框架、缺乏代码生成系统的框架，或者只想快速实现单个一次性组件的开发者，跳过正式的绑定层并直接在框架适配器内实现组件是完全有效的。

*Dart/Flutter 说明性示例：*
```dart
// 渲染函数处理从上下文读取并手动构建组件。
Widget renderButton(ComponentContext context, Widget Function(String) buildChild) {
  // 手动观察动态值并管理流
  return StreamBuilder(
    stream: context.dataContext.observeDynamicValue(context.componentModel.properties['label']),
    builder: (context, snapshot) {
      return ElevatedButton(
        onPressed: () {
          context.dispatchAction(context.componentModel.properties['action']);
        },
        child: Text(snapshot.data?.toString() ?? ''),
      );
    }
  );
}
```

---

## 3. 框架绑定层（框架特定）

框架开发者在编写实际 UI 视图时，不应直接与原始 `ComponentContext` 或 `ComponentBinding` 交互。相反，架构提供了框架特定的适配器，将 `Binding` 的流桥接到框架的原生响应性。

### 所有权契约
A2UI 架构的一个关键部分是理解谁"拥有"数据层。
*   **数据层（消息处理器）拥有 `ComponentModel`**。它根据传入的 JSON 流创建、更新和销毁组件的原始数据状态。
*   **框架适配器拥有 `ComponentContext` 和 `ComponentBinding`**。当原生框架决定将组件挂载到屏幕上（例如 React 运行 `render`）时，框架适配器创建 `ComponentContext` 并将其传递给绑定器。当原生框架卸载组件时，框架适配器必须调用 `binding.dispose()`。

### 数据 Props 与结构 Props
区分数据 Props（如 `label` 或 `value`）和结构 Props（如 `child` 或 `children`）很重要。
*   **数据 Props**：完全由绑定器处理。适配器接收完全解析值的流（例如 `"Submit"` 而不是 `DynamicString` 路径）。
*   **结构 Props**：绑定器不尝试将组件 ID 解析为实际的 UI 树。相反，它输出需要渲染的子组件的元数据。
    *   对于简单的 `ComponentId`（例如 `Card.child`），它发出类似 `{ id: string, basePath: string }` 的对象。
    *   对于 `ChildList`（例如 `Column.children`），它评估数组并发出 `ChildNode` 流列表。
*   框架适配器然后负责获取这些节点定义并递归调用框架原生的 `buildChild(id, basePath)` 方法。

### 组件订阅生命周期规则
为确保性能和防止内存泄漏，框架适配器必须严格管理其订阅：
1.  **延迟订阅**：仅在组件实际挂载/附加到 UI 时才绑定和订阅数据路径或属性更新。
2.  **路径稳定性**：如果组件的属性通过 `updateComponents` 消息变更，适配器/绑定器必须在订阅新路径之前取消订阅旧路径。
3.  **销毁/清理**：当组件从 UI 中移除（例如通过 `deleteSurface` 消息）时，框架绑定必须挂钩到其原生生命周期以触发 `binding.dispose()`。

### 响应式验证 (`Checkable`)
支持 `checks` 属性的交互式组件应实现 `Checkable` 特征。
*   **聚合错误流**：组件应订阅其属性中定义的所有 `CheckRule` 条件。
*   **UI 反馈**：它应响应式地显示第一个失败检查的 `message`。
*   **操作阻止**：如果任何验证检查失败，操作（如 `Button` 点击）应被响应式地禁用或阻止。

### 快乐路径：开发者体验

一旦绑定层和框架适配器实现完成，添加新的 UI 组件变得极其简单且严格类型安全。开发者无需担心 JSON 指针、手动订阅或响应式流生命周期。他们只需接收完全解析的原生类型。

以下是使用通用 React 适配器和现有 `ButtonBinder` 实现 `Button` 时"快乐路径"的示例：

```typescript
// 1. 框架适配器从绑定器的模式推断 prop 类型。
// 原始的 `DynamicString` label 和 `Action` 对象已自动
// 解析为静态 `string` 和可调用的 `() => void` 函数。

// 概念上，推断的类型如下所示：
interface ButtonResolvedProps {
  label?: string;      // 从 DynamicString 解析
  action: () => void;  // 从 Action 解析
  child?: string;      // 解析的结构 ComponentId
}

// 2. 开发者编写简单的、无状态的 UI 组件。
// `props` 参数严格类型化为匹配 `ButtonResolvedProps`。
const ReactButton = createReactComponent(ButtonBinder, ({ props, buildChild }) => {
  return (
    <button onClick={props.action}>
      {/* 如果按钮有结构子组件 ID，我们使用 buildChild 辅助函数 */}
      {props.child ? buildChild(props.child) : props.label}
    </button>
  );
});
```

由于通过适配器流动的通用类型，如果开发者将 `props.action` 误写为 `props.onClick`，或将 `props.label` 视为对象而非字符串，编译器将立即标记类型错误。

### 示例：框架特定适配器

适配器充当包装器，实例化绑定器，将其输出流绑定到框架的状态机制，注入结构渲染辅助函数（`buildChild`），并挂钩到原生销毁生命周期以调用 `dispose()`。

#### React 伪适配器
```typescript
// React 适配器的伪代码概念
function createReactComponent(binder, RenderComponent) {
  return function ReactWrapper({ context, buildChild }) {
    // 挂钩到组件挂载
    const [props, setProps] = useState(binder.initialProps);
    
    useEffect(() => {
      // 挂载时创建绑定
      const binding = binder.bind(context);
      
      // 订阅更新
      const sub = binding.propsStream.subscribe(newProps => setProps(newProps));
      
      // 卸载时清理
      return () => {
        sub.unsubscribe();
        binding.dispose(); 
      };
    }, [context]);

    return <RenderComponent props={props} buildChild={buildChild} />;
  }
}
```

#### Angular 伪适配器
```typescript
// Angular 适配器的伪代码概念
@Component({
  selector: 'app-angular-wrapper',
  imports: [MatButtonModule],
  template: `
    @if (props(); as props) {
      <button mat-button>{{ props.label }}</button>
    }
  `
})
export class AngularWrapper {
  private binder = inject(BinderService);
  private context = inject(ComponentContext);

  private bindingResource = resource({
    loader: async () => {
      const binding = this.binder.bind(this.context);

      return {
        instance: binding,
        props: toSignal(binding.propsStream) // 将 Observable 转换为 Signal
      };
    },
  });

  props = computed(() => this.bindingResource.value()?.props() ?? null);

  constructor() {
    inject(DestroyRef).onDestroy(() => {
      this.bindingResource.value()?.instance.dispose();
    });
  }
}
```

---

## 4. 基础目录实现

一旦核心架构和适配器构建完成，就可以实现实际的目录了。

### 强类型目录实现
为确保所有组件正确实现并匹配确切的 API 签名，具有强类型系统的平台应利用其高级类型特性。这确保提供的渲染器不仅存在，而且其 `name` 和 `schema` 严格匹配官方目录定义，在编译时而非运行时捕获不匹配。

#### 静态类型语言（例如 Kotlin/Swift）
在 Kotlin 等语言中，您可以定义一个严格的接口或类，要求核心库定义的特定组件 API 的具体实例。

```kotlin
// 核心库定义目录的确切形状
class BasicCatalogImplementations(
    val button: ButtonApi, // 必须是 ButtonApi 类的实例
    val text: TextApi,
    val row: RowApi
    // ...
)

// 框架适配器实现扩展基础 API 的原生视图
class ComposeButton : ButtonApi() {
    // 框架特定的渲染逻辑
}

// 编译器强制要求提供所有必需的组件
val implementations = BasicCatalogImplementations(
    button = ComposeButton(),
    text = ComposeText(),
    row = ComposeRow()
)

val catalog = Catalog("id", listOf(implementations.button, implementations.text, implementations.row))
```

#### 动态语言（例如 TypeScript）
在 TypeScript 中，我们可以使用交叉类型来强制框架渲染器与确切定义交叉。

```typescript
// 概念：强制实现匹配规范
type BasicCatalogImplementations = {
  Button: Renderer & { name: "Button", schema: Schema },
  Text: Renderer & { name: "Text", schema: Schema },
  Row: Renderer & { name: "Row", schema: Schema },
  // ...
};

// 如果开发者忘记 'Row' 或拼写错误，编译器会抛出错误。
const catalog = new Catalog("id", [
  implementations.Button,
  implementations.Text,
  implementations.Row
]);
```

### 基础目录核心函数
标准 A2UI 目录需要标准函数定义（如 `length`、`formatDate` 等）的共享逻辑层。

#### 函数定义
客户端函数的运作方式类似于组件。它们需要定义（模式）和实现。

```typescript
interface FunctionImplementation {
  readonly name: string;
  readonly returnType: 'string' | 'number' | 'boolean' | 'array' | 'object' | 'any' | 'void';
  readonly schema: Schema; // 期望的参数
  
  // 执行函数逻辑。返回值或响应式流。
  execute(args: Record<string, any>, context: DataContext): unknown | Observable<unknown>;
}
```

#### 表达式解析逻辑 (`formatString`)
标准的 `formatString` 函数特别复杂。它负责解释字符串属性中的 `${expression}` 语法。

**实现要求**：
1.  **递归**：函数实现必须使用 `DataContext.resolveDynamicValue()` 或 `DataContext.subscribeDynamicValue()` 来递归评估嵌套表达式或函数调用（例如 `${formatDate(value:${/date})}`）。
2.  **分词**：解析器必须区分 DataPaths（例如 `${/user/name}`）和 FunctionCalls（例如 `${now()}`）。
3.  **转义**：必须处理字面的 `${` 序列（通常通过转义为 `\${`）。
4.  **响应式强制转换**：结果使用类型强制转换标准转换为字符串。

---

## 5. Agent 实现指南

如果您是被指派为 A2UI 构建新渲染器的 AI Agent，您必须遵循以下严格的分阶段操作序列。不要尝试一次性实现整个架构。

### 1. 需要了解的上下文
在编写任何代码之前，请仔细审阅：
*   `specification/v0_9/docs/a2ui_protocol.md`（协议规则）
*   `specification/v0_9/json/common_types.json`（动态绑定类型）
*   `specification/v0_9/json/server_to_client.json`（消息信封）
*   `specification/v0_9/json/catalogs/minimal/minimal_catalog.json`（您的初始目标）

### 2. 关键依赖决策
创建一个计划文档，明确说明：
*   您将使用哪个**模式库**（或者是否将使用原始语言构造如 `structs`/`data classes`）。
*   您将使用哪个**可观察/响应式库**（必须支持多播和明确的取消订阅）。
*   您目标的原生 UI 框架。

### 3. 核心模型层
实现框架无关的数据层（第 1 节）。
*   实现标准监听器模式（`EventSource`/`EventEmitter`）。
*   实现 `DataModel`，确保正确的 JSON 指针解析和级联/冒泡通知策略。
*   实现 `ComponentModel`、`SurfaceComponentsModel`、`SurfaceModel` 和 `SurfaceGroupModel`。
*   实现 `DataContext` 和 `ComponentContext`。
*   实现 `MessageProcessor`。包含检测模式引用以生成 `ClientCapabilities` 的逻辑。
*   定义 `Catalog`、`ComponentApi` 和 `FunctionImplementation` 接口。
*   定义 `ComponentBinding` 接口。

### 4. 框架特定层
实现无关模型和原生 UI 之间的桥接（第 3 节）。
*   定义 `ComponentAdapter` API（核心库如何将组件传递给框架）。
*   实现将 `ComponentBinding` 流绑定到原生 UI 状态的机制（例如包装视图/组件）。
*   实现递归 `Surface` 构建器，它接受 `surfaceId`，找到 "root" 组件，并递归调用 `buildChild`。
*   **关键**：确保卸载/销毁生命周期钩子调用 `binding.dispose()`。

### 5. 最小目录支持
不要从完整的基础目录开始。首先以 `minimal_catalog.json` 为目标。
*   **核心库**：为 `Text`、`Row`、`Column`、`Button` 和 `TextField` 创建定义/绑定器。
*   **核心库**：实现 `capitalize` 函数。
*   **框架库**：为这 5 个组件实现实际的原生 UI 组件。
*   设计一种机制（例如工厂函数或类）将这些组件捆绑到目录中。

### 6. 演示应用（里程碑）
构建一个独立的应用程序，在扩展之前证明架构可行。
*   应用应完全在本地运行（不需要服务器）。
*   它应从 `specification/v0_9/json/catalogs/minimal/examples/` 加载 JSON 消息数组。
*   它应显示这些示例的列表。
*   当选择一个示例时，它应将消息管道传输到 `MessageProcessor` 并渲染 Surface。
*   **响应性测试**：添加一种机制来模拟延迟的 `updateDataModel` 消息（例如等待 2 秒后再发送数据），以证明 UI 可以渐进式渲染并对变更做出响应。

**在此停止。在继续第 7 步之前，请向用户请求对架构和演示应用的批准。**

### 7. 基础目录支持
一旦最小架构被证明是健壮的：
*   **核心库**：实现完整的基础函数套件。关键要注意，字符串插值和表达式解析应仅在 `formatString` 函数内进行。不要尝试为所有字符串添加全局字符串插值。
*   **核心库**：为剩余的基础目录组件创建定义/绑定器。
*   **框架库**：实现所有剩余的 UI 组件。
*   **测试**：查看现有的参考实现（例如 `web_core`）来制定并运行全面的数据强制转换和函数逻辑的单元和集成测试用例。
*   更新演示应用以从 `specification/v0_9/json/catalogs/basic/examples/` 加载示例。