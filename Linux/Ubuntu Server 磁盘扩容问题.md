# 磁盘分区扩容

1. 删除磁盘上不必要的文件；
2. 想办法扩容磁盘。

第一种方法没有什么好说的，使用 `rm -rf [文件名]` 的方式删除不必要的文件。第二种方法的话，稍微复杂一些。

Step 1：查看磁盘情况；

```shell
 df -h
1
```

输出的信息如下：

```shell
Filesystem                         Size  Used Avail Use% Mounted on
tmpfs                              1.6G  1.6M  1.6G   1% /run
/dev/mapper/ubuntu--vg-ubuntu--lv  197G  139G   49G  75% /
tmpfs                              7.8G   56K  7.8G   1% /dev/shm
tmpfs                              5.0M     0  5.0M   0% /run/lock
/dev/sda2                          2.0G  127M  1.7G   7% /boot
tmpfs                              1.6G  4.0K  1.6G   1% /run/user/1000
1234567
```

可以看到 `/dev/mapper/ubuntu--vg-ubuntu--lv` 的 Size 是 197G，已经使用了 139G。

Step 2：查看还可以扩容的空间大小；

```shell
sudo vgdisplay
```

输出的信息如下：

```shell
[sudo] password for harveyx:
  --- Volume group ---
  VG Name               ubuntu-vg
  System ID
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  3
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                1
  Open LV               1
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               <218.00 GiB
  PE Size               4.00 MiB
  Total PE              55807
  Alloc PE / Size       51200 / 200.00 GiB
  Free  PE / Size       4607 / <18.00 GiB
  VG UUID               qgEcbC-D9m3-vFFw-Pmvu-ycN6-JA5i-3bvx4S
123456789101112131415161718192021
```

可以看到 `Free PE / Size <18.00 GiB`。 这是还可以扩容的大小。

Step 3：使用命令进行扩容；

```shell
lvextend -L 10G /dev/mapper/ubuntu--vg-ubuntu--lv    # 增加至 10 G
lvextend -L +10G /dev/mapper/ubuntu--vg-ubuntu--lv   # 再增加 10 G
lvreduce -L 10G /dev/mapper/ubuntu--vg-ubuntu--lv    # 减小至 50 G
lvreduce -L -10G /dev/mapper/ubuntu--vg-ubuntu--lv   # 再减小 8 G
```

调整完成后，开始执行调整：

```shell
resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv
```

Step 4：检查是否扩容成功

```shell
df -h
```

此时，就可以看到 `/dev/mapper/ubuntu--vg-ubuntu--lv` 的容量变成了 Step 3 扩容的大小。

> 假如使用的 VMware 虚拟机的话，需要先关闭 Ubuntu，然后在 Settings > Hard Disk 的位置调整存储大小。
