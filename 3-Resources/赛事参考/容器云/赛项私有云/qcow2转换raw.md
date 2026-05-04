```bash
qemu-img convert -f qcow2 -O raw /root/cirros-0.3.4-x86_64-disk.img /root/cirros.raw

qemu-img info cirros.raw
#image: cirros.raw
#file format: raw
#virtual size: 39M (41126400 bytes)
#disk size: 18M

```
