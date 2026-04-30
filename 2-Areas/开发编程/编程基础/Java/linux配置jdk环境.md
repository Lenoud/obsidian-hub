# Linux 配置 JDK 环境

在 Linux 系统中配置 Java JDK 环境变量。

## 配置方法

```bash
cat >> /etc/profile << EOF

export JAVA_HOME=/usr/local/jdk/jdk-17.0.10
export PATH=\$JAVA_HOME/bin:\$PATH

EOF
```

```bash
source /etc/profile
java -version
```

## 说明

| 变量 | 路径 | 说明 |
| --- | --- | --- |
| `JAVA_HOME` | `/usr/local/jdk/jdk-17.0.10` | JDK 安装目录 |
| `PATH` | `$JAVA_HOME/bin:$PATH` | 将 JDK bin 目录加入系统 PATH |
