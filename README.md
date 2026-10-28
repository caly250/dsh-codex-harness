# dsh-codex-harness（0.4.0 重构版）

Codex 风格编码 Agent 预设，适配 **DSH 0.2.x**（在本机 0.2.1-rc.2 上验证）。
重构自 `@lejvn/dsh-codex-harness@0.3.0`（其上游为 `@shuind/dsh-codex-harness`）。

## 它做什么

在 Web UI 的模式选择器 / 设置 → Agent 预设 中增加一个 **Codex 模式**：

- **Codex 身份**：`You are Codex, a coding agent based on the {{model}} model...`
- **行为纪律提示词**（persona suffix）：编辑约束（ASCII 默认、注释纪律、绝不回滚他人 git 改动）、规划纪律、结论先行的汇报风格、第一性原则工作哲学。
- **完整工具集**：与官方 `standard` 预设逐行对齐（终端、文件系统、检索、后台任务、日程、goal、plan mode、压缩、子代理/工作流、ask-user、todo、web、present、skills）。

## 与上游 0.3.0 的差异（重构说明）

1. **纯声明式 bundle**。上游带一个宿主插件，`apply()` 调用的
   `ctx.settings.markVolatile()` 在 DSH 0.2.x 的 settings 服务上不存在
   （0.2.x 只有 configure/describe/update/replace/mutate），且整段被
   `if (ctx.settings?.markVolatile)` 守卫跳过——8 个 Config 字段
   （persona/systemPrompt/terminalToolsEnabled/…）没有任何读取方，全部是死配置；
   上游的 Codex persona 只写进了这份死配置，**真实预设里根本没有注入任何提示词**。
   本版本删除宿主插件与 client.js，把提示词真正写进 `dsh-persona`。
2. **不钉死宿主包版本**。上游把 20 个 `@deepseek-ai/dsh-*` peer 依赖钉在
   `0.2.0-rc.2`，在 0.2.1 上被 DSH 版本校验直接拒装。本包零运行时依赖，
   预设内引用的官方插件名由宿主 composition 解析，随 DSH 升级自然漂移。
3. **删除硬编码默认模型**。上游钉死 `provider: deepseek-official /
   model: deepseek-reasoner`；该 provider 不存在时预设激活失败。模型选择交还 DSH。
4. **工具集补齐**。上游缺 delegation（subagent/workflow）、schedule、time-context、
   plugin-manager 禁用行；本版本与官方 standard 完整对齐。

## 安装

```
dsh plugin add <本目录路径>
```

或在插件管理器里以本地目录安装。装完重启/热重载 profile，在模式选择器选
「Codex 模式」新建会话即可。

## 自定义提示词

预设行 `preset-codex` 的 `config.plugins` 可被 profile 层
`cordis.patch.yml` 按 id 覆盖（与官方预设相同的机制），或直接在
设置 → Agent 预设 的编辑器里改。

## 许可与来源

MIT。Codex persona 与提示词文本源自 `@shuind/dsh-codex-harness`（shuind），
经 `@lejvn/dsh-codex-harness`（lejvn）移植；本仓库为面向 DSH 0.2.x 的二次重构。
见 NOTICE。
