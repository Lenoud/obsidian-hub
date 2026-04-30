# 仪表盘无法操作lv逻辑卷时修改对应的配置文件

Openfiler 仪表盘无法操作 LV 逻辑卷时的配置文件修改方法。

### 配置文件地址：
/opt/openfiler/etc/volumes.xml

例如lv02在仪表盘中错误的显示，可能是这个文件中不存在volume id="lv02"这一段，根据之前的配置写入即可

<?xml version="1.0" ?>

<volumes>

        <volume id="lv01" name="nfs" mountpoint="/mnt/vg01/lv01/" vg="vg01" fstype="xfs" />

        <volume id="lv02" name="share_dir" mountpoint="/mnt/vg02/lv02/" vg="vg02" fstype="xfs" />

</volumes>
