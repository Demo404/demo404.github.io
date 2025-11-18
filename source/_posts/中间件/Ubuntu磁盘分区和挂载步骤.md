---
title: Ubuntu磁盘分区和挂载步骤
date: 2025-11-18 10:07:04
tags: [操作系统, Ubuntu]
---

#### 一、预检查（确认 LVM 空闲空间与卷组名）
先看看当前 LVM 信息，确认可以划出足够的容量：
```
sudo pvs        # 查看物理卷（PV）
sudo vgs        # 查看卷组（VG）及可用空间（VG Free）
sudo lvs        # 查看现有逻辑卷（LV）
```

期望看到 ubuntu-vg（或类似名）有大约剩余 ~9.9T 的空闲空间（VG Free）。示例里面要关注的字段：VG 名称（这里例子是 ubuntu-vg）与 VG Free（必须 >= 9T）。

#### 二、创建逻辑卷（9T）
将 9T 从卷组里分配到新的 LV（这里 LV 名称用 minio，可以自定义）：
```
sudo lvcreate -n minio -L 9T ubuntu-vg
```
说明：
 - -n minio 是 LV 名称 /dev/ubuntu-vg/minio
 - -L 9T 分配 9 TB（注意 T 为 Tebibyte 还是 TB，LVM 默认是 TiB）
 - ubuntu-vg 是示例卷组名，若你 vgs 看到不同名请替换为真实的 VG 名称

创建成功后确认：
```
sudo lvs -o+devices
# 或
sudo lvdisplay /dev/ubuntu-vg/minio
```

#### 三、格式化为 XFS（推荐 XFS 用于大文件/对象存储）
```
sudo mkfs.xfs -f /dev/ubuntu-vg/minio
```
说明：
- -f 强制格式化（如果是新创建 LV 通常不必要，但保险写上）
- XFS 对大容量/并发写入表现好，MinIO 推荐 XFS（或 ext4 也行）


#### 四、创建挂载点并挂载（临时挂载验证）
创建目录并挂载：
```
sudo mkdir -p /data
sudo mount /dev/ubuntu-vg/minio /data
```
检查挂载：
```
df -hT /data
# 或
lsblk -f
```
应看到 /dev/mapper/ubuntu--vg-minio 已挂载到 /data，文件系统类型 xfs，容量约 9T。


#### 五、设置合适的挂载选项并写入 /etc/fstab（自动挂载且优化）
建议使用 XFS 常用选项：noatime,nodiratime,attr2,inode64。把设备用 UUID 写入 fstab 更稳妥：
先获取 /dev 的 UUID：
```
sudo blkid /dev/ubuntu-vg/minio
```
复制 UUID="..." 部分，然后编辑 /etc/fstab（用 root 权限）
```
sudo cp /etc/fstab /etc/fstab.bak    # 备份 fstab
sudo vi /etc/fstab
```
添加一行（用你上一步得到的 UUID:71800ad3-9c32-45f2-8915-92888db5b1fe 替换 <YOUR-UUID>；如果不想用 UUID，也可用设备路径 /dev/ubuntu-vg/minio）：
```
UUID=<YOUR-UUID>  /data  xfs  defaults,noatime,nodiratime,attr2,inode64  0  2
```
保存后测试 fstab 无误并重新挂载（先卸载再 mount -a）：
```
sudo umount /data
sudo mount -a
mount | grep ' /data '
```
如果 mount -a 没报错，说明写入成功。

#### 六、设定目录权限（给 MinIO 或其他服务用）
```
sudo useradd -r -s /sbin/nologin minio || true
sudo chown -R minio:minio /data
sudo chmod 750 /data
```
- 说明：如果已经有 minio 用户就不需要创建 useradd（|| true 保证已存在不会报错）。

#### 七、验证空间与权限
```
df -h /data
sudo ls -ld /data
sudo ls -la /data
sudo getfattr -d -m - /data   # optional, 查看 xfs 属性
```

#### 八、如果将来想扩容或缩小
- 扩容：如果 VG 还有空闲空间，可用 lvextend -L +XG /dev/ubuntu-vg/minio 然后 xfs_growfs /data 在线扩容（XFS 支持在线扩展）。
- 缩小：XFS 不支持在线缩小，缩小比较复杂，需备份、重建文件系统或转换为 ext4 并小心操作。

示例扩容命令：
```
# 假设还剩余 1T 空间，扩展 1T
sudo lvextend -L +1T /dev/ubuntu-vg/minio
sudo xfs_growfs /data
df -h /data
```