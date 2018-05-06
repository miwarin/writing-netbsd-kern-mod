# writing-netbsd-kern-mod

study [Writing a NetBSD kernel module](https://saurvs.github.io/post/writing-netbsd-kern-mod/)


mkmod すると /proc/devices に出る

    % cat /proc/devices | grep rperm
    420 rperm

modload ./rperm.kmod すると modstat に出る

    % modstat | grep rperm
    rperm                   driver   filesys  -        0     474 -

kmod を make するとファイルがいろいろ作られる。ファイルを置く場所は ${HOME} の下でもいいみたい(gcc に PATH が通っていればいい？)(Makefile の bsd.module.mk への PATH は誰がいつ通すのか)

    % ls -l 
    total 36
    -rwxr--r-- 1 rin users   57 5月   6 14:53 Makefile*
    -rw-r--r-- 1 rin users  123 5月   6 14:03 README.md
    lrwxr-xr-x 1 rin users   31 5月   6 14:54 amd64 -> /usr/src/sys/arch/amd64/include/
    lrwxr-xr-x 1 rin users   30 5月   6 14:54 i386 -> /usr/src/sys/arch/i386/include/
    lrwxr-xr-x 1 rin users   31 5月   6 14:54 machine -> /usr/src/sys/arch/amd64/include/
    -rwxr--r-- 1 rin users 2869 5月   6 14:54 rperm.c*
    -rw-r--r-- 1 rin users 5472 5月   6 14:54 rperm.kmod
    -rw-r--r-- 1 rin users 1962 5月   6 14:54 rperm.kmod.map
    -rw-r--r-- 1 rin users 6992 5月   6 14:54 rperm.o
    -rwxr-xr-x 1 rin users 8416 5月   6 15:25 test*
    -rwxr--r-- 1 rin users  642 5月   6 15:25 test.c*
    lrwxr-xr-x 1 rin users   29 5月   6 14:54 x86 -> /usr/src/sys/arch/x86/include/


```write``` に渡されるデータはユーザーランドから渡されたデータそのまま。たとえば rperm_write に以下のように ```printf``` すると

        int i;
        for(i = 0; i < sc.buf_len; i++)
        {
                printf("%02x ", sc.buf[i]);
        }

dmesg に以下のように出力される。0x20 は空白文字。

    48 65 6c 6c 6f 20 4e 65 74 42 53 44 
     H  e  l  l  o     N  e  t  B  S  D
