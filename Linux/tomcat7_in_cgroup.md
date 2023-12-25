# tomcat7 in memory-cgroup

将物理机tomcat服务直接加入memory 控制组，memory.stat 中 cache 不为0，但是 mmaped_file 为0。很奇怪。使用docker 起tomcat容器，甚至cache也为0

## cat memory.stat

```bash
root@ubuntu20:/sys/fs/cgroup/memory/myTomcat# cat memory.stat 
cache 3244032
rss 75956224
rss_huge 0
shmem 0
mapped_file 0
dirty 0
writeback 0
pgpgin 28776
pgpgout 9374
pgfault 28215
pgmajfault 0
inactive_anon 0
active_anon 75964416
inactive_file 2027520
active_file 1216512
unevictable 0
hierarchical_memory_limit 9223372036854771712
total_cache 3244032
total_rss 75956224
total_rss_huge 0
total_shmem 0
total_mapped_file 0
total_dirty 0
total_writeback 0
total_pgpgin 28776
total_pgpgout 9374
total_pgfault 28215
total_pgmajfault 0
total_inactive_anon 0
total_active_anon 75964416
total_inactive_file 2027520
total_active_file 1216512
total_unevictable 0
```

rss+mapped_file =75MB  完全不等于

* top命令输出的res
* ps命令输出的rss  180692

```bash
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root        6155  0.2  2.2 5818324 180692 pts/0  Sl+  15:52   0:06 /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java -Djava.util.logging.config.file=/usr/local/tomcat/apache-tomcat-7.0.109//conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djdk.tls.ephemeralDHKeySize=2048 -Dorg.apache.catalina.security.SecurityListener.UMASK=0027 -Dignore.endorsed.dirs= -classpath /usr/local/tomcat/apache-tomcat-7.0.109//bin/bootstrap.jar:/usr/local/tomcat/apache-tomcat-7.0.109//bin/tomcat-juli.jar -Dcatalina.base=/usr/local/tomcat/apache-tomcat-7.0.109/ -Dcatalina.home=/usr/local/tomcat/apache-tomcat-7.0.109/ -Djava.io.tmpdir=/usr/local/tomcat/apache-tomcat-7.0.109//temp org.apache.catalina.startup.Bootstrap start
root        8089  0.0  0.0   6300   720 pts/1    S+   16:28   0:00 grep --color=auto 6155
```

## cat /proc/6155/maps  查看内存映射情况

```bash
root@ubuntu20:/sys/fs/cgroup/memory/myTomcat# cat /proc/6155/maps
83c00000-87580000 rw-p 00000000 00:00 0 
87580000-d6980000 ---p 00000000 00:00 0 
d6980000-df380000 rw-p 00000000 00:00 0 
df380000-100000000 ---p 00000000 00:00 0 
100000000-100280000 rw-p 00000000 00:00 0 
100280000-140000000 ---p 00000000 00:00 0 
565508c52000-565508c53000 r--p 00000000 fd:00 2888943                    /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java
565508c53000-565508c54000 r-xp 00001000 fd:00 2888943                    /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java
565508c54000-565508c55000 r--p 00002000 fd:00 2888943                    /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java
565508c55000-565508c56000 r--p 00002000 fd:00 2888943                    /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java
565508c56000-565508c57000 rw-p 00003000 fd:00 2888943                    /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java
56550944d000-56550946e000 rw-p 00000000 00:00 0                          [heap]
7f6d04000000-7f6d04021000 rw-p 00000000 00:00 0 
7f6d04021000-7f6d08000000 ---p 00000000 00:00 0 
7f6d08000000-7f6d08021000 rw-p 00000000 00:00 0 
7f6d08021000-7f6d0c000000 ---p 00000000 00:00 0 
7f6d0c000000-7f6d0c021000 rw-p 00000000 00:00 0 
7f6d0c021000-7f6d10000000 ---p 00000000 00:00 0 
7f6d10000000-7f6d10021000 rw-p 00000000 00:00 0 
7f6d10021000-7f6d14000000 ---p 00000000 00:00 0 
7f6d14000000-7f6d14021000 rw-p 00000000 00:00 0 
7f6d14021000-7f6d18000000 ---p 00000000 00:00 0 
7f6d18000000-7f6d18021000 rw-p 00000000 00:00 0 
7f6d18021000-7f6d1c000000 ---p 00000000 00:00 0 
7f6d1c000000-7f6d1c021000 rw-p 00000000 00:00 0 
7f6d1c021000-7f6d20000000 ---p 00000000 00:00 0 
7f6d20000000-7f6d20021000 rw-p 00000000 00:00 0 
7f6d20021000-7f6d24000000 ---p 00000000 00:00 0 
7f6d24000000-7f6d24035000 rw-p 00000000 00:00 0 
7f6d24035000-7f6d28000000 ---p 00000000 00:00 0 
7f6d28000000-7f6d28021000 rw-p 00000000 00:00 0 
7f6d28021000-7f6d2c000000 ---p 00000000 00:00 0 
7f6d2c000000-7f6d2c32a000 rw-p 00000000 00:00 0 
7f6d2c32a000-7f6d30000000 ---p 00000000 00:00 0 
7f6d30000000-7f6d30021000 rw-p 00000000 00:00 0 
7f6d30021000-7f6d34000000 ---p 00000000 00:00 0 
7f6d34000000-7f6d34130000 rw-p 00000000 00:00 0 
7f6d34130000-7f6d38000000 ---p 00000000 00:00 0 
7f6d38000000-7f6d38021000 rw-p 00000000 00:00 0 
7f6d38021000-7f6d3c000000 ---p 00000000 00:00 0 
7f6d3c000000-7f6d3c021000 rw-p 00000000 00:00 0 
7f6d3c021000-7f6d40000000 ---p 00000000 00:00 0 
7f6d40000000-7f6d40021000 rw-p 00000000 00:00 0 
7f6d40021000-7f6d44000000 ---p 00000000 00:00 0 
7f6d44000000-7f6d44021000 rw-p 00000000 00:00 0 
7f6d44021000-7f6d48000000 ---p 00000000 00:00 0 
7f6d48000000-7f6d48021000 rw-p 00000000 00:00 0 
7f6d48021000-7f6d4c000000 ---p 00000000 00:00 0 
7f6d4c000000-7f6d4c656000 rw-p 00000000 00:00 0 
7f6d4c656000-7f6d50000000 ---p 00000000 00:00 0 
7f6d50000000-7f6d50817000 rw-p 00000000 00:00 0 
7f6d50817000-7f6d54000000 ---p 00000000 00:00 0 
7f6d54000000-7f6d5459a000 rw-p 00000000 00:00 0 
7f6d5459a000-7f6d58000000 ---p 00000000 00:00 0 
7f6d58000000-7f6d5a082000 rw-p 00000000 00:00 0 
7f6d5a082000-7f6d5c000000 ---p 00000000 00:00 0 
7f6d5c000000-7f6d5c021000 rw-p 00000000 00:00 0 
7f6d5c021000-7f6d60000000 ---p 00000000 00:00 0 
7f6d60000000-7f6d60021000 rw-p 00000000 00:00 0 
7f6d60021000-7f6d64000000 ---p 00000000 00:00 0 
7f6d64000000-7f6d64021000 rw-p 00000000 00:00 0 
7f6d64021000-7f6d68000000 ---p 00000000 00:00 0 
7f6d68000000-7f6d68021000 rw-p 00000000 00:00 0 
7f6d68021000-7f6d6c000000 ---p 00000000 00:00 0 
7f6d6c000000-7f6d6c021000 rw-p 00000000 00:00 0 
7f6d6c021000-7f6d70000000 ---p 00000000 00:00 0 
7f6d70000000-7f6d70021000 rw-p 00000000 00:00 0 
7f6d70021000-7f6d74000000 ---p 00000000 00:00 0 
7f6d74000000-7f6d74021000 rw-p 00000000 00:00 0 
7f6d74021000-7f6d78000000 ---p 00000000 00:00 0 
7f6d7c000000-7f6d7c021000 rw-p 00000000 00:00 0 
7f6d7c021000-7f6d80000000 ---p 00000000 00:00 0 
7f6d80000000-7f6d80021000 rw-p 00000000 00:00 0 
7f6d80021000-7f6d84000000 ---p 00000000 00:00 0 
7f6d84000000-7f6d84021000 rw-p 00000000 00:00 0 
7f6d84021000-7f6d88000000 ---p 00000000 00:00 0 
7f6d89bbb000-7f6d89d3b000 rw-p 00000000 00:00 0 
7f6d89d3b000-7f6d89dbb000 ---p 00000000 00:00 0 
7f6d89dbb000-7f6d89fbb000 rw-p 00000000 00:00 0 
7f6d89fbb000-7f6d8a1bb000 rw-p 00000000 00:00 0 
7f6d8a1bb000-7f6d8a1be000 ---p 00000000 00:00 0 
7f6d8a1be000-7f6d8a2bc000 rw-p 00000000 00:00 0 
7f6d8a2bc000-7f6d8a2bf000 ---p 00000000 00:00 0 
7f6d8a2bf000-7f6d8a3bd000 rw-p 00000000 00:00 0 
7f6d8a3bd000-7f6d8a3c0000 ---p 00000000 00:00 0 
7f6d8a3c0000-7f6d8a4be000 rw-p 00000000 00:00 0 
7f6d8a4be000-7f6d8a4c1000 ---p 00000000 00:00 0 
7f6d8a4c1000-7f6d8a5bf000 rw-p 00000000 00:00 0 
7f6d8a5bf000-7f6d8a5c2000 ---p 00000000 00:00 0 
7f6d8a5c2000-7f6d8a6c0000 rw-p 00000000 00:00 0 
7f6d8a6c0000-7f6d8a6c3000 ---p 00000000 00:00 0 
7f6d8a6c3000-7f6d8a7c1000 rw-p 00000000 00:00 0 
7f6d8a7c1000-7f6d8a7c4000 ---p 00000000 00:00 0 
7f6d8a7c4000-7f6d8a8c2000 rw-p 00000000 00:00 0 
7f6d8a8c2000-7f6d8a8c5000 ---p 00000000 00:00 0 
7f6d8a8c5000-7f6d8a9c3000 rw-p 00000000 00:00 0 
7f6d8a9c3000-7f6d8a9c6000 ---p 00000000 00:00 0 
7f6d8a9c6000-7f6d8aac4000 rw-p 00000000 00:00 0 
7f6d8aac4000-7f6d8aac7000 ---p 00000000 00:00 0 
7f6d8aac7000-7f6d8abc5000 rw-p 00000000 00:00 0 
7f6d8abc5000-7f6d8abc8000 ---p 00000000 00:00 0 
7f6d8abc8000-7f6d8acc6000 rw-p 00000000 00:00 0 
7f6d8acc6000-7f6d8acc9000 ---p 00000000 00:00 0 
7f6d8acc9000-7f6d8adc7000 rw-p 00000000 00:00 0 
7f6d8adc7000-7f6d8adca000 ---p 00000000 00:00 0 
7f6d8adca000-7f6d8b0c8000 rw-p 00000000 00:00 0 
7f6d8b0c8000-7f6d8b2c8000 rw-p 00000000 00:00 0 
7f6d8b2c8000-7f6d8b2cb000 ---p 00000000 00:00 0 
7f6d8b2cb000-7f6d8b3c9000 rw-p 00000000 00:00 0 
7f6d8b3c9000-7f6d8b3cc000 ---p 00000000 00:00 0 
7f6d8b3cc000-7f6d8b4ca000 rw-p 00000000 00:00 0 
7f6d8b4ca000-7f6d8b4d1000 r--p 00000000 fd:00 2888986                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libsunec.so
7f6d8b4d1000-7f6d8b4ea000 r-xp 00007000 fd:00 2888986                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libsunec.so
7f6d8b4ea000-7f6d8b4f4000 r--p 00020000 fd:00 2888986                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libsunec.so
7f6d8b4f4000-7f6d8b4f8000 r--p 00029000 fd:00 2888986                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libsunec.so
7f6d8b4f8000-7f6d8b4f9000 rw-p 0002d000 fd:00 2888986                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libsunec.so
7f6d8b4f9000-7f6d8b4fc000 ---p 00000000 00:00 0 
7f6d8b4fc000-7f6d8b7fa000 rw-p 00000000 00:00 0 
7f6d8b7fa000-7f6d8b9fa000 rw-p 00000000 00:00 0 
7f6d8b9fa000-7f6d8b9fb000 ---p 00000000 00:00 0 
7f6d8b9fb000-7f6d8bafb000 rw-p 00000000 00:00 0 
7f6d8bafb000-7f6d8bafe000 ---p 00000000 00:00 0 
7f6d8bafe000-7f6d8bbfc000 rw-p 00000000 00:00 0 
7f6d8bbfc000-7f6d8bbfd000 ---p 00000000 00:00 0 
7f6d8bbfd000-7f6d8bc00000 ---p 00000000 00:00 0 
7f6d8bc00000-7f6d8bcfd000 rw-p 00000000 00:00 0 
7f6d8bcfd000-7f6d8bcfe000 ---p 00000000 00:00 0 
7f6d8bcfe000-7f6d8bd01000 ---p 00000000 00:00 0 
7f6d8bd01000-7f6d8bdfe000 rw-p 00000000 00:00 0 
7f6d8bdfe000-7f6d8bdff000 ---p 00000000 00:00 0 
7f6d8bdff000-7f6d8be02000 ---p 00000000 00:00 0 
7f6d8be02000-7f6d8beff000 rw-p 00000000 00:00 0 
7f6d8beff000-7f6d8bf00000 ---p 00000000 00:00 0 
7f6d8bf00000-7f6d8bf03000 ---p 00000000 00:00 0 
7f6d8bf03000-7f6d8c000000 rw-p 00000000 00:00 0 
7f6d8c000000-7f6d8c021000 rw-p 00000000 00:00 0 
7f6d8c021000-7f6d90000000 ---p 00000000 00:00 0 
7f6d90018000-7f6d9002a000 r--s 0034c000 fd:00 2889035                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/resources.jar
7f6d9002a000-7f6d9002c000 r--s 00016000 fd:00 2889024                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/jce.jar
7f6d9002c000-7f6d9003d000 r--s 001c5000 fd:00 2889030                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/jsse.jar
7f6d9003d000-7f6d90041000 r--p 00000000 fd:00 2888979                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libmanagement.so
7f6d90041000-7f6d90045000 r-xp 00004000 fd:00 2888979                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libmanagement.so
7f6d90045000-7f6d90047000 r--p 00008000 fd:00 2888979                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libmanagement.so
7f6d90047000-7f6d90048000 r--p 00009000 fd:00 2888979                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libmanagement.so
7f6d90048000-7f6d90049000 rw-p 0000a000 fd:00 2888979                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libmanagement.so
7f6d90049000-7f6d9005b000 r--s 00223000 fd:00 2492125                    /usr/local/tomcat/apache-tomcat-7.0.109/lib/ecj-4.4.2.jar
7f6d9005b000-7f6d9005e000 r--s 00018000 fd:00 2492140                    /usr/local/tomcat/apache-tomcat-7.0.109/lib/tomcat-i18n-zh-CN.jar
7f6d9005e000-7f6d90060000 r--s 00008000 fd:00 2492144                    /usr/local/tomcat/apache-tomcat-7.0.109/lib/websocket-api.jar
7f6d90060000-7f6d90064000 r--s 00018000 fd:00 2492136                    /usr/local/tomcat/apache-tomcat-7.0.109/lib/tomcat-i18n-fr.jar
7f6d90064000-7f6d90067000 r--s 0001a000 fd:00 2492138                    /usr/local/tomcat/apache-tomcat-7.0.109/lib/tomcat-i18n-ko.jar
7f6d90067000-7f6d9006a000 r--s 00013000 fd:00 2492129                    /usr/local/tomcat/apache-tomcat-7.0.109/lib/jsp-api.jar
7f6d9006a000-7f6d9007e000 r--s 00195000 fd:00 2492124                    /usr/local/tomcat/apache-tomcat-7.0.109/lib/catalina.jar
7f6d9007e000-7f6d90084000 r--s 00041000 fd:00 2492123                    /usr/local/tomcat/apache-tomcat-7.0.109/lib/catalina-tribes.jar
7f6d90084000-7f6d9008c000 r--s 0008e000 fd:00 2492128                    /usr/local/tomcat/apache-tomcat-7.0.109/lib/jasper.jar
7f6d9008c000-7f6d9008f000 r--s 00006000 fd:00 2492139                    /usr/local/tomcat/apache-tomcat-7.0.109/lib/tomcat-i18n-ru.jar
7f6d9008f000-7f6d90094000 r--s 0003c000 fd:00 2492133                    /usr/local/tomcat/apache-tomcat-7.0.109/lib/tomcat-dbcp.jar
7f6d90094000-7f6d90096000 r--s 0001f000 fd:00 2492122                    /usr/local/tomcat/apache-tomcat-7.0.109/lib/catalina-ha.jar
7f6d90096000-7f6d90099000 r--s 00020000 fd:00 2492141                    /usr/local/tomcat/apache-tomcat-7.0.109/lib/tomcat-jdbc.jar
7f6d90099000-7f6d9009b000 r--s 0000d000 fd:00 2492126                    /usr/local/tomcat/apache-tomcat-7.0.109/lib/el-api.jar
7f6d9009b000-7f6d9009e000 r--s 0000a000 fd:00 2492134                    /usr/local/tomcat/apache-tomcat-7.0.109/lib/tomcat-i18n-de.jar
7f6d9009e000-7f6d900a1000 r--s 0001c000 fd:00 2492137                    /usr/local/tomcat/apache-tomcat-7.0.109/lib/tomcat-i18n-ja.jar
7f6d900a1000-7f6d900ae000 r--s 000c7000 fd:00 2492132                    /usr/local/tomcat/apache-tomcat-7.0.109/lib/tomcat-coyote.jar
7f6d900ae000-7f6d900df000 rw-p 00000000 00:00 0 
7f6d900df000-7f6d900e2000 ---p 00000000 00:00 0 
7f6d900e2000-7f6d94000000 rw-p 00000000 00:00 0 
7f6d94000000-7f6d94021000 rw-p 00000000 00:00 0 
7f6d94021000-7f6d98000000 ---p 00000000 00:00 0 
7f6d98001000-7f6d98002000 r--s 00001000 fd:00 2492131                    /usr/local/tomcat/apache-tomcat-7.0.109/lib/tomcat-api.jar
7f6d98002000-7f6d98005000 r--s 00014000 fd:00 2492135                    /usr/local/tomcat/apache-tomcat-7.0.109/lib/tomcat-i18n-es.jar
7f6d98005000-7f6d9800a000 r--s 00034000 fd:00 2492143                    /usr/local/tomcat/apache-tomcat-7.0.109/lib/tomcat7-websocket.jar
7f6d9800a000-7f6d9800d000 r--s 0001e000 fd:00 2492127                    /usr/local/tomcat/apache-tomcat-7.0.109/lib/jasper-el.jar
7f6d9800d000-7f6d982f3000 r--p 00000000 fd:00 2622347                    /usr/lib/locale/locale-archive
7f6d982f3000-7f6d982f6000 ---p 00000000 00:00 0 
7f6d982f6000-7f6d983f4000 rw-p 00000000 00:00 0 
7f6d983f4000-7f6d983f7000 ---p 00000000 00:00 0 
7f6d983f7000-7f6d984f5000 rw-p 00000000 00:00 0 
7f6d984f5000-7f6d984f6000 ---p 00000000 00:00 0 
7f6d984f6000-7f6d99000000 rw-p 00000000 00:00 0 
7f6d99000000-7f6d99a30000 rwxp 00000000 00:00 0 
7f6d99a30000-7f6da8000000 ---p 00000000 00:00 0 
7f6da8000000-7f6da854f000 rw-p 00000000 00:00 0 
7f6da854f000-7f6dac000000 ---p 00000000 00:00 0 
7f6dac000000-7f6dac003000 r--s 0002e000 fd:00 2492130                    /usr/local/tomcat/apache-tomcat-7.0.109/lib/servlet-api.jar
7f6dac003000-7f6dac005000 r--s 0000c000 fd:00 2492121                    /usr/local/tomcat/apache-tomcat-7.0.109/lib/catalina-ant.jar
7f6dac005000-7f6dac007000 r--s 0000d000 fd:00 2492142                    /usr/local/tomcat/apache-tomcat-7.0.109/lib/tomcat-util.jar
7f6dac007000-7f6dac009000 r--s 0000b000 fd:00 2492116                    /usr/local/tomcat/apache-tomcat-7.0.109/bin/tomcat-juli.jar
7f6dac009000-7f6dac00b000 r--s 00005000 fd:00 2492110                    /usr/local/tomcat/apache-tomcat-7.0.109/bin/commons-daemon.jar
7f6dac00b000-7f6dac010000 r--p 00000000 fd:00 2888981                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libnet.so
7f6dac010000-7f6dac021000 r-xp 00005000 fd:00 2888981                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libnet.so
7f6dac021000-7f6dac025000 r--p 00016000 fd:00 2888981                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libnet.so
7f6dac025000-7f6dac026000 r--p 00019000 fd:00 2888981                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libnet.so
7f6dac026000-7f6dac027000 rw-p 0001a000 fd:00 2888981                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libnet.so
7f6dac027000-7f6dac02e000 r--p 00000000 fd:00 2888982                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libnio.so
7f6dac02e000-7f6dac037000 r-xp 00007000 fd:00 2888982                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libnio.so
7f6dac037000-7f6dac03a000 r--p 00010000 fd:00 2888982                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libnio.so
7f6dac03a000-7f6dac03b000 r--p 00012000 fd:00 2888982                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libnio.so
7f6dac03b000-7f6dac03c000 rw-p 00013000 fd:00 2888982                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libnio.so
7f6dac03c000-7f6dac03e000 r--s 00006000 fd:00 2492106                    /usr/local/tomcat/apache-tomcat-7.0.109/bin/bootstrap.jar
7f6dac03e000-7f6dac041000 r--s 0000f000 fd:00 2889005                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/ext/icedtea-sound.jar
7f6dac041000-7f6dac043000 r--s 00010000 fd:00 2889012                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/ext/zipfs.jar
7f6dac043000-7f6dac045000 r--s 00008000 fd:00 2889009                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/ext/sunec.jar
7f6dac045000-7f6dac047000 r--s 0000c000 fd:00 2652715                    /usr/share/java/java-atk-wrapper.jar
7f6dac047000-7f6dac049000 r--s 00001000 fd:00 2889004                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/ext/dnsns.jar
7f6dac049000-7f6dac065000 r--s 00394000 fd:00 2889003                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/ext/cldrdata.jar
7f6dac065000-7f6dac080000 r--s 001d6000 fd:00 2889008                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/ext/nashorn.jar
7f6dac080000-7f6dac08a000 r--s 00117000 fd:00 2889007                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/ext/localedata.jar
7f6dac08a000-7f6dac08d000 r--s 00042000 fd:00 2889011                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/ext/sunpkcs11.jar
7f6dac08d000-7f6dac093000 r--s 0003e000 fd:00 2889010                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/ext/sunjce_provider.jar
7f6dac093000-7f6dad31b000 rw-p 00000000 00:00 0 
7f6dad31b000-7f6dad4ed000 r--s 03cc9000 fd:00 2889036                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/rt.jar
7f6dad4ed000-7f6dae1ef000 rw-p 00000000 00:00 0 
7f6dae1ef000-7f6dae1f0000 ---p 00000000 00:00 0 
7f6dae1f0000-7f6dae2f0000 rw-p 00000000 00:00 0 
7f6dae2f0000-7f6dae2f1000 ---p 00000000 00:00 0 
7f6dae2f1000-7f6dae3f1000 rw-p 00000000 00:00 0 
7f6dae3f1000-7f6dae3f2000 ---p 00000000 00:00 0 
7f6dae3f2000-7f6dae4f2000 rw-p 00000000 00:00 0 
7f6dae4f2000-7f6dae4f3000 ---p 00000000 00:00 0 
7f6dae4f3000-7f6dae5f3000 rw-p 00000000 00:00 0 
7f6dae5f3000-7f6dae5f4000 ---p 00000000 00:00 0 
7f6dae5f4000-7f6dae6f4000 rw-p 00000000 00:00 0 
7f6dae6f4000-7f6dae6f5000 ---p 00000000 00:00 0 
7f6dae6f5000-7f6dae7f5000 rw-p 00000000 00:00 0 
7f6dae7f5000-7f6dae7f6000 ---p 00000000 00:00 0 
7f6dae7f6000-7f6dae8f6000 rw-p 00000000 00:00 0 
7f6dae8f6000-7f6dae8f7000 ---p 00000000 00:00 0 
7f6dae8f7000-7f6daea14000 rw-p 00000000 00:00 0 
7f6daea14000-7f6daec8e000 ---p 00000000 00:00 0 
7f6daec8e000-7f6daecab000 rw-p 00000000 00:00 0 
7f6daecab000-7f6daef24000 ---p 00000000 00:00 0 
7f6daef24000-7f6daef6a000 rw-p 00000000 00:00 0 
7f6daef6a000-7f6daf070000 ---p 00000000 00:00 0 
7f6daf070000-7f6daf09a000 rw-p 00000000 00:00 0 
7f6daf09a000-7f6daf431000 ---p 00000000 00:00 0 
7f6daf431000-7f6daf434000 r--p 00000000 fd:00 2888989                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libzip.so
7f6daf434000-7f6daf439000 r-xp 00003000 fd:00 2888989                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libzip.so
7f6daf439000-7f6daf43b000 r--p 00008000 fd:00 2888989                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libzip.so
7f6daf43b000-7f6daf43c000 r--p 00009000 fd:00 2888989                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libzip.so
7f6daf43c000-7f6daf43d000 rw-p 0000a000 fd:00 2888989                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libzip.so
7f6daf43d000-7f6daf440000 r--p 00000000 fd:00 2654023                    /usr/lib/x86_64-linux-gnu/libnss_files-2.31.so
7f6daf440000-7f6daf447000 r-xp 00003000 fd:00 2654023                    /usr/lib/x86_64-linux-gnu/libnss_files-2.31.so
7f6daf447000-7f6daf449000 r--p 0000a000 fd:00 2654023                    /usr/lib/x86_64-linux-gnu/libnss_files-2.31.so
7f6daf449000-7f6daf44a000 r--p 0000b000 fd:00 2654023                    /usr/lib/x86_64-linux-gnu/libnss_files-2.31.so
7f6daf44a000-7f6daf44b000 rw-p 0000c000 fd:00 2654023                    /usr/lib/x86_64-linux-gnu/libnss_files-2.31.so
7f6daf44b000-7f6daf451000 rw-p 00000000 00:00 0 
7f6daf451000-7f6daf452000 r--s 00002000 fd:00 2492120                    /usr/local/tomcat/apache-tomcat-7.0.109/lib/annotations-api.jar
7f6daf452000-7f6daf453000 r--s 0000a000 fd:00 2889006                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/ext/jaccess.jar
7f6daf453000-7f6daf45a000 r--s 000d3000 fd:00 2889029                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/jfr.jar
7f6daf45a000-7f6daf467000 r--p 00000000 fd:00 2888970                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libjava.so
7f6daf467000-7f6daf47f000 r-xp 0000d000 fd:00 2888970                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libjava.so
7f6daf47f000-7f6daf486000 r--p 00025000 fd:00 2888970                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libjava.so
7f6daf486000-7f6daf487000 r--p 0002b000 fd:00 2888970                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libjava.so
7f6daf487000-7f6daf489000 rw-p 0002c000 fd:00 2888970                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libjava.so
7f6daf489000-7f6daf48e000 r--p 00000000 fd:00 2888988                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libverify.so
7f6daf48e000-7f6daf497000 r-xp 00005000 fd:00 2888988                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libverify.so
7f6daf497000-7f6daf499000 r--p 0000e000 fd:00 2888988                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libverify.so
7f6daf499000-7f6daf49b000 r--p 0000f000 fd:00 2888988                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libverify.so
7f6daf49b000-7f6daf49c000 rw-p 00011000 fd:00 2888988                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libverify.so
7f6daf49c000-7f6daf49e000 r--p 00000000 fd:00 2654030                    /usr/lib/x86_64-linux-gnu/librt-2.31.so
7f6daf49e000-7f6daf4a2000 r-xp 00002000 fd:00 2654030                    /usr/lib/x86_64-linux-gnu/librt-2.31.so
7f6daf4a2000-7f6daf4a4000 r--p 00006000 fd:00 2654030                    /usr/lib/x86_64-linux-gnu/librt-2.31.so
7f6daf4a4000-7f6daf4a5000 r--p 00007000 fd:00 2654030                    /usr/lib/x86_64-linux-gnu/librt-2.31.so
7f6daf4a5000-7f6daf4a6000 rw-p 00008000 fd:00 2654030                    /usr/lib/x86_64-linux-gnu/librt-2.31.so
7f6daf4a6000-7f6daf4a7000 ---p 00000000 00:00 0 
7f6daf4a7000-7f6daf4aa000 ---p 00000000 00:00 0 
7f6daf4aa000-7f6daf5a7000 rw-p 00000000 00:00 0 
7f6daf5a7000-7f6daf5aa000 r--p 00000000 fd:00 2627311                    /usr/lib/x86_64-linux-gnu/libgcc_s.so.1
7f6daf5aa000-7f6daf5bc000 r-xp 00003000 fd:00 2627311                    /usr/lib/x86_64-linux-gnu/libgcc_s.so.1
7f6daf5bc000-7f6daf5c0000 r--p 00015000 fd:00 2627311                    /usr/lib/x86_64-linux-gnu/libgcc_s.so.1
7f6daf5c0000-7f6daf5c1000 r--p 00018000 fd:00 2627311                    /usr/lib/x86_64-linux-gnu/libgcc_s.so.1
7f6daf5c1000-7f6daf5c2000 rw-p 00019000 fd:00 2627311                    /usr/lib/x86_64-linux-gnu/libgcc_s.so.1
7f6daf5c2000-7f6daf5cf000 r--p 00000000 fd:00 2654017                    /usr/lib/x86_64-linux-gnu/libm-2.31.so
7f6daf5cf000-7f6daf676000 r-xp 0000d000 fd:00 2654017                    /usr/lib/x86_64-linux-gnu/libm-2.31.so
7f6daf676000-7f6daf70f000 r--p 000b4000 fd:00 2654017                    /usr/lib/x86_64-linux-gnu/libm-2.31.so
7f6daf70f000-7f6daf710000 r--p 0014c000 fd:00 2654017                    /usr/lib/x86_64-linux-gnu/libm-2.31.so
7f6daf710000-7f6daf711000 rw-p 0014d000 fd:00 2654017                    /usr/lib/x86_64-linux-gnu/libm-2.31.so
7f6daf711000-7f6daf7a7000 r--p 00000000 fd:00 2627304                    /usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.28
7f6daf7a7000-7f6daf898000 r-xp 00096000 fd:00 2627304                    /usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.28
7f6daf898000-7f6daf8e1000 r--p 00187000 fd:00 2627304                    /usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.28
7f6daf8e1000-7f6daf8e2000 ---p 001d0000 fd:00 2627304                    /usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.28
7f6daf8e2000-7f6daf8ed000 r--p 001d0000 fd:00 2627304                    /usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.28
7f6daf8ed000-7f6daf8f0000 rw-p 001db000 fd:00 2627304                    /usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.28
7f6daf8f0000-7f6daf8f3000 rw-p 00000000 00:00 0 
7f6daf8f3000-7f6dafae9000 r--p 00000000 fd:00 2888992                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/server/libjvm.so
7f6dafae9000-7f6db0453000 r-xp 001f6000 fd:00 2888992                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/server/libjvm.so
7f6db0453000-7f6db0605000 r--p 00b60000 fd:00 2888992                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/server/libjvm.so
7f6db0605000-7f6db0606000 ---p 00d12000 fd:00 2888992                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/server/libjvm.so
7f6db0606000-7f6db069c000 r--p 00d12000 fd:00 2888992                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/server/libjvm.so
7f6db069c000-7f6db06c5000 rw-p 00da8000 fd:00 2888992                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/server/libjvm.so
7f6db06c5000-7f6db06fa000 rw-p 00000000 00:00 0 
7f6db06fa000-7f6db071c000 r--p 00000000 fd:00 2654015                    /usr/lib/x86_64-linux-gnu/libc-2.31.so
7f6db071c000-7f6db0894000 r-xp 00022000 fd:00 2654015                    /usr/lib/x86_64-linux-gnu/libc-2.31.so
7f6db0894000-7f6db08e2000 r--p 0019a000 fd:00 2654015                    /usr/lib/x86_64-linux-gnu/libc-2.31.so
7f6db08e2000-7f6db08e6000 r--p 001e7000 fd:00 2654015                    /usr/lib/x86_64-linux-gnu/libc-2.31.so
7f6db08e6000-7f6db08e8000 rw-p 001eb000 fd:00 2654015                    /usr/lib/x86_64-linux-gnu/libc-2.31.so
7f6db08e8000-7f6db08ec000 rw-p 00000000 00:00 0 
7f6db08ec000-7f6db08ed000 r--p 00000000 fd:00 2654016                    /usr/lib/x86_64-linux-gnu/libdl-2.31.so
7f6db08ed000-7f6db08ef000 r-xp 00001000 fd:00 2654016                    /usr/lib/x86_64-linux-gnu/libdl-2.31.so
7f6db08ef000-7f6db08f0000 r--p 00003000 fd:00 2654016                    /usr/lib/x86_64-linux-gnu/libdl-2.31.so
7f6db08f0000-7f6db08f1000 r--p 00003000 fd:00 2654016                    /usr/lib/x86_64-linux-gnu/libdl-2.31.so
7f6db08f1000-7f6db08f2000 rw-p 00004000 fd:00 2654016                    /usr/lib/x86_64-linux-gnu/libdl-2.31.so
7f6db08f2000-7f6db08f5000 r--p 00000000 fd:00 2888956                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/jli/libjli.so
7f6db08f5000-7f6db08ff000 r-xp 00003000 fd:00 2888956                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/jli/libjli.so
7f6db08ff000-7f6db0902000 r--p 0000d000 fd:00 2888956                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/jli/libjli.so
7f6db0902000-7f6db0903000 r--p 0000f000 fd:00 2888956                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/jli/libjli.so
7f6db0903000-7f6db0904000 rw-p 00010000 fd:00 2888956                    /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/jli/libjli.so
7f6db0904000-7f6db0906000 r--p 00000000 fd:00 2632415                    /usr/lib/x86_64-linux-gnu/libz.so.1.2.11
7f6db0906000-7f6db0917000 r-xp 00002000 fd:00 2632415                    /usr/lib/x86_64-linux-gnu/libz.so.1.2.11
7f6db0917000-7f6db091d000 r--p 00013000 fd:00 2632415                    /usr/lib/x86_64-linux-gnu/libz.so.1.2.11
7f6db091d000-7f6db091e000 ---p 00019000 fd:00 2632415                    /usr/lib/x86_64-linux-gnu/libz.so.1.2.11
7f6db091e000-7f6db091f000 r--p 00019000 fd:00 2632415                    /usr/lib/x86_64-linux-gnu/libz.so.1.2.11
7f6db091f000-7f6db0920000 rw-p 0001a000 fd:00 2632415                    /usr/lib/x86_64-linux-gnu/libz.so.1.2.11
7f6db0920000-7f6db0926000 r--p 00000000 fd:00 2654028                    /usr/lib/x86_64-linux-gnu/libpthread-2.31.so
7f6db0926000-7f6db0937000 r-xp 00006000 fd:00 2654028                    /usr/lib/x86_64-linux-gnu/libpthread-2.31.so
7f6db0937000-7f6db093d000 r--p 00017000 fd:00 2654028                    /usr/lib/x86_64-linux-gnu/libpthread-2.31.so
7f6db093d000-7f6db093e000 r--p 0001c000 fd:00 2654028                    /usr/lib/x86_64-linux-gnu/libpthread-2.31.so
7f6db093e000-7f6db093f000 rw-p 0001d000 fd:00 2654028                    /usr/lib/x86_64-linux-gnu/libpthread-2.31.so
7f6db093f000-7f6db0943000 rw-p 00000000 00:00 0 
7f6db0943000-7f6db094b000 rw-s 00000000 fd:00 524328                     /tmp/hsperfdata_root/6155
7f6db094b000-7f6db094e000 rw-p 00000000 00:00 0 
7f6db094e000-7f6db094f000 r--p 00000000 fd:00 2654011                    /usr/lib/x86_64-linux-gnu/ld-2.31.so
7f6db094f000-7f6db0972000 r-xp 00001000 fd:00 2654011                    /usr/lib/x86_64-linux-gnu/ld-2.31.so
7f6db0972000-7f6db097a000 r--p 00024000 fd:00 2654011                    /usr/lib/x86_64-linux-gnu/ld-2.31.so
7f6db097a000-7f6db097b000 r--p 00000000 00:00 0 
7f6db097b000-7f6db097c000 r--p 0002c000 fd:00 2654011                    /usr/lib/x86_64-linux-gnu/ld-2.31.so
7f6db097c000-7f6db097d000 rw-p 0002d000 fd:00 2654011                    /usr/lib/x86_64-linux-gnu/ld-2.31.so
7f6db097d000-7f6db097e000 rw-p 00000000 00:00 0 
7ffcfc4d4000-7ffcfc4f5000 rw-p 00000000 00:00 0                          [stack]
7ffcfc54e000-7ffcfc551000 r--p 00000000 00:00 0                          [vvar]
7ffcfc551000-7ffcfc552000 r-xp 00000000 00:00 0                          [vdso]
ffffffffff600000-ffffffffff601000 --xp 00000000 00:00 0                  [vsyscall]
```

## pgcacher -pid 6155

```bash
root@ubuntu20:/sys/fs/cgroup/memory/myTomcat# pgcacher -pid 6155
2023/12/02 16:10:40 skipping "/root/.vscode-server/bin/1a5daa3a0231a0fbba4f14db7ec463cf99d7768e/vscode-remote-lock.root.1a5daa3a0231a0fbba4f14db7ec463cf99d7768e (deleted)": could not open file for read: open /root/.vscode-server/bin/1a5daa3a0231a0fbba4f14db7ec463cf99d7768e/vscode-remote-lock.root.1a5daa3a0231a0fbba4f14db7ec463cf99d7768e (deleted): no such file or directory
+----------------------------------------------------------------------------------+----------------+-------------+----------------+-------------+---------+
| Name                                                                             | Size           │ Pages       │ Cached Size    │ Cached Pages│ Percent │
|----------------------------------------------------------------------------------+----------------+-------------+----------------+-------------+---------|
| /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/rt.jar                                 | 62.603M        | 16027       | 17.136M        | 4387        | 27.373  |
| /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/server/libjvm.so                 | 13.816M        | 3537        | 12.430M        | 3182        | 89.963  |
| /usr/local/tomcat/apache-tomcat-7.0.109/lib/ecj-4.4.2.jar                        | 2.203M         | 565         | 2.067M         | 530         | 93.805  |
| /usr/lib/x86_64-linux-gnu/libc-2.31.so                                           | 1.936M         | 496         | 1.936M         | 496         | 100.000 |
| /usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.28                                    | 1.866M         | 478         | 1.866M         | 478         | 100.000 |
| /usr/local/tomcat/apache-tomcat-7.0.109/lib/catalina.jar                         | 1.656M         | 425         | 1.586M         | 407         | 95.765  |
| /usr/lib/x86_64-linux-gnu/libm-2.31.so                                           | 1.306M         | 335         | 1.306M         | 335         | 100.000 |
| /usr/local/tomcat/apache-tomcat-7.0.109/lib/tomcat-coyote.jar                    | 846.772K       | 212         | 846.772K       | 212         | 100.000 |
| /usr/lib/locale/locale-archive                                                   | 2.895M         | 742         | 783.153K       | 196         | 26.415  |
| /usr/local/tomcat/apache-tomcat-7.0.109/lib/jasper.jar                           | 596.760K       | 150         | 596.760K       | 150         | 100.000 |
| /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/jfr.jar                                | 868.759K       | 218         | 410.468K       | 103         | 47.248  |
| /usr/local/tomcat/apache-tomcat-7.0.109/lib/tomcat7-websocket.jar                | 225.955K       | 57          | 225.955K       | 57          | 100.000 |
| /usr/local/tomcat/apache-tomcat-7.0.109/lib/servlet-api.jar                      | 194.100K       | 49          | 194.100K       | 49          | 100.000 |
| /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/ext/cldrdata.jar                       | 3.684M         | 944         | 191.801K       | 48          | 5.085   |
| /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/ext/nashorn.jar                        | 1.940M         | 497         | 187.828K       | 47          | 9.457   |
| /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libsunec.so                      | 186.242K       | 47          | 186.242K       | 47          | 100.000 |
| /usr/lib/x86_64-linux-gnu/ld-2.31.so                                             | 187.016K       | 47          | 187.016K       | 47          | 100.000 |
| /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libjava.so                       | 183.164K       | 46          | 183.164K       | 46          | 100.000 |
| /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/jsse.jar                               | 1.834M         | 470         | 171.782K       | 43          | 9.149   |
| /usr/lib/x86_64-linux-gnu/libpthread-2.31.so                                     | 153.539K       | 39          | 153.539K       | 39          | 100.000 |
| /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/resources.jar                          | 3.367M         | 862         | 151.999K       | 38          | 4.408   |
| /usr/local/tomcat/apache-tomcat-7.0.109/lib/jasper-el.jar                        | 128.015K       | 33          | 128.015K       | 33          | 100.000 |
| /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/ext/sunjce_provider.jar                | 270.686K       | 68          | 127.381K       | 32          | 47.059  |
| /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/ext/localedata.jar                     | 1.129M         | 289         | 119.961K       | 30          | 10.381  |
| /usr/local/tomcat/apache-tomcat-7.0.109/lib/catalina-ha.jar                      | 131.298K       | 33          | 119.361K       | 30          | 90.909  |
| /usr/lib/x86_64-linux-gnu/libz.so.1.2.11                                         | 106.383K       | 27          | 106.383K       | 27          | 100.000 |
| /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libnet.so                        | 106.953K       | 27          | 106.953K       | 27          | 100.000 |
| /usr/lib/x86_64-linux-gnu/libgcc_s.so.1                                          | 102.523K       | 26          | 102.523K       | 26          | 100.000 |
| /usr/local/tomcat/apache-tomcat-7.0.109/lib/catalina-tribes.jar                  | 280.205K       | 71          | 102.609K       | 26          | 36.620  |
| /usr/local/tomcat/apache-tomcat-7.0.109/lib/tomcat-dbcp.jar                      | 256.594K       | 65          | 98.689K        | 25          | 38.462  |
| /usr/local/tomcat/apache-tomcat-7.0.109/lib/tomcat-i18n-fr.jar                   | 109.539K       | 28          | 93.891K        | 24          | 85.714  |
| /usr/local/tomcat/apache-tomcat-7.0.109/lib/tomcat-jdbc.jar                      | 138.101K       | 35          | 90.751K        | 23          | 65.714  |
| /usr/local/tomcat/apache-tomcat-7.0.109/lib/tomcat-i18n-ja.jar                   | 123.720K       | 31          | 91.792K        | 23          | 74.194  |
| /usr/local/tomcat/apache-tomcat-7.0.109/lib/tomcat-i18n-ko.jar                   | 114.954K       | 29          | 91.170K        | 23          | 79.310  |
| /usr/local/tomcat/apache-tomcat-7.0.109/lib/tomcat-i18n-es.jar                   | 90.908K        | 23          | 90.908K        | 23          | 100.000 |
| /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/ext/sunpkcs11.jar                      | 275.329K       | 69          | 91.775K        | 23          | 33.333  |
| /usr/local/tomcat/apache-tomcat-7.0.109/lib/tomcat-i18n-zh-CN.jar                | 105.679K       | 27          | 90.022K        | 23          | 85.185  |
| /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/jce.jar                                | 94.627K        | 24          | 86.741K        | 22          | 91.667  |
| /usr/local/tomcat/apache-tomcat-7.0.109/lib/jsp-api.jar                          | 86.833K        | 22          | 86.833K        | 22          | 100.000 |
| /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libnio.so                        | 79.188K        | 20          | 79.188K        | 20          | 100.000 |
| /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/ext/icedtea-sound.jar                  | 68.685K        | 18          | 68.685K        | 18          | 100.000 |
| /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libverify.so                     | 70.680K        | 18          | 70.680K        | 18          | 100.000 |
| /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/ext/zipfs.jar                          | 70.366K        | 18          | 70.366K        | 18          | 100.000 |
| /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/jli/libjli.so                    | 66.930K        | 17          | 66.930K        | 17          | 100.000 |
| /usr/local/tomcat/apache-tomcat-7.0.109/lib/tomcat-util.jar                      | 57.208K        | 15          | 57.208K        | 15          | 100.000 |
| /usr/local/tomcat/apache-tomcat-7.0.109/lib/el-api.jar                           | 56.197K        | 15          | 56.197K        | 15          | 100.000 |
| /usr/local/tomcat/apache-tomcat-7.0.109/lib/catalina-ant.jar                     | 55.515K        | 14          | 55.515K        | 14          | 100.000 |
| /usr/share/java/java-atk-wrapper.jar                                             | 52.358K        | 14          | 52.358K        | 14          | 100.000 |
| /usr/local/tomcat/apache-tomcat-7.0.109/bin/tomcat-juli.jar                      | 49.646K        | 13          | 49.646K        | 13          | 100.000 |
| /usr/lib/x86_64-linux-gnu/libnss_files-2.31.so                                   | 50.641K        | 13          | 50.641K        | 13          | 100.000 |
| /usr/local/tomcat/apache-tomcat-7.0.109/lib/tomcat-i18n-de.jar                   | 49.893K        | 13          | 49.893K        | 13          | 100.000 |
| /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libmanagement.so                 | 42.516K        | 11          | 42.516K        | 11          | 100.000 |
| /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/amd64/libzip.so                        | 42.641K        | 11          | 42.641K        | 11          | 100.000 |
| /usr/local/tomcat/apache-tomcat-7.0.109/lib/websocket-api.jar                    | 37.571K        | 10          | 37.571K        | 10          | 100.000 |
| /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/ext/sunec.jar                          | 38.044K        | 10          | 38.044K        | 10          | 100.000 |
| /usr/lib/x86_64-linux-gnu/librt-2.31.so                                          | 35.117K        | 9           | 35.117K        | 9           | 100.000 |
| /usr/local/tomcat/apache-tomcat-7.0.109/lib/tomcat-i18n-ru.jar                   | 32.500K        | 9           | 32.500K        | 9           | 100.000 |
| /tmp/hsperfdata_root/6155                                                        | 32.000K        | 8           | 32.000K        | 8           | 100.000 |
| /usr/local/tomcat/apache-tomcat-7.0.109/bin/bootstrap.jar                        | 29.214K        | 8           | 29.214K        | 8           | 100.000 |
| /usr/local/tomcat/apache-tomcat-7.0.109/bin/commons-daemon.jar                   | 24.763K        | 7           | 24.763K        | 7           | 100.000 |
| /usr/lib/x86_64-linux-gnu/libdl-2.31.so                                          | 18.406K        | 5           | 18.406K        | 5           | 100.000 |
| /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/ext/jaccess.jar                        | 43.468K        | 11          | 19.758K        | 5           | 45.455  |
| /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java                                   | 14.289K        | 4           | 14.289K        | 4           | 100.000 |
| /root/.vscode-server/data/logs/20231202T153159/remoteagent.log                   | 10.867K        | 3           | 10.867K        | 3           | 100.000 |
| /usr/local/tomcat/apache-tomcat-7.0.109/logs/catalina.2023-12-02.log             | 13.181K        | 4           | 9.885K         | 3           | 75.000  |
| /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/ext/dnsns.jar                          | 8.151K         | 3           | 8.151K         | 3           | 100.000 |
| /usr/local/tomcat/apache-tomcat-7.0.109/lib/annotations-api.jar                  | 11.679K        | 3           | 11.679K        | 3           | 100.000 |
| /usr/local/tomcat/apache-tomcat-7.0.109/lib/tomcat-api.jar                       | 7.422K         | 2           | 7.422K         | 2           | 100.000 |
| /root/.vscode-server/data/logs/20231202T153159/ptyhost.log                       | 4.325K         | 2           | 4.325K         | 2           | 100.000 |
| /usr/local/tomcat/apache-tomcat-7.0.109/logs/localhost.2023-12-02.log            | 1.615K         | 1           | 1.615K         | 1           | 100.000 |
| /usr/local/tomcat/apache-tomcat-7.0.109/logs/localhost_access_log.2023-12-02.txt | 75B            | 1           | 75B            | 1           | 100.000 |
| /root/.vscode-server/data/logs/20231202T153159/network.log                       | 0B             | 0           | 0B             | 0           | 0.000   |
| /usr/local/tomcat/apache-tomcat-7.0.109/logs/host-manager.2023-12-02.log         | 0B             | 0           | 0B             | 0           | 0.000   |
| /usr/local/tomcat/apache-tomcat-7.0.109/logs/manager.2023-12-02.log              | 0B             | 0           | 0B             | 0           | 0.000   |
|----------------------------------------------------------------------------------+----------------+-------------+----------------+-------------+---------|
│ Sum                                                                              │ 107.207M       │ 27480       │ 45.596M        │ 11702       │ 42.584  │
+----------------------------------------------------------------------------------+----------------+-------------+----------------+-------------+---------+
root@ubuntu20:/sys/fs/cgroup/memory/myTomcat# 
```
