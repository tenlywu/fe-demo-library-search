# 知页 · 文库搜索体验

## 已确定的搭建选择

- 目标：定义查找文档、筛选、预览、收藏的交互体验。
- 类型：React + TypeScript + Vite；桌面优先，兼容手机。
- 运行：仓库根目录 `pnpm dev:search`，默认端口 5173。
- 数据：6 份只读 JSON 演示文档；收藏和最近浏览统一存入 localStorage，不回写文件；无运行时后端或搜索引擎。
- 视觉：温和的纸张与知识空间，浅绿色侧栏，彩色文档封面。

## 已实现

关键词提交搜索（标题、简介、主题；忽略大小写）、热门词与建议、主题与格式交集筛选、相关度/日期/阅读量排序、卡片/列表视图、收藏、最近浏览、文档弹窗、Escape 关闭、弹窗焦点约束、Cmd/Ctrl+K 聚焦。

收藏与最近浏览只在当前浏览器 localStorage 中保存；键为 `fe-demo:library-search:v1:saved`、`fe-demo:library-search:v1:recent`，目前均是 ID 数组，兼容读取旧 `zhiye:*` 键。JSON 文件不被修改。读取损坏数据时回退为空，写入失败时演示面板显示临时保存提示。相关度优先目前保持固定数据顺序，不实现真实相关性评分。搜索只在提交或选择建议后执行。收藏按钮不触发预览，预览加入最近浏览。

## 场景

- `/` 或 `/?scenario=default`：默认
- `/?scenario=loading`：保持加载骨架，用于评审
- `/?scenario=empty`：空结果
- `/?scenario=error`：网络异常，重试回到默认

右下演示工具可切换场景与重置全部（包括收藏、最近浏览、查询、筛选与视图）。当前只有演示场景写入 URL，搜索和筛选条件尚未写入 URL。

## 模拟与边界

文档预览为排版示意，不是实际 PDF。无下载、登录、分页、上传、真实 API 或权限检查。异常和加载来自场景参数，不产生真实网络请求。字体在线加载，离线退回系统字体。

## 后续迭代入口

主要代码 `src/main.tsx`，初始数据 `src/data/documents.json`，视觉 `src/style.css`。可以继续细化搜索建议、结果相关性、筛选规则、URL 状态、更多样本和真实文件预览。修改前确认本 case 范围，避免修改官网应用。

验收：输入“AI”提交、筛选 PDF、收藏刷新、打开关闭预览、切换列表、空态恢复、异常重试、手机布局。

## 独立仓库与 GitHub Pages

本 case 同时作为独立 GitHub 仓库同步。单独克隆后使用 `pnpm install --ignore-scripts`、`pnpm dev`、`pnpm build`。本地仍可从 FE-Demo 根目录通过 workspace 命令运行。

GitHub Actions 在推送 main 后自动构建并发布 dist。PAGES_BASE_PATH 环境变量设置发布子路径，默认 `/`（本机或 Vercel）；Pages 自动设为仓库名。依赖通过当前 case 的 pnpm-lock.yaml 固定，CI 跳过不需要的安装脚本。
