# Arch Linux 常用软件配置


记录一下安装 `Arch Linux` 后的常用软件安装以及配置。

## `yay`
使用 `yay` 从 `AUR` 上下载各种官方仓库所没有的包。

项目主页：<https://github.com/Jguer/yay>。

从源码安装：

```sh
$ pacman -S --needed git base-devel
$ git clone https://aur.archlinux.org/yay.git
$ cd yay
$ makepkg -si
```

或者下载二进制包：

```sh
$ pacman -S --needed git base-devel
$ git clone https://aur.archlinux.org/yay-bin.git
$ cd yay-bin
$ makepkg -si
```

用法与 `pacman` 一致。

## `debtap`
`debtap` 是 `AUR` 包，用于将 `deb` 包转换为 `pacman` 可以使用的包。

安装：

```sh
$ yay -S debtap
```

安装后可以编辑 `/usr/bin/debtap`，替换所有的 `http://ftp.debian.org/debian/dists` 为 `https://mirrors.ustc.edu.cn/debian/dists`，替换所有的 `http://archive.ubuntu.com/ubuntu/dists` 为 `https://mirrors.ustc.edu.cn/ubuntu/dists/`。

使用：
```sh
$ sudo debtap -u # 更新源列表
$ debtap -q package.deb # 转换 deb 包，-q 表示不要编辑除元数据之外的信息
$ sudo pacman -U package.tar.xz # 安装生成的包
```

## `clementine`
一个跨平台的音乐播放器，不仅可以播放各种格式的音乐，还支持提取歌曲的元信息。

安装：

```sh
$ sudo pacman -S clementine
$ sudo pacman -S gst-plugins-good gst-plugins-base \
    gst-libav gst-plugins-bad gst-plugins-ugly
```

由于 `clementine` 使用了 `GStreamer`，所以还要安装必要的插件，否则无法播放音乐。

## `wireshark`
基于 `Qt` 编写的开源抓包工具。

可以编译源码，也可以直接用 `pacman` 安装：

```sh
$ sudo pacman -S wireshark
```

默认情况下只能使用 `root` 用户运行才可以访问网卡等设备，通过修改用户组设置使得常用用户可以直接使用：

```sh
$ sudo groupadd wireshark # 新建一个专用的用户组
$ sudo chgrp wireshark /usr/bin/dumpcap # 将dumpcap更改为wireshark用户组
$ sudo chmod 4755 /usr/bin/dumpcap # 4 表示执行时用户可以与所有者有相同权限
$ sudo gpasswd -a ctj12461 wireshark # 添加自己
```

附 `wireshark` 过滤出音乐的 `HTTP request` 的 `pattern`：

```
(tcp.port == 80 || udp.port == 80) && (http.request.uri contains "mp3" || http.request.uri contains "m4a" || http.request.uri contains "mp4" || http.request.uri contains "flac" || http.request.uri contains "ogg")
```

## `VS Code`
如果直接用包管理器安装 `code`，则会安装 `Code - OSS`，这个虽然也是 `VS Code`，但在协议上与 `Microsoft` 提供的 `Visual Studio Code` 不同，所以所带有的内容也有所差别，比如无法登陆 `Microsoft` 帐号，无法同步设置等。

如果有需要，可以用 `yay` 安装 `visual-studio-code-bin`：

```sh
$ yay -S visual-studio-code-bin
```

也是直接输入 `code` 运行。

## `WPS 2019 for Linux`
目前 `WPS` 对 `Microsoft Office` 的支持是最好的，而且还在稳定更新，推荐使用。

使用 `yay` 安装，并且要安装可选的依赖包：

```sh
$ yay -S wps-office-cn wps-office-mime-cn wps-office-mui-cn # 安装中文环境的 WPS
$ yay -S ttf-wps-fonts wps-office-fonts # 安装字体
```

如果使用 `KDE`，可能会遇到字体模糊的情况，这是由缩放不为 100% 引起的问题，`WPS` 使用了 `Qt` 所以只要在运行前加上环境变量 `QT_SCREEN_SCALE_FACTORS=1` 即可，对于启动器或桌面上的 `Desktop Entry`（快捷方式），只要在 `/usr/share/applications` 下修改所有含 `wps` 的 `Desktop Entry` 文件即可，安装下面修改即可：

```sh
# /usr/share/applications/wps-office-wps.desktop
Exec=env QT_SCREEN_SCALE_FACTORS=1 /usr/bin/wps %U
```

## `fcitx 5`
`fcitx 5` 使用简单，比较推荐。

安装：

```sh
$ sudo pacman -S fcitx5-im fcitx5-configtool fcitx5-chinese-addons fcitx5-rime
```

添加环境变量以正常使用 `fcitx5`：

```sh
GTK_IM_MODULE=fcitx
QT_IM_MODULE=fcitx
XMODIFIERS=@im=fcitx
INPUT_METHOD=fcitx
SDL_IM_MODULE=fcitx
GLFW_IM_MODULE=ibus
```

如果无法开机启动，则执以下命令：

```sh
# 通过在 autostart 目录下添加启动项
$ cp /usr/share/applications/org.fcitx.Fcitx5.desktop ~/.config/autostart/
```

词库安装，可以自己选择：

```sh
$ sudo pacman -S fcitx5-pinyin-zhwiki
$ yay -S fcitx5-pinyin-sougou
$ yay -S fcitx5-pinyin-zhwiki-rime
$ yay -S fcitx5-pinyin-moegirl-rime
```

解决中文下按 `[` 和 `]` 输出为其他符号：编辑 `/usr/share/fcitx5/punctuation/punc.mb.zh_CN`，把 `[` 和 `]` 映射的字符修改为 `【` 和 `】`。
