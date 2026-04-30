# 📌 Rsync 常用命令速查表
## 1️⃣ 基本语法
```bash
rsync [选项] 源路径 目标路径
```

---

## 2️⃣ 常用参数说明
+ `-a` : 归档模式（递归、保留权限、时间戳、软链等，最常用）
+ `-v` : 显示详细过程 (verbose)
+ `-z` : 传输时压缩数据 (适合大文件/网络慢的情况)
+ `-P` : 显示进度条并支持断点续传（等价于 `--partial --progress`）
+ `--delete` : 删除目标中多余的文件，保持源与目标一致
+ `--exclude` : 排除文件或目录
+ `--include` : 仅包含特定文件或目录
+ `-e ssh` : 指定使用 SSH 作为远程传输方式

---

## 3️⃣ 本地同步
### 同步目录内容
```bash
rsync -av /data/ /backup/
```

✅ 说明：把 `/data/` 下的文件同步到 `/backup/`，保留属性。

⚠️ 注意目录末尾斜杠的区别：

+ `/data/` → 复制目录 **内容**
+ `/data`  → 复制整个目录

---

## 4️⃣ 远程同步
### 推送到远程
```bash
rsync -av /data/ root@192.168.1.10:/backup/
```

✅ 说明：把本地 `/data/` 推送到远程服务器 `/backup/`。

### 从远程拉取
```bash
rsync -av root@192.168.1.10:/data/ /backup/
```

✅ 说明：把远程服务器 `/data/` 拉取到本地 `/backup/`。

### 指定 SSH 端口（如 2222）
```bash
rsync -av -e "ssh -p 2222" /data/ root@192.168.1.10:/backup/
```

---

## 5️⃣ 常见增强用法
### 带进度条
```bash
rsync -avP /data/ /backup/
```

### 模拟执行（不会真的复制）
```bash
rsync -av --dry-run /data/ /backup/
```

### 只同步更新过的文件
```bash
rsync -av --update /data/ /backup/
```

### 删除目标中多余文件（保持完全一致）
```bash
rsync -av --delete /data/ /backup/
```

---

## 6️⃣ 过滤文件
### 排除 `.log` 文件
```bash
rsync -av --exclude='*.log' /data/ /backup/
```

### 只同步 `.txt` 文件
```bash
rsync -av --include='*.txt' --exclude='*' /data/ /backup/
```

---

## 7️⃣ 定时任务示例（crontab）
每天凌晨 2 点同步目录：

```bash
0 2 * * * rsync -avz /data/ root@192.168.1.10:/backup/ >> /var/log/rsync.log 2>&1
```

---

可保存为 `/root/rsync_cheatsheet.md` 随时查阅。
