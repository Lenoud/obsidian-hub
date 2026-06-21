# qcow2 转 raw(OpenStack 赛事参考)

OpenStack 私有云赛项中,把 cirros 的 qcow2 镜像转换为 raw 格式的步骤,作为考点速记。

> 注:赛事参考内容,组件版本可能已过时,实际操作时按当前 OpenStack 版本调整。

## 转换命令

```bash
# 用 qemu-img 转换
qemu-img convert -f qcow2 -O raw /root/cirros-0.3.4-x86_64-disk.img /root/cirros.raw

# 查看结果
qemu-img info /root/cirros.raw
# image: cirros.raw
# file format: raw
# virtual size: 39M (41126400 bytes)
# disk size: 18M
```

## 转换场景

| 场景 | 是否需要转 raw |
| --- | --- |
| 上传到 Glance | 通常不需要,Glance 原生支持 qcow2 |
| Ceph RBD 后端 | 推荐 raw(RBD 性能更好) |
| 导入到 ESXi | 需要 vmdk,可经 raw 中转 |
| 创建物理机镜像(DD) | 推荐 raw |

## 批量转换脚本

```bash
for img in *.qcow2; do
    raw="${img%.qcow2}.raw"
    qemu-img convert -f qcow2 -O raw "$img" "$raw"
    echo "转换完成: $raw"
done
```

## 相关笔记
- [[qcow2虚拟机的创建]] — KVM 环境的 qcow2 操作
- [[KVM虚拟化]] — qcow2/raw/vmdk 对比
- [[MOC-Linux运维]]
