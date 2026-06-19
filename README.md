# myskills

个人的 Claude Code 自定义技能合集。

## 技能分类

### ✍️ content（内容创作类）

| 技能 | 描述 |
|------|------|
| [game-review-writer](content/game-review-writer/) | 游戏评测写作 —— 结合个人体验与网络搜索，按深度分析风格撰写游戏评测（MDX 格式），支持新建评测和已有评测的版本更新 |

### 📋 recording（记录追踪类）

| 技能 | 描述 |
|------|------|
| [workflow-recorder](recording/workflow-recorder/) | 工作流程记录器 —— 记录工作流程、对话决策和问题解决方案，生成结构化报告 |

### 🔧 maintenance（维护类）

| 技能 | 描述 |
|------|------|
| [github-project-maintenance](maintenance/github-project-maintenance/) | GitHub 项目维护 —— 规范对自己项目和他人项目的修改流程，包括分支管理、冲突解决和提交规范 |

## 安装

将需要的技能目录复制到 `~/.claude/skills/` 或项目的 `.agents/skills/` 下：

```powershell
# 安装到全局（所有项目可用）
Copy-Item -Path "content/game-review-writer" -Destination "$env:USERPROFILE\.claude\skills\game-review-writer" -Recurse -Force

# 安装到项目（项目级覆盖全局）
Copy-Item -Path "content/game-review-writer" -Destination ".\agents\skills\game-review-writer" -Recurse -Force
```

安装后在 Claude Code 中输入 `/game-review-writer` 即可使用。

## 目录结构

```
myskills/
├── README.md
├── content/                          # 内容创作类
│   └── game-review-writer/           # 游戏评测写作
│       ├── SKILL.md
│       └── REFERENCE.md
├── recording/                        # 记录追踪类
│   └── workflow-recorder/            # 工作流程记录器
│       ├── SKILL.md
│       └── templates/
│           └── report-template.md
└── maintenance/                      # 维护类
    ├── README.md
    └── github-project-maintenance/   # GitHub 项目维护
        └── SKILL.md
```
