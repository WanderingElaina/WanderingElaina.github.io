
## 目标

* 删除远程仓库个人分支中的`.idea`文件夹
* 本地也删除`.idea`文件夹（如果你需要）
* 让Git忽略`.idea`文件夹，避免以后误提交

---

## 假设

* 已经在个人分支，比如 `feature/xxx` 上
* 已经提交了`.idea`文件夹到远程

---

# 具体步骤

### 1. **添加 `.idea` 到 `.gitignore` 文件**

1）打开项目根目录下的 `.gitignore` 文件（没有就新建一个）

2）添加一行：

```
.idea/
```

3）保存文件

---

### 2. **从Git索引中删除 `.idea` 文件夹，但不删除本地文件**

因为 `.idea` 已经被提交到了仓库，需要从Git的跟踪中移除它。

执行命令：

```bash
git rm -r --cached .idea
```

* `--cached` 表示只从Git索引中删除，不会删除你本地的`.idea`文件夹

---

### 3. **提交这次修改**

提交 `.gitignore` 和移除 `.idea` 索引的操作：

```bash
git add .gitignore
git commit -m "Remove .idea folder from tracking and add to gitignore"
```

---

### 4. **推送到远程个人分支**

```bash
git push origin feature/xxx
```

* 这里`feature/xxx`替换为你的个人分支名

---

### 5. **确认远程仓库 `.idea` 文件夹已经被删除**

去你的代码托管平台（GitHub/GitLab等）上看下个人分支，`.idea`文件夹应该已经不见了。

---

### 6. **验证后续不会提交 `.idea`**

以后你修改IDEA配置文件或者`.idea`里面的内容，Git都不会显示为未跟踪文件，避免再次提交。

---

# 总结

命令汇总：

```bash
echo ".idea/" >> .gitignore
git rm -r --cached .idea
git add .gitignore
git commit -m "Remove .idea folder from tracking and add to gitignore"
git push origin feature/xxx
```

---

