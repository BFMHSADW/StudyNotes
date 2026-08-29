# Flatpak

## 安装Flatpak

- 安装flatpak，有些发行版默认安装好了flatpak

```bash
sudo dnf in flatpak  //用你的发行版的安装命令安装，这里列出的是Fedora，Ubuntu是apt，Archlinux是pacman
```

## 添加官方库以及替换国内源

- 国内因为某些原因，直接连接官方源服务器有点慢，所以需要替换国内的镜像源

```bash
flatpak remotes --show-details  //显示目前库的详细信息

flatpak list  //

flatpak install

flatpak uninstall

flatpak run

flatpak update

flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo

flatpak remote-modify flathub --url=https://mirrors.ustc.edu.cn/flathub

flatpak install flathub com.mattjakeman.ExtensionManager
```
