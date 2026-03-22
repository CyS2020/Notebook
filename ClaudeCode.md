### Claude Code 快速入门

#### 常用快捷键
- `!`: 终端模式，进入shell
- `ESC * 2`: 选择回滚点
- `Shit + Tab`: 切换模式
  - `? for shortcuts`: 默认模式-修改文件前一定询问用户
  - `accept edits on`: 自动模式-自动修改文件、
  - `plan mode on`: 规划模式-只讨论、不修改文件
- `Shit + Enter`: 终端内换行
- `Ctrl + G`: 打开编辑器
- `Ctrl + C * 2`: 退出 Claude Code


#### 常用内置命令
- `/tasks`: 查看当前后台任务
- `/rewind`: 回滚最近的上下文
- `/resume`: 恢复之前中断的对话
- `/mcp`: 查看 MCP 服务
- `/compact`: 压缩上下文
- `/init`: 初始化 CLAUDE.md 文件
- `/memory`: 查看 CLAUDE.md 文件
- `/hooks`: 特定事件下自动执行逻辑
- `/skills`: 查看 skills
- `/plugins`: 查看插件
  - 插件式一系列 Skill SubAgent Hooks MCP 等能力打包


#### 常用技巧
- `CLAUDE.md`: 每次都需要读取的内容
- `Agent Skill` vs ` SubAgent`
  - `Agent Skill`: 与上下文关联大，对上下文影响小
  - `SubAgent`: 与上下文关联小，对上下文影响大