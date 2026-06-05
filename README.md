# myskills

个人的 Claude Code 自定义技能合集。

## 技能分类

### 📋 recording（记录追踪类）

| 技能 | 描述 |
|------|------|
| [workflow-recorder](recording/workflow-recorder/) | 工作流程记录器 —— 记录工作流程、对话决策和问题解决方案，生成结构化报告 |

### 🔧 maintenance（维护类）

| 技能 | 描述 |
|------|------|
| *(github项目维护 — 待添加)* | |

## 安装

将需要的技能目录复制到 `~/.claude/skills/` 下：

```powershell
# 安装单个技能
Copy-Item -Path "recording/workflow-recorder" -Destination "$env:USERPROFILE\.claude\skills\workflow-recorder" -Recurse -Force
```

安装后在 Claude Code 中输入 `/workflow-recorder` 即可使用。

## 目录结构

```
myskills/
├── README.md
├── recording/                    # 记录追踪类
│   └── workflow-recorder/        # 工作流程记录器
│       ├── SKILL.md
│       └── templates/
│           └── report-template.md
└── maintenance/                  # 维护类
    └── (更多技能...)
```
