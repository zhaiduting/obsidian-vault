~ $ id
uid=10170(u0_a170) gid=10170(u0_a170) groups=10170(u0_a170),1077(external_storage),3003(inet),9997(everybody),20170(u0_a170_cache),50170(all_a170),99909997 context=u:r:untrusted_app:s0:c170,c256,c512,c768
~ $ su
No su program found on this device. Termux
does not supply tools for rooting, see e.g.
http://www.androidcentral.com/root for
information about rooting Android.

以上信息表明当前设备没有 root权限。在执行 `id` 命令时，看到的 UID 是普通用户（`u0_a170`），而不是 root 用户（`uid=0(root)`）。另外，执行 `su` 命令时提示没有找到 `su` 程序，这也表明设备没有 root 权限。