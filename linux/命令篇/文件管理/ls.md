# ls

> 显示目录中的文件及其属性信息

## 英文全拼

`list`

## 语法格式

```bash
ls [参数] [文件或目录]
```

## 参数



- 无参数

```bash
root@ubuntu:/# ls
bin   cdrom  etc   lib    lost+found  mnt  proc  run   snap  swap.img  tmp  var
boot  dev    home  lib64  media       opt  root  sbin  srv   sys       usr
```

- -a 显示隐藏文件

```bash
root@ubuntu:/# ls -a
.   bin   cdrom  etc   lib    lost+found  mnt  proc  run   snap  swap.img  tmp  var
..  boot  dev    home  lib64  media       opt  root  sbin  srv   sys       usr
```

- -l 以长格式显示文件的详细信息，包括权限、所有者、大小和修改时间

```bash
root@ubuntu:/# ls -l
total 4194380
lrwxrwxrwx   1 root root          7 Apr 20 16:46 bin -> usr/bin
drwxr-xr-x   3 root root       4096 Jul  4 13:46 boot
dr-xr-xr-x   2 root root       4096 Apr 23 10:18 cdrom
drwxr-xr-x  19 root root       4300 Jul  8 15:13 dev
drwxr-xr-x 147 root root      12288 Jul  7 13:52 etc
drwxr-xr-x   4 root root       4096 Jul  4 17:42 home
lrwxrwxrwx   1 root root          7 Apr 20 16:46 lib -> usr/lib
lrwxrwxrwx   1 root root          9 Apr 20 16:46 lib64 -> usr/lib64
drwx------   2 root root      16384 Jul  3 21:36 lost+found
drwxr-xr-x   2 root root       4096 Apr 23 09:15 media
drwxr-xr-x   2 root root       4096 Apr 23 09:15 mnt
drwxr-xr-x   3 root root       4096 Jul  4 17:17 opt
dr-xr-xr-x 378 root root          0 Jul  8 15:13 proc
drwx------   6 root root       4096 Jul  4 17:58 root
drwxr-xr-x  46 root root       1100 Jul  8 15:16 run
lrwxrwxrwx   1 root root          8 Apr 20 16:46 sbin -> usr/sbin
drwxr-xr-x  15 root root       4096 Apr 23 09:22 snap
drwxr-xr-x   2 root root       4096 Apr 23 09:15 srv
-rw-------   1 root root 4294967296 Jul  3 21:39 swap.img
dr-xr-xr-x  13 root root          0 Jul  8 15:13 sys
drwxrwxrwt  16 root root        360 Jul  8 15:19 tmp
drwxr-xr-x  12 root root       4096 Apr 23 09:15 usr
drwxr-xr-x  15 root root       4096 Jul  4 13:47 var
root@ubuntu:/# 
```

- h 以人类可读的格式显示文件大小（例如：1K，234M，2G）

```bash
zhaohui@ubuntu-26-04-lts:/$ ls -lh
total 4.1G
lrwxrwxrwx   1 root root    7 Apr 20 16:46 bin -> usr/bin
drwxr-xr-x   3 root root 4.0K Jun  4 21:33 boot
dr-xr-xr-x   2 root root 4.0K Apr 23 10:18 cdrom
drwxr-xr-x  19 root root 4.2K Jun 14 20:44 dev
drwxr-xr-x 144 root root  12K Jun  8 21:22 etc
drwxr-xr-x   4 root root 4.0K Jun  5 19:58 home
lrwxrwxrwx   1 root root    7 Apr 20 16:46 lib -> usr/lib
lrwxrwxrwx   1 root root    9 Apr 20 16:46 lib64 -> usr/lib64
drwx------   2 root root  16K Jun  3 21:16 lost+found
drwxr-xr-x   2 root root 4.0K Apr 23 09:15 media
drwxr-xr-x   2 root root 4.0K Apr 23 09:15 mnt
drwxr-xr-x   2 root root 4.0K Apr 23 09:15 opt
dr-xr-xr-x 381 root root    0 Jun 14 20:44 proc
drwx------   6 root root 4.0K Jun  8 21:29 root
drwxr-xr-x  46 root root 1.1K Jun 14 21:00 run
lrwxrwxrwx   1 root root    8 Apr 20 16:46 sbin -> usr/sbin
drwxr-xr-x  15 root root 4.0K Apr 23 09:22 snap
drwxr-xr-x   2 root root 4.0K Apr 23 09:15 srv
-rw-------   1 root root 4.0G Jun  3 21:23 swap.img
dr-xr-xr-x  13 root root    0 Jun 14 21:08 sys
drwxrwxrwt  16 root root  360 Jun 14 21:00 tmp
drwxr-xr-x  12 root root 4.0K Apr 23 09:15 usr
drwxr-xr-x  15 root root 4.0K Jun  4 20:07 var
```

