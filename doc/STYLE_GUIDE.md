# 项目代码风格指南

本项目采用 **TypeScript + React + Node.js**，所有贡献代码必须遵循以下约定。  
这些规则既约束团队成员，也帮助 Copilot 更好地生成符合我们预期的代码。

---

## 1. 基础约定

- **语言**：必须使用 TypeScript，禁止新增纯 JS 文件。
- **代码格式**：由 ESLint + Prettier 保证，禁止在提交前绕过 `npm run lint`。
- **空值处理**：
  - 严禁 `!` 非空断言。
  - 必须显式处理 `null | undefined`，推荐用 `??`、类型守卫或 `Result` 模式。

---

## 2. 数据流

- **优先使用流（Stream / Web Streams / AsyncIterator）**，避免一次性加载大文件或大对象到内存。
- 遇到文件处理、网络传输、大数组，默认选用 **管道 (pipeline)** 模式。
- 禁止使用 `fs.readFileSync` 处理大文件，改用 `createReadStream` + transform。

---

## 3. 错误处理

- 严禁裸 `throw` 或只 `console.error`。
- 推荐用 `Result<T, E>` 模式（如 [neverthrow](https://github.com/supermacro/neverthrow)）。
- API 层必须返回明确的错误结构 `{ code, message }`。

---

## 4. 设计模式

- **工厂模式**：所有可插拔模块必须提供 `createXxx(options)` 工厂函数。
- **策略模式**：业务分支（如支付方式、存储后端）必须用策略模式，不允许写一堆 `if/else`。
- **依赖倒置**：对外依赖通过接口暴露，内部实现可切换。

示例：

```ts
export interface Payment {
  pay(amount: number): Promise<void>
}

export function createPayment(provider: "stripe" | "paypal"): Payment {
  switch (provider) {
    case "stripe":
      return new StripePayment()
    case "paypal":
      return new PaypalPayment()
    default: {
      const _exhaustive: never = provider
      return _exhaustive
    }
  }
}
```
