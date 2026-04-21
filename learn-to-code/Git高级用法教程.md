# 🔧 Git 高级用法教程

> 分支管理、合并冲突、Git 工作流完整指南

## 📂 目录

1. [分支管理](#分支管理)
2. [合并与冲突](#合并与冲突)
3. [Git 工作流](#git-工作流)
4. [远程操作](#远程操作)
5. [实用技巧](#实用技巧)

---

## 🌿 分支管理

### 1. 创建和切换分支

```bash
# 创建新分支
git branch feature-login

# 切换到新分支
git checkout feature-login

# 创建并切换（简写）
git checkout -b feature-login
```

### 2. 列出分支

```bash
# 列出本地分支
git branch

# 列出远程分支
git branch -r

# 列出所有分支
git branch -a
```

### 3. 重命名分支

```bash
# 重命名当前分支
git branch -m old-name new-name

# 重命名其他分支
git branch -m old-name new-name
```

### 4. 删除分支

```bash
# 删除已合并的分支
git branch -d feature-login

# 强制删除分支
git branch -D feature-login

# 删除远程分支
git push origin --delete feature-login
```

---

## 🔀 合并与冲突

### 1. 合并分支

```bash
# 确保在主分支上
git checkout main

# 合并功能分支
git merge feature-login
```

### 2. 快进合并 (Fast Forward)

当没有冲突时，Git 直接移动指针。

```
Before:          After:
main ──── A      main ──── A
               /
feature ─── B
```

### 3. 三方合并 (Recursive)

当有冲突时，Git 创建新的合并提交。

```
Before:          After:
main ──── A      main ────┬── M (合并提交)
               /         /
feature ─── B ───────────
```

### 4. 解决冲突

冲突时会显示类似：
```
<<<<<<< HEAD
当前分支的代码
=======
被合并分支的代码
>>>>>>> feature-login
```

**解决步骤：**
1. 打开冲突文件
2. 编辑保留需要的代码
3. 删除冲突标记 `<<<<<<<`, `=======`, `>>>>>>>`
4. `git add <file>`
5. `git commit`

### 5. 放弃合并

```bash
# 放弃合并，回到合并前的状态
git merge --abort
```

---

## 🔄 Git 工作流

### 1. Git Flow

```
     develop
    /        \
feature    release
    \        /
     ─── main ────
```

| 分支 | 用途 | 生命周期 |
|------|------|----------|
| main | 生产代码 | 长期 |
| develop | 开发代码 | 长期 |
| feature/* | 新功能 | 短期 |
| release/* | 发布版本 | 短期 |
| hotfix/* | 紧急修复 | 短期 |

### 2. GitHub Flow

更适合小型团队：
1. 从 main 创建分支
2. 开发并提交
3. 发起 Pull Request
4. 审查并合并
5. 部署

### 3. 创建 Pull Request

```bash
# 1. 推送分支到远程
git push -u origin feature-login

# 2. 在 GitHub 上创建 PR（通过网页）
# 或使用 GitHub CLI
gh pr create --title "添加登录功能" --body "实现了用户登录功能"
```

---

## 🌐 远程操作

### 1. 查看远程仓库

```bash
# 查看远程仓库信息
git remote -v

# 输出:
# origin  https://github.com/user/repo.git (fetch)
# origin  https://github.com/user/repo.git (push)
```

### 2. 添加远程仓库

```bash
git remote add upstream https://github.com/original/repo.git
```

### 3. 同步远程分支

```bash
# 拉取远程分支并创建本地分支
git fetch origin
git checkout -b feature origin/feature

# 拉取远程更新（不合并）
git fetch

# 拉取并合并
git pull origin main
```

### 4. 推送分支

```bash
# 推送并设置上游分支
git push -u origin feature-login

# 推送所有分支
git push --all

# 推送标签
git push --tags
```

---

## 💡 实用技巧

### 1. 查看历史

```bash
# 简洁历史
git log --oneline

# 图形化历史
git log --graph --oneline --all

# 查看文件历史
git log -p filename
```

### 2. 撤销操作

```bash
# 撤销工作区修改
git checkout -- filename

# 撤销暂存区
git reset HEAD filename

# 回退到指定提交
git reset --hard abc1234

# 创建反向提交（撤销指定提交）
git revert abc1234
```

### 3. 储藏工作

```bash
# 储藏当前修改
git stash

# 储藏并添加说明
git stash save "临时修改"

# 查看储藏列表
git stash list

# 恢复最新储藏
git stash pop

# 恢复指定储藏
git stash apply stash@{0}

# 删除储藏
git stash drop stash@{0}
```

### 4. 清理

```bash
# 删除已合并的分支
git branch --merged main | grep -v "main" | xargs git branch -d

# 清理远程已删除的分支
git fetch --prune

# 清理未跟踪文件
git clean -fd
```

### 5. 别名

```bash
# 设置别名
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.lg "log --graph --oneline --all"

# 使用别名
git lg
```

---

## 🎯 最佳实践

1. **频繁提交** - 小而频繁的提交更容易追踪
2. **清晰的提交信息** - 使用 `type: 描述` 格式
3. **分支命名** - `feature/xxx`, `bugfix/xxx`, `hotfix/xxx`
4. **PR 审查** - 合并前必须有人审查
5. **保护 main 分支** - 不能直接 push 到 main

---

## 🔄 更新日志

- **2026-04-21**: 创建 Git 高级用法教程
- 分支管理
- 合并冲突解决
- Git 工作流
- 远程操作
- 实用技巧
