---
name: github-project-maintenance
description: GitHub 项目维护 —— 规范对自己项目和他人项目的修改流程，包括分支管理、冲突解决和提交规范。
origin: custom
---

# GitHub 项目维护

在 Claude Code 会话中对 GitHub 项目进行规范化维护。区分两类场景：**对自己项目的修改**和**对他人项目的修改**，各自遵循不同的分支策略和提交流程。

## 何时使用

以下情况应激活本技能：

- 用户说 "维护项目"、"GitHub 维护"、"/github-project-maintenance"
- 需要在 GitHub 仓库中提交代码修改
- 需要给开源项目提交 PR（Pull Request）
- 需要处理与其他贡献者的代码冲突
- 需要规范化的 commit 信息生成

## 分类决策树

```
需要修改 GitHub 项目
├── 这是我自己的项目？
│   └── 是 → 流程 A：对自己项目的修改
└── 这是别人的项目？
    └── 是 → 流程 B：对他人项目的修改
```

---

## 流程 A：对自己项目的修改

对自己项目拥有完整权限，直接在 main 主体分支上进行修改，最后按规范提交。

### A.1 前置检查

在开始修改前，完成以下检查：

1. **确认身份**：此仓库是你的个人项目（你有 push 权限且是 owner/collaborator）
2. **同步远端**：`git fetch origin` 确认本地是最新状态
3. **确认工作区干净**：`git status` 确保没有未提交的杂物

### A.2 开发流程

1. **直接在 main 分支开发**（或按项目既有分支策略）
2. **按需修改文件**：遵循项目的编码规范和测试要求
3. **验证修改**：运行测试、构建，确保一切正常
4. **生成 commit 信息**（参见下方 [提交信息规范](#提交信息规范)）

### A.3 提交流程

```bash
# 1. 查看修改内容
git diff

# 2. 暂存所有修改
git add .

# 3. 使用规范格式提交
git commit -m "<type>: <简短描述>

<详细正文（可选）>"

# 4. 推送到远端
git push origin main
```

### A.4 注意事项

- 即使是自己的项目，如果有 CI/CD 配置，提交前确保本地测试通过
- 如果是重大功能变更，考虑在分支上开发后再合并
- commit 信息遵循下方的 [提交信息规范](#提交信息规范)

---

## 流程 B：对他人项目的修改

对他人的项目修改需要遵循规范的 Fork/Clone → 分支 → 修改 → 同步 → 解决冲突 → 提交 PR 流程。

### B.1 克隆与分支创建

```bash
# 1. 克隆项目到本地
git clone <repository-url>
cd <project-directory>

# 2. 获取最新状态
git fetch origin
```

#### B.1.1 判断修改类型

根据修改目的，确定分支类型和命名前缀：

| 修改类型 | 分支前缀 | 示例 | 判断条件 |
|----------|----------|------|----------|
| Bug 修复 | `fix/` | `fix/login-error` | 修复现有功能的不正确行为 |
| 新功能 | `feat/` | `feat/oauth-support` | 添加新的功能或能力 |
| 文档更新 | `docs/` | `docs/api-example` | 仅修改文档，不涉及代码 |
| 重构 | `refactor/` | `refactor/db-layer` | 重构代码，不改变外部行为 |
| 性能优化 | `perf/` | `perf/sql-query` | 优化性能，不改变功能 |
| 测试补充 | `test/` | `test/edge-cases` | 添加或完善测试 |
| 构建/CI | `chore/` | `chore/update-ci` | 构建配置、CI/CD 相关 |

**Claude 应主动判断**：根据用户的修改描述，自动选择合适的分类和分支名。如有歧义，向用户确认。

```bash
# 3. 创建并切换到新分支
git checkout -b <prefix>/<descriptive-name>
```

### B.2 修改与提交

1. **进行代码修改**：遵循原项目的编码风格和约定
2. **验证修改**：运行原项目的测试套件，确保不破坏现有功能
3. **本地提交**：

```bash
git add <modified-files>
git commit -m "<type>: <简短描述>

<详细说明修改的原因和内容>"
```

> commit 信息使用英文还是中文，取决于原项目的惯例。如不确定，默认使用英文。

### B.3 推送前：检查远程 main 更新

**这是关键步骤。** 在推送自己的分支之前，必须检查远程 main 是否有新的提交。

```bash
# 1. 获取远端最新信息
git fetch origin

# 2. 比较本地 main 与远程 main 的差异
git log main..origin/main --oneline
```

#### 情况 1：远程 main 没有新提交

```
(输出为空 — 远程 main 没有新提交)
```

→ 直接推送你的分支：

```bash
git push -u origin <your-branch>
```

#### 情况 2：远程 main 有新提交

```
abc1234 feat: new feature merged
def5678 fix: critical bug fixed
```

→ **需要同步并解决可能的冲突。** 进入 [B.4 同步与冲突解决](#b4-同步与冲突解决)。

### B.4 同步与冲突解决

当远程 main 有新提交时，需要将远程更新合并到本地，并将你的分支提交 rebase 到最新的 main 之上。

#### 步骤概览

```
远程 main:  A → B → C → D (新)
                 ↖
本地 main:  A → B → C (旧)
                 ↖
你的分支:    A → B → E → F (你的修改)

目标状态:
远程 main:  A → B → C → D
                        ↖
你的分支:    A → B → C → D → E' → F' (rebase 后)
```

#### 操作步骤

```bash
# 1. 确保在你的工作分支上
git checkout <your-branch>

# 2. 更新本地 main 到最新
git fetch origin
git checkout main
git merge origin/main
# 或者：git pull origin main

# 3. 切回工作分支，执行 rebase
git checkout <your-branch>
git rebase main

# 4. 观察 rebase 结果
```

##### 4a. Rebase 成功（无冲突）

```
Successfully rebased and updated refs/heads/<your-branch>.
```

→ 直接推送：

```bash
# 因为是 rebase 后，提交历史已变更，需要强制推送
git push -u origin <your-branch> --force-with-lease
```

> ⚠️ 使用 `--force-with-lease` 而非 `--force`，更安全：它会检查远端分支是否在 rebase 期间被其他人修改过。

##### 4b. Rebase 遇到冲突

```
CONFLICT (content): Merge conflict in path/to/file.ext
error: could not apply abc1234... your commit message
```

→ **进入冲突解决流程**：

```bash
# 1. 查看冲突文件列表
git status
# 输出示例：
# both modified:   src/auth/login.ts
# both modified:   src/config/settings.ts

# 2. 逐文件解决冲突
# 冲突文件中会看到类似标记：
# <<<<<<< HEAD         (来自 main 的内容)
#   const port = 3000;
# =======
#   const port = 8080;
# >>>>>>> abc1234       (来自你的提交)
```

**冲突解决策略（Claude 遵循以下原则）**：

| 场景 | 策略 |
|------|------|
| 你的修改是对同一函数的改进 | 保留你的版本（更完善的那个） |
| main 的新代码修复了 bug | 保留 main 的修复，将你的修改适配到新代码上 |
| 双方添加了无关的新功能到不同位置 | 保留双方修改 |
| 不确定如何取舍 | **停下来询问用户**，展示双方代码差异 |
| main 删除了你修改的文件 | 确认文件是否还需要；如需要则恢复，否则接受删除 |

```bash
# 3. 解决完所有冲突后，标记为已解决
git add <resolved-files>

# 4. 继续 rebase
git rebase --continue

# 5. 如果 rebase 过程中又遇到冲突，重复步骤 2-4
# 6. 如果想放弃 rebase（回到 rebase 前状态）
git rebase --abort
```

### B.5 推送与创建 PR

冲突解决完成且 rebase 成功后：

```bash
# 推送分支
git push -u origin <your-branch> --force-with-lease
```

**推送后**，Claude 将生成以下信息供你创建 PR：

1. **PR 标题**：遵循原项目的 PR 模板（如有），否则使用 `<type>: <description>` 格式
2. **PR 描述**：包含以下内容：
   - **What**: 做了什么修改
   - **Why**: 为什么做这些修改
   - **How**: 实现方式简述
   - **Testing**: 测试情况

示例 PR 描述：

```markdown
## What
修复登录页面在移动端布局错乱的问题。

## Why
在 iOS Safari 上，登录表单的输入框宽度溢出视口，导致横向滚动条出现。
用户报告了此问题（#123）。

## How
- 将登录表单容器的 `width: 480px` 改为 `max-width: 480px; width: 100%`
- 添加 `padding: 0 16px` 作为移动端安全边距

## Testing
- ✅ 手动测试 iPhone SE, 12, 14 Pro Max 模拟器
- ✅ 单元测试全部通过
- ✅ 响应式断点测试覆盖 320px - 1920px
```

---

## 提交信息规范

所有 commit 信息遵循约定式提交（Conventional Commits）格式：

### 格式

```
<type>: <简短描述>

<详细正文（可选）>

<页脚（可选）>
```

### Type 类型

| Type | 说明 | 示例 |
|------|------|------|
| `feat` | 新功能 | `feat: add OAuth2 login support` |
| `fix` | Bug 修复 | `fix: resolve login redirect loop` |
| `docs` | 文档更新 | `docs: update API authentication guide` |
| `style` | 代码格式（不影响逻辑） | `style: format with prettier` |
| `refactor` | 重构（不改变功能） | `refactor: extract auth logic to service` |
| `perf` | 性能优化 | `perf: cache user session queries` |
| `test` | 测试相关 | `test: add edge case tests for parser` |
| `chore` | 构建/工具/依赖 | `chore: update dependencies` |
| `ci` | CI/CD 配置 | `ci: add GitHub Actions workflow` |

### 描述规则

- 使用祈使句（"add" 而非 "added" 或 "adds"）
- 首字母小写
- 不加句号结尾
- 中文项目可使用中文描述

### 示例

```
# 简单提交
fix: fix login page overflow on mobile devices

# 带正文的提交
feat: add GitHub OAuth authentication

Implement OAuth2 flow using GitHub as the identity provider.
Users can now sign in with their GitHub account.

Closes #42

# 破坏性变更（在正文或页脚标注）
feat: redesign user settings API

BREAKING CHANGE: The /api/user/settings endpoint now returns
a nested 'preferences' object instead of flat fields.
```

---

## 异常场景处理

### 场景 1：Rebase 过程中想放弃

```bash
git rebase --abort
```

回到 rebase 前的状态。然后可以考虑使用 `merge` 替代 `rebase`：

```bash
git checkout <your-branch>
git merge main
# 解决一次冲突（而非每个 commit 一次）
git push -u origin <your-branch>
```

### 场景 2：强制推送被拒绝

某些仓库禁止强制推送。此时：

1. 回到 main，更新到最新
2. 从 main 创建新分支
3. 使用 `git cherry-pick` 逐个应用你的修改
4. 推送新分支

### 场景 3：本地 main 落后太多

如果远程 main 相比你 clone 时变化非常大（几十个提交），考虑：

```bash
# 更高效的方式：重置本地 main
git checkout main
git fetch origin
git reset --hard origin/main

# 然后 rebase 你的工作分支
git checkout <your-branch>
git rebase main
```

### 场景 4：不确定修改类型

当你无法判断是 `fix` 还是 `feat` 时：
- 修复现有行为 → `fix`
- 新增行为 → `feat`
- 不确定 → 向用户确认

---

## 快速参考

### 对自己项目

```
fetch → 修改 → diff → add → commit → push main
```

### 对他人项目

```
clone → 判断类型 → 创建分支 → 修改 → commit → fetch → 检查远程main
                                                              ├── 无新提交 → push 分支
                                                              └── 有新提交 → rebase main
                                                                              ├── 无冲突 → force-with-lease push
                                                                              └── 有冲突 → 解决 → continue rebase → force-with-lease push
```

---

## 注意事项

1. **永远不要直接 `--force` push**：始终使用 `--force-with-lease`，它在推送前会验证远端状态，防止覆盖他人的提交。
2. **冲突不确定时暂停**：如果无法判断冲突应如何解决，停止操作并询问用户，展示双方差异。
3. **保留原项目风格**：修改他人项目时，严格遵循原项目的代码风格、测试规范和 commit 惯例。
4. **一个分支一个关注点**：不要在一个分支中混合 bug 修复和新功能开发。
5. **测试先行**：在提交前运行项目的测试套件，确保没有引入回归。
6. **Commit 原子化**：每个 commit 应该是一个逻辑上完整的最小变更单元。

## 相关技能

- `workflow-recorder` — 在维护过程中记录操作流程和关键决策
- `git-workflow` rules — Git 提交规范和工作流约定
