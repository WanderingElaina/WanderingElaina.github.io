---

## 🧭 标准 Git 协作流程（以你个人分支为例）

### **① 保存自己当前修改**

```bash
git add .
git commit -m "保存当前修改"
git push origin dev_zhou
```

✅ **目的：**

* 防止后续操作（比如合并主分支）时覆盖未提交的代码；
* 确保远程分支上有完整备份。

---

### **② 更新主分支到最新**

```bash
git checkout main
git pull origin main
```

✅ **目的：**

* 拉取远程主分支的最新代码；
* 保证你的本地主分支与远程主分支一致。

---

### **③ 回到你的个人开发分支**

```bash
git checkout dev_zhou
```

---

### **④ 合并主分支更新到自己的分支**

```bash
git merge main
```

✅ **目的：**

* 把别人最新提交（主分支的新内容）同步进你的分支；
* 避免后续提交 PR 时发生大范围冲突。

💡 如果有冲突（如 UsersServiceImpl），解决完后：

```bash
git add .
git commit -m "Merge main into dev_zhou, resolve conflicts"
```

---

### **⑤ 推送更新后的个人分支**

```bash
git push origin dev_zhou
```

✅ **目的：**

* 把你合并完后的个人分支上传远程；
* 保证远程 `dev_zhou` 是干净且可合并的状态。

---

### **⑥ 在 GitHub 上发起 PR（Pull Request）**

* 选择：

  * **base:** main
  * **compare:** dev_zhou
* 提交合并申请；
* 审核通过后 **Merge**。

✅ **目的：**

* 把你的修改正式合入主分支；
* 代码被团队统一纳入生产分支。

---

### **⑦ （可选）部署到服务器**

* 一般部署脚本会从主分支拉取最新代码；

```bash
git pull origin main
# 然后执行部署命令（如 mvn package / docker build / npm build）
```

---

## ✅ 流程图总结

```
(你的分支)
   |
   | git push origin dev_zhou        ← 保存工作
   ↓
(切到 main)
   |
   | git pull origin main            ← 更新主分支
   ↓
(切回 dev_zhou)
   |
   | git merge main                  ← 合并更新
   | git push origin dev_zhou        ← 推送合并后的代码
   ↓
(GitHub PR → Merge → main)
   |
   ↓
(服务器从 main 拉取最新代码)
```

---

## ⚠️ 提示

* 每次开始新开发前，**先同步主分支到你个人分支**；
* 每次准备提 PR 前，**再次同步主分支**，避免冲突；
* 任何时候都不要在主分支直接开发。

---

是否希望我帮你把这个流程整理成一份 **“团队协作 Git 使用规范文档（Markdown 版）”**？
你可以直接放到你项目的 README 或 Wiki 中。
