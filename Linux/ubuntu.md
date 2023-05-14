# 软件配置

## apt

### 源切换

> apt官方源在国内访问缓慢，需要切换到国内第三方源

1. 备份原来配置文件

   `sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak `

2. 使用第三方源配置，覆盖文件内容 

   **ubuntu 16.04 清华源**

   ```bash
   # deb cdrom:[Ubuntu 16.04 LTS Xenial Xerus - Release amd64 (20160420.1)]/ xenial main restricted
   deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial main restricted
   deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates main restricted
   deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial universe
   deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates universe
   deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial multiverse
   deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates multiverse
   deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse
   deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security main restricted
   deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security universe
   deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security multiverse
   
   ```

### 安装离线软件包

1. 下载软件包，后缀名为deb 
2. 使用dpkg命令安装软件包 `sudo dpkg -i appName.deb`
3. 安装缺少的依赖文件`  sudo apt install -f`

# 中文输入法

> 尝试很多教程都没有成功，输入法有两个框架，有让装google-pinyin 有让装sougou ，安装搜狗离线包时会报错安装失败，最后搞来搞去成功了，为了避免在这个问题上遇到问题，建议以后直接使用sougou方案，按这个方案教程尝试

1. 系统设置中，安装中文语言包（操作前保证apt源已经切换到国内源，这样速度才快）
2. 系统设置中，选择fcitx框架，使用apt 安装该框架完整软件
3. 下载sougou离线包，dpkg 命令安装， apt install -f 修改依赖
4. 系统设置，text-entry 选择sougou
5. fcitx-config中选择搜狗输入法
6. 将输入法放在第二优先级

# samba

# nfs

