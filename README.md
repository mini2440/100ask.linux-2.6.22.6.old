

拷贝 jz2440 的 linux 源码和 patch 文件到 ubuntu 下

    user@vmware:~/workspace/mini2440/100ask$ ls
    linux-2.6.22.6.tar.bz2       u-boot-1.1.6.tar.bz2
    linux-2.6.22.6_jz2440.patch  u-boot-1.1.6_jz2440.patch

解压 linux 并打补丁

    user@vmware:~/workspace/mini2440/100ask$ tar jxf linux-2.6.22.6.tar.bz2 
    user@vmware:~/workspace/mini2440/100ask$ cd linux-2.6.22.6/
    user@vmware:~/workspace/mini2440/100ask/linux-2.6.22.6$ patch -p1 < ../linux-2.6.22.6_jz2440.patch

配置编译 linux

    user@vmware:~/workspace/mini2440/100ask/linux-2.6.22.6$ make clean
    user@vmware:~/workspace/mini2440/100ask/linux-2.6.22.6$ cp config_ok .config
    user@vmware:~/workspace/mini2440/100ask/linux-2.6.22.6$ make uImage
    Makefile:1449: *** mixed implicit and normal rules: deprecated syntax
    /home/user/workspace/mini2440/100ask/linux-2.6.22.6/Makefile:416: *** mixed implicit and normal rules: deprecated syntax
    /home/user/workspace/mini2440/100ask/linux-2.6.22.6/Makefile:1449: *** mixed implicit and normal rules: deprecated syntax
    make[1]: *** No rule to make target 'silentoldconfig'.  Stop.
      CHK     include/linux/version.h
    make: *** No rule to make target 'include/config/auto.conf', needed by 'include/asm-arm/.arch'.  Stop.

百度得到的结论：是由于我的系统的 make 工具太新，make 的旧版规则已经无法兼容新版  
参考博客：<https://blog.csdn.net/sinat_24088685/article/details/51009472>  
参照博客修改后重新 make 试试

    user@vmware:~/workspace/mini2440/100ask/linux-2.6.22.6$ make uImage
    ... ...
      LD      arch/arm/boot/compressed/vmlinux
      OBJCOPY arch/arm/boot/zImage
      Kernel: arch/arm/boot/zImage is ready
      UIMAGE  arch/arm/boot/uImage
    "mkimage" command not found - U-Boot images will not be built
      Image arch/arm/boot/uImage is read

uImage 还是没有编译出来，提示说需要 安装 mkimage 工具，安装 mkimage 后试试

    user@vmware:~/workspace/mini2440/100ask/linux-2.6.22.6$ sudo apt-get install u-boot-tools
    user@vmware:~/workspace/mini2440/100ask/linux-2.6.22.6$ make uImage
    ... ...
    Image Name:   Linux-2.6.22.6
    Created:      Tue May  1 19:39:40 2018
    Image Type:   ARM Linux Kernel Image (uncompressed)
    Data Size:    1812732 Bytes = 1770.25 kB = 1.73 MB
    Load Address: 30008000
    Entry Point:  30008000
      Image arch/arm/boot/uImage is ready

成功编译出 uImage，接下来修改 lcd 和 net card 驱动使此驱动支持 mini2440，网卡驱动可以在 100ask 光盘中获取  
lcd 驱动不行，由于我使用的 lcd 是 x35，lcd 驱动参考 <https://blog.csdn.net/comwise/article/details/11727445  
屏蔽其中三行报错的头文件，最终编译得到 uImage
