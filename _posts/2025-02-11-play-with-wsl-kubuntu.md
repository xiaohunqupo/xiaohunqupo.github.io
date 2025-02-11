---
title: 使用 WSL 安装 KUbuntu

tags: WSL, Linux, kubuntu

---

# 前言
手头的工作是需要做一下linux移植工作，突然想到以前总是用虚拟机安装Linux，从来没有尝试过WSL。

那就着手试试吧！

# 安装 wsl
我这里简单写了，一般也可以顺利的安装。

详细的过程，可以参考文章[WSL2安装和基本命令](https://blog.csdn.net/code_peak/article/details/128541617?ops_request_misc=&request_id=&biz_id=102&utm_term=wsl2%E5%AE%89%E8%A3%85&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-1-128541617.142%5Ev96%5Epc_search_result_base6&spm=1018.2226.3001.4187)

## 先安装 wsl 工具
命令行中执行以下命令

```
wsl --install
```
安装后重启系统。

## 安装子系统
子系统有两种方式安装

### 命令行
```
# 查看可用linux版本
wsl --list --online

# 安装指定的linux版本
wsl --install -d <DistroName>
```

### 商店安装
在 `Windows Store` 中，选择子系统安装。

## 注意
必须启用以下Windows功能

1. Hyper-V
2. Virtual Machine Platform
3. 适用于 Linux 的 Windows 子系统

# 安装桌面
我选用的是 `KDE` 版本的UBUNTU，参考了文章[WSL2 Ubuntu22.04安装KDE桌面](https://blog.csdn.net/qq_30448087/article/details/134897586)

## 安装命令
使用`apt`安装`KDE`桌面如下所示:
```
# 可以安装kubuntu-desktop或plasma-desktop，区别是前者更完整体积更大后者提供基本的桌面组件体积更小
sudo apt install kubuntu-desktop
```

## 注意1
安装桌面过程中遇到如下提示:
```
Initializing mlocate database; this may take some time
```
按照参考文章所说，这个过程其实是对配置中的目录进行全搜索，如果硬盘大会特别慢，2T据说要搜索6天。

这个时候不要急，我们可以调整搜索目录范围。

首先我们使用键盘快捷键 `CTRL`+`Z`，暂停桌面的安装进程（不用kill掉）。

你会看到进度条还在下方，但是此时可以输入命令了，输入如下命令：

```
# 使用 VIM 工具打开文件
sudo vim /etc/updatedb.conf
```

VIM工具进入后是命令状态，输入 `I`回车，进入编辑状态。

在 `"PRUNEPATHS:"` 变量后添加 `"/mnt"`。

编辑后，按`ESC键`返回命令状态，输入 `:wq` ，可以保存退出。

再次执行以下命令：
```
# jobs命令用于查看当前执行的任务，按理说此时应该只有一个安装进程
jobs

# fg命令用于返回之前暂停的任务，并继续执行，如果只有一个进程就正常输入
fg

```
执行`fg`命令后请等待，这时不会有屏幕刷新，但是后台其实正在执行，差不多1小时左右即可完成。

## 注意2
按照参考文章说，有人安装时候，需要执行下面的语句，但是我没遇到这个问题。
```
apt purge -y acpid acpi-support modemmanager
apt-mark hold acpid acpi-support modemmanager
```

# 安装 X Server
## 安装xorg
`xorg`是显示服务器，实现了 `X Window` 系统的功能，主要负责管理计算机的显示设备和用户输入设备（如键盘和鼠标）。

执行命令：
```
sudo apt-get install xorg
```
## 安装Xehpyr
Xephyr 是一个基于 `KDrive` 的嵌套 `X Window`，它允许在一个现有的 `X Window` 内部创建一个嵌套的 `X Window`，并在一个窗口中显示。

执行命令：
```
sudo apt-get install xserver-xephyr
```
## 设置环境变量
执行命令：
```
echo "export XDG_SESSION_TYPE=x11" >> ~/.profile
echo "export GDK_PLATFORM=x11" >> ~/.profile
echo "export GDK_BACKEND=x11" >> ~/.profile
echo "export QT_QPA_PLATFORM=xcb" >> ~/.profile
echo "export WAYLAND_DISPLAY=" >> ~/.profile
source ~/.profile
# 最后这一句，原作的原话如下:
# “儲存環境變數到~/.profile，告訴程式全數使用X渲染。如果你不這樣做，在X伺服器視窗開的Linux程式可能會跑到視窗外面。”
```

## 注意
如果安装过程中，出现无法安装，有可能是因为apt较低或相关依赖库版本较低，可先尝试以下语句：
```
sudo apt update
sudo apt upgrade
```

# 启动X Server
执行下面语句启动 X Server
```
# 设置窗口可以改变大小，至于1920x1080是我笔记本屏幕的大小，大家可以根据需求自行修改
Xephyr -br -ac -noreset -resizeable -screen 1920x1080 :1 &
#上面这条命令最后的“&”是指异步运行，这里需要异步运行后面还有命令需要输入
```

如果出现类似下面的提示信息
```
Please change 1777 /tmp/.X11-unix.
```

可以执行以下语句：
```
# 解除文件挂在，避免出现权限无法修改的情况
sudo mount -o remount,rw /tmp/.X11-unix
# 调整文件夹权限
sudo chmod 1777 /tmp/.X11-unix
```

# 启动桌面
执行下面的语句需要启动桌面:
```
export DISPLAY=:1
dbus-launch --exit-with-session startplasma-x11 &
```

需要注意，桌面的显示依托于 `X Server`，所以必须在 `X Server` 启动后，才能正常启动桌面。