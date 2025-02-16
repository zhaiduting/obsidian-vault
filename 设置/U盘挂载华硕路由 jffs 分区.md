以下命令显示 JFFS（Journaling Flash File System）分区可用空间 40.4M，U 盘为 13.4G

```shell
zhaiduting@RT-BE88U-E830:/tmp/home/root# df -h
Filesystem                Size      Used Available Use% Mounted on
/dev/root                59.5M     59.5M         0 100% /
devtmpfs                809.4M         0    809.4M   0% /dev
ubi:data                 16.8M    200.0K     15.7M   1% /data
tmpfs                  1000.0M    420.0K    999.6M   0% /var
tmpfs                  1000.0M      1.2M    998.8M   0% /tmp/mnt
ubi:defaults           1000.0M      1.2M    998.8M   0% /tmp/mnt/defaults
ubi:jffs2                44.5M      1.8M     40.4M   4% /jffs
tmpfs                  1000.0M      1.2M    998.8M   0% /tmp/mnt
ubi:defaults           1000.0M      1.2M    998.8M   0% /tmp/mnt/defaults
tmpfs                  1000.0M      1.2M    998.8M   0% /tmp
/dev/sda                 14.1G      5.0M     13.4G   0% /tmp/mnt/sda
```

将 U 盘挂载到 jffs 后可用容量变成 13.4G，这么大的容量足以安装软件中心的绝大部分应用了。

```shell
zhaiduting@RT-BE88U-E830:/tmp/home/root# df -h
Filesystem                Size      Used Available Use% Mounted on
/dev/root                59.5M     59.5M         0 100% /
devtmpfs                809.4M         0    809.4M   0% /dev
ubi:data                 16.8M    196.0K     15.7M   1% /data
tmpfs                  1000.0M    420.0K    999.6M   0% /var
tmpfs                  1000.0M      1.2M    998.8M   0% /tmp/mnt
ubi:defaults           1000.0M      1.2M    998.8M   0% /tmp/mnt/defaults
ubi:jffs2                14.1G      4.8M     13.4G   0% /jffs
tmpfs                  1000.0M      1.2M    998.8M   0% /tmp/mnt
ubi:defaults           1000.0M      1.2M    998.8M   0% /tmp/mnt/defaults
tmpfs                  1000.0M      1.2M    998.8M   0% /tmp
/dev/sda                 14.1G      4.8M     13.4G   0% /tmp/mnt/sda
/dev/sda                 14.1G      4.8M     13.4G   0% /jffs
```

### 具体挂载步骤如下：

1、将[U 盘格式化为 ext4 文件系统](U盘格式化为ext4文件系统)，其它格式不行
2、将 jffs 分区的内容复制到 U 盘

```shell
# `/jffs/.` 表示复制 `/jffs/` 目录下的所有内容（包括隐藏文件），
# 但不会复制上级目录中的文件。
cp -av /jffs/. /tmp/mnt/sda/
```

错误写法 1：`cp -a /jffs/ /dev/mnt/sda`
这样写虽然可以将隐藏文件夹`.koolshare`一起复制过去，==但是 U 盘根目录会多出一个 jffs 文件夹==，实际上这个顶层文件夹是不需要的。

错误写法 2：很多隐藏文件夹都没有复制
`cp -a /jffs/* /dev/mnt/sda`
`cp -a /jffs/.koolshare /dev/mnt/sda`

错误写法 3：`cp -av /jffs/* /jffs/.* /tmp/mnt/sda/`
==`/jffs/.*` 会匹配到上级目录 `/jffs/..` ==，这会导致 jffs 分区之外的文件也会被复制！

3、使用`vi /jffs/scripts/post-mount`修改脚本文件，最终内容如下

```shell
#!/bin/sh

if [ "$1" = "/tmp/mnt/sda" ]; then
    umount /jffs
    mount --bind /tmp/mnt/sda /jffs
fi

/koolshare/bin/ks-mount-start.sh start $1
```

4、使用`reboot`命令重启路由器
5、通过 ssh 连接路由器，使用`df -h`命令查看挂载是否生效。

### 加入循环等待

```shell
#!/bin/sh

LOG_FILE="/tmp/mount.log"  # 日志文件路径
DEBUG_MODE=1                # 调试模式：1 开启，0 关闭

# 调试模式函数
log() {
    if [ "$DEBUG_MODE" = "1" ]; then
        echo "$(date '+%Y-%m-%d %H:%M:%S') - $1" >> "$LOG_FILE"
    fi
}

if [ "$1" = "/tmp/mnt/sda" ]; then
    log "Processing /tmp/mnt/sda..."

    # 停止 skipd 和 perp
    killall skipd
    /koolshare/perp/perp.sh stop

    # 卸载 /jffs
    log "Unmounting /jffs..."
    umount -l /jffs

    # 等待 /jffs 完全卸载
    while grep -qs '/jffs ' /proc/mounts; do
        log "Waiting for /jffs to be completely unmounted..."
        sleep 1
    done
    log "/jffs has been completely unmounted."

    # 挂载 /tmp/mnt/sda 到 /jffs
    log "Mounting /tmp/mnt/sda to /jffs..."
    if mount /tmp/mnt/sda /jffs; then
        log "Successfully mounted /tmp/mnt/sda to /jffs."

        # 等待 /jffs 完全挂载
        while ! grep -qs '/jffs ' /proc/mounts; do
            log "Waiting for /jffs to be completely mounted..."
            sleep 1
        done
        log "/jffs has been completely mounted."

        # 启动 skipd 和 perp
        log "Starting skipd and perp..."
        service restart_skipd
        /koolshare/perp/perp.sh start
    else
        log "Failed to mount /tmp/mnt/sda to /jffs."
    fi
fi

/koolshare/bin/ks-mount-start.sh start $1

```

### 解决梅林 Clash 不能开机启动的问题

在以上文件末尾加入以下代码

- -d 选项指定 clash 的工作目录
- -f 选项指定配置文件
- 结尾的  & 指定 clash 在后台运行

```shell
if $(pidof clash) > /dev/null; then
	kill $(pidof clash)
fi

/koolshare/bin/clash -d /koolshare/merlinclash/ -f /koolshare/merlinclash/yaml_use/mihomo.yaml &

```
