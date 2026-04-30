# MySQL 备份脚本

自动化 MySQL 数据库备份、打包、校验并同步到远程服务器，支持本地和远程备份的自动轮转清理。

提供 Python 和 Bash 两个版本。

## Python 版本

```python
#!/usr/bin/env python3
import argparse
import subprocess
import os
import datetime
import logging
from pathlib import Path

def main():
    # 解析命令行参数
    parser = argparse.ArgumentParser(description='MySQL Backup Sync Script with Rotation')
    # 目录配置
    parser.add_argument('--src-dir', default='/root/mysql_backup', help='源目录路径')
    parser.add_argument('--backup-dir', default='/root/mysql_backup_archives', help='本地备份目录')
    parser.add_argument('--remote-dir', default='/root/mysql_backup_remote', help='远程备份目录')

    # 远程服务器配置
    parser.add_argument('--remote-user', default='root', help='远程服务器用户名')
    parser.add_argument('--remote-host', default='117.72.66.224', help='远程服务器地址或主机名')
    parser.add_argument('--remote-port', type=int, default=22, help='远程服务器SSH端口')

    # 日志配置
    parser.add_argument('--log-file', default='/var/log/mysql_backup_sync.log', help='日志文件路径')

    # 备份保留配置
    parser.add_argument('--local-retention', type=int, default=7, help='本地备份保留天数')
    parser.add_argument('--remote-retention', type=int, default=30, help='远程备份保留天数')

    args = parser.parse_args()

    # 配置日志
    logging.basicConfig(
        filename=args.log_file,
        level=logging.INFO,
        format='[%(asctime)s] %(message)s',
        datefmt='%Y-%m-%d %H:%M:%S'
    )

    # 时间戳
    timestamp = datetime.datetime.now().strftime("%Y%m%d_%H%M%S")
    tar_filename = f"mysql_backup_simplehire_{timestamp}.tar.gz"
    hash_filename = f"{tar_filename}.sha256"

    # 确保备份目录存在
    backup_dir = Path(args.backup_dir)
    backup_dir.mkdir(parents=True, exist_ok=True)

    logging.info("开始备份 ...")
    print(f"备份过程日志将记录到: {args.log_file}")

    try:
        # 1. 打包 - 修复路径处理问题
        tar_path = backup_dir / tar_filename

        # 清理源目录路径，处理结尾的斜杠
        src_dir = args.src_dir.rstrip('/')
        if not os.path.exists(src_dir):
            error_msg = f"源目录不存在: {src_dir}"
            logging.error(error_msg)
            print(error_msg)
            return

        # 正确解析父目录和源目录名
        src_parent = os.path.dirname(src_dir)
        src_dirname = os.path.basename(src_dir)

        # 验证解析结果
        if not src_parent or not src_dirname:
            error_msg = f"路径解析错误 - 父目录: {src_parent}, 目录名: {src_dirname}"
            logging.error(error_msg)
            print(error_msg)
            return

        logging.info(f"打包源目录: {src_dir}")
        logging.info(f"tar命令参数: -czf {tar_path} -C {src_parent} {src_dirname}")

        tar_cmd = ['tar', '-czf', str(tar_path), '-C', src_parent, src_dirname]
        result = subprocess.run(tar_cmd, capture_output=True, text=True)

        if result.returncode != 0:
            logging.error(f"打包失败! 错误: {result.stderr}")
            print("打包失败，请查看日志获取详细信息")
            return

        # 2. 生成 hash
        hash_path = backup_dir / hash_filename
        sha_cmd = ['sha256sum', str(tar_path)]
        with open(hash_path, 'w') as f:
            subprocess.run(sha_cmd, stdout=f, check=True)

        # 3. 本地清理超过指定天数的备份
        find_cmd = [
            'find', str(backup_dir),
            '-type', 'f',
            '-mtime', f'+{args.local_retention}',
            '-exec', 'rm', '-f', '{}', ';'
        ]
        subprocess.run(find_cmd, check=True)

        # 4. 同步到远程服务器
        rsync_cmd = [
            'rsync', '-avz',
            f'--rsh=ssh -p {args.remote_port}',
            f'{str(backup_dir)}/',
            f'{args.remote_user}@{args.remote_host}:{args.remote_dir}/'
        ]
        result = subprocess.run(rsync_cmd, capture_output=True, text=True)

        if result.returncode != 0:
            logging.error(f"同步失败! 错误: {result.stderr}")
            print("同步失败，请查看日志获取详细信息")
            return

        logging.info(f"同步成功: {tar_filename}")

        # 5. 远程清理超过指定天数的备份
        ssh_cmd = [
            'ssh', '-p', str(args.remote_port),
            f'{args.remote_user}@{args.remote_host}',
            f'find {args.remote_dir} -type f -mtime +{args.remote_retention} -exec rm -f {{}} \;'
        ]
        subprocess.run(ssh_cmd, check=True)

        logging.info("备份任务完成。")
        logging.info("\n\n")
        print("备份任务已成功完成")

    except subprocess.CalledProcessError as e:
        logging.error(f"命令执行失败: {e}")
        print(f"操作失败: {e}，请查看日志获取详细信息")
    except Exception as e:
        logging.error(f"发生意外错误: {e}")
        print(f"发生意外错误: {e}，请查看日志获取详细信息")

if __name__ == "__main__":
    main()
```

## Bash 版本

```bash
#!/bin/bash
# =========================================
# MySQL Backup Sync Script with Rotation
# 功能:
#  1. 打包 mysql_backup/ 目录
#  2. 生成 hash 文件
#  3. 本地保存 7 天
#  4. 同步到远程服务器并在远程保存 30 天
# =========================================

# 配置
SRC_DIR="/root/mysql_backup"
BACKUP_DIR="/root/mysql_backup_archives"
REMOTE_USER="root"
REMOTE_HOST="117.72.66.224"
REMOTE_DIR="/root/mysql_backup_remote"
LOG_FILE="/var/log/mysql_backup_sync.log"

# 时间戳
DATE=$(date +"%Y%m%d_%H%M%S")
TAR_FILE="mysql_backup_simplehire_${DATE}.tar.gz"
HASH_FILE="${TAR_FILE}.sha256"

# 确保备份目录存在
mkdir -p $BACKUP_DIR

echo "[$(date +"%F %T")] 开始备份 ..." >> $LOG_FILE

# 1. 打包
tar -czf ${BACKUP_DIR}/${TAR_FILE} -C /root mysql_backup
if [ $? -ne 0 ]; then
    echo "[$(date +"%F %T")] 打包失败!" >> $LOG_FILE
    exit 1
fi

# 2. 生成 hash
sha256sum ${BACKUP_DIR}/${TAR_FILE} > ${BACKUP_DIR}/${HASH_FILE}

# 3. 本地清理超过 7 天的备份
find $BACKUP_DIR -type f -mtime +7 -exec rm -f {} \;

# 4. 同步到远程服务器
rsync -avz ${BACKUP_DIR}/ ${REMOTE_USER}@${REMOTE_HOST}:${REMOTE_DIR}/ >> $LOG_FILE 2>&1

if [ $? -eq 0 ]; then
    echo "[$(date +"%F %T")] 同步成功: ${TAR_FILE}" >> $LOG_FILE
else
    echo "[$(date +"%F %T")] 同步失败!" >> $LOG_FILE
    exit 1
fi

# 5. 远程清理超过 30 天的备份
ssh ${REMOTE_USER}@${REMOTE_HOST} "find ${REMOTE_DIR} -type f -mtime +30 -exec rm -f {} \;"

echo "[$(date +"%F %T")] 备份任务完成。" >> $LOG_FILE
echo -e "\n\n" >> $LOG_FILE
echo $LOG_FILE
```

## 说明

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `--src-dir` | `/root/mysql_backup` | MySQL 备份源目录 |
| `--backup-dir` | `/root/mysql_backup_archives` | 本地打包备份目录 |
| `--remote-dir` | `/root/mysql_backup_remote` | 远程备份目录 |
| `--remote-host` | `117.72.66.224` | 远程服务器地址 |
| `--local-retention` | `7` | 本地备份保留天数 |
| `--remote-retention` | `30` | 远程备份保留天数 |
| `--log-file` | `/var/log/mysql_backup_sync.log` | 日志文件路径 |
