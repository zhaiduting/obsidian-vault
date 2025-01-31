### 相关命令

查看存储设备

```
~ > diskutil list
# 此处省略其它设备

/dev/disk8 (external, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:     FDisk_partition_scheme                        *15.5 GB    disk8
   1:                 DOS_FAT_32 U                       15.5 GB    disk8s1
```

格式化前需要卸载 U 盘`diskutil unmountDisk /dev/disk8`
使用`sudo mkfs.ext4 /dev/disk8`格式化 U 盘，这条命令需要满足以下条件

- 需要安装  `e2fsprogs`，安装命令为`brew install e2fsprogs`
- 修改 .zshrc 文件配置路径变量

```
export PATH="/opt/homebrew/opt/e2fsprogs/bin:$PATH"
export PATH="/opt/homebrew/opt/e2fsprogs/sbin:$PATH"
```

### 终端记录

```
~ > diskutil unmountDisk /dev/disk8
Unmount of all volumes on disk8 was successful
~ > mkfs.ext4 /dev/disk8
mke2fs 1.47.2 (1-Jan-2025)
mkfs.ext4: Permission denied 尝试确定文件系统大小时
~ >
~ > sudo mkfs.ext4 /dev/disk8
Password:
mke2fs 1.47.2 (1-Jan-2025)
创建含有 3782656 个块（每块 4k）和 946560 个 inode 的文件系统
文件系统 UUID：c592231e-ce58-4544-bd4a-ecfcb42451fd
超级块的备份存储于下列块：
	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208

正在分配组表：完成
正在写入 inode表：完成
创建日志（16384 个块）：完成
写入超级块和文件系统账户统计信息：已完成

~ > diskutil list

/dev/disk8 (external, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:                                                   *15.5 GB    disk8
```
