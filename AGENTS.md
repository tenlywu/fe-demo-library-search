# FE Demo 开发约定

Treat Playwright as globally available for Codex work, even when the current project has no `playwright` dependency.

## 项目定位与模板

本仓库用于给工程师定义交互效果。交互为主用 React + TypeScript + Vite；SEO / 内容为主用 Astro + TypeScript，按需局部使用 React。每个 case 放在 `apps/<case>/`，独立运行，先读对应 README。

## 所有 case 的默认数据机制

- 初始数据来自只读 JSON 或 SQLite，优先 JSON。不得回写初始数据文件。
- 所有需要保留的新增、修改、删除与管理状态统一存入当前浏览器的 localStorage。加载时读取初始数据，再应用已保存的覆盖状态；无覆盖状态时使用初始数据。
- 单纯的弹窗开关、悬停等临时 UI 状态可留在内存。是否保存查询、筛选、视图由 case 明确约定。
- localStorage 的键必须按 case 命名，并为可演进的数据结构设置版本。解析失败应回退初始状态，写入失败应告知用户仅临时保存。
- 必须提供重置演示数据的入口，只重置当前 case 的键，不得调用 localStorage.clear() 影响其他 case。删除初始记录时，使用覆盖数据或删除标记，不能修改源文件。
- SQLite 仅作为只读数据源；按需使用浏览器端 WASM 读取，或构建阶段导出 JSON。不得为了 SQLite 引入运行时 Node API、文件回写或有状态服务器。
- 默认交付静态 HTML/CSS/JS，兼容 GitHub Pages 和 Vercel 静态托管。发布到子路径时配置 base 并验证资源、站内链接和刷新行为。静态部署不等于 file:// 双击运行；离线单文件输出需单独确认。
- 不默认加入数据库、登录、服务端 API 或云资源。localStorage 按来源和浏览器隔离，不保证跨设备、跨部署域名同步，也不保存密钥或真实敏感数据。

## Supabase 例外

当 localStorage 无法满足需求（例如跨设备同步、多人共享、真实身份及服务端权限、大容量数据）时，先明确说明缺口和所需的 Supabase 能力，再按该 case 的明确需求接入。不得静默升级为云端数据库。

## 新 case 的澄清

一次性澄清目标及 1–3 条操作路径、模板类型、运行/发布方式、视觉参考与设备、关键状态和数据管理行为。数据问题默认按“只读 JSON/SQLite + localStorage”填写，仅确认需要管理哪些数据及重置规则；不要每次重新让用户选择数据库。能从需求判断的项先填写默认值。

记录已实现与模拟范围。使用适量 Playwright 关键路径验证；低影响文案和样式调整不单独新增测试。

本仓库是独立 case。直接使用 `pnpm dev` / `pnpm build`。同名本地目录仍位于 FE-Demo workspace，修改仅提交当前 case。
