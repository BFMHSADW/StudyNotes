# Fedora食用本

>本文档记录了本人食用Fedora的过程和一些个人经验分享，不构成教学和指导

---

## 下载镜像与制作U盘启动盘

Fedora是一个由Red Hat赞助的社区驱动的Linux发行版，旨在提供最新的开源软件和技术。它是一个稳定、可靠且易于使用的操作系统，适合开发者、系统管理员和普通用户使用。

下载地址：<https://fedoraproject.org>

## 初见

安装完成好的fedora

```bash
sudo dnf update    //更新已添加的源
sudo dnf upgrade    //安装更新包
```

修改主机名

```bash
sudo hostnamectl set-hostname <hostname>
```

## 更换shell

fish是一个简单好上手的交互式shell，具有友好的用户界面和丰富的功能。

安装fish

```bash
sudo dnf install
```

查看fish的安装路径

```bash
which fish
```

修改默认shell为fish

```bash
chsh -s /usr/bin/fish
```

重启终端

可选：如果想恢复默认shell，执行以下命令

```bash
chsh -s /bin/bash
```

## 配置中文输入法

雾凇拼音是中州韵输入法中比较好用的方案，kde桌面的推荐Fcitx5，ibus与Gnome桌面适配性更高，体验更好

### ibus

```bash
sudo dnf install ibus ibus-rime librime-lua
```

``ibus``是ibus的基础框架

``ibus-rime``是rime输入法的基础框架

``librime-lua``是雾凇拼音的必备依赖

#### 配置

```bash
ibus-setup
```

### Fcitx5

Fcitx5默认的pinyin有很多不方便的地方，这里推荐使用rime输入法框架，采用雾凇拼音配置方案

### 可选：快照备份与回档

```bash
sudo dnf install snapper dnf-plugin-snapper btrfs-assistant inotify-tools
sudo dnf install btrfs-progs
```

``snapper`` 是一个用于管理Btrfs文件系统快照的工具，可帮助用户轻松创建、管理和恢复系统快照。

``dnf-plugin-snapper``是snapper在dnf包管理器中的插件，用于在使用dnf进行系统更新时自动创建快照。

``btrfs-assistant``是一个用于辅助管理Btrfs文件系统的图形化工具，提供了更直观的操作界面。

``inotify-tools``是一个用于监控文件系统事件的工具，可与snapper配合使用，在文件系统发生特定变化时触发快照创建。

``btrfs-progs``是一组用于管理Btrfs文件系统的实用程序，提供了创建、检查、修复和调整Btrfs文件系统等功能，Fedora会预装，缺少可以不装，只利用snapper就可以来完成快照的创建、管理和恢复工作。

## 安装基础工具

Flatpak

```bash
sudo dnf install flatpak    //一般默认会安装，如果没有手动安装一下
```

dnfdragora

```bash
sudo dnf install dnfdragora
```

``dnfdragora``是dnf的gui管理工具。

### 安装visual studio code

visual studio code 的安装方式有很多种，我这边使用的是官方文档里Fedora的安装方式,也可以直接从官网下载rpm包，然后进行安装。

通过添加微软官方库进行安装VSCode

```bash
sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc &&
echo -e "[code]\nname=Visual Studio Code\nbaseurl=https://packages.microsoft.com/yumrepos/vscode\nenabled=1\nautorefresh=1\ntype=rpm-md\ngpgcheck=1\ngpgkey=https://packages.microsoft.com/keys/microsoft.asc" | sudo tee /etc/yum.repos.d/vscode.repo > /dev/null
```

```bash
dnf check-update &&
sudo dnf install code # or code-insiders
```

通过rpm包安装VSCode

官网下载地址：<https://code.visualstudio.com/download>

```bash
sudo dnf install ./
```

## 安装其他桌面环境

Niri

```bash
sudo dnf copr enable avengemedia/dms
sudo dnf install niri dms
systemctl --user add-wants niri.service dms
```

dnf命令

```bash
# 软件管理命令
Software Management Commands:
  do                                     Do transaction                                   # 执行事务
  install                                Install software                                 # 安装软件
  upgrade                                Upgrade software                                 # 升级软件
  remove                                 Remove (uninstall) software                      # 移除（卸载）软件
  distro-sync                            Upgrade or downgrade installed software to the latest available versions  # 将已安装软件升级或降级到最新可用版本
  downgrade                              Downgrade software                               # 降级软件
  reinstall                              Reinstall software                               # 重新安装软件
  debuginfo-install                      Install debuginfo packages.                      # 安装调试信息包
  swap                                   Remove software and install another in one transaction  # 在一次事务中移除软件并安装另一个
  mark                                   Change the reason of an installed package        # 更改已安装包的安装原因
  autoremove                             Remove all unneeded packages originally installed as dependencies.  # 移除所有最初作为依赖安装的不需要的包
  provides                               Find what package provides the given value       # 查找哪个包提供了给定的值
  replay                                 Replay a transaction that was previously stored to a directory  # 重放之前存储到目录中的事务
  check-upgrade                          Check for available package upgrades             # 检查可用的软件包升级
  check                                  Check for problems in the packagedb              # 检查包数据库中的问题

# 查询命令
Query Commands:
  leaves                                 List groups of installed packages not required by other installed packages  # 列出不被其他已安装包需要的已安装包组
  repoquery                              Search for packages matching various criteria    # 搜索符合各种条件的软件包
  search                                 Search for software matching all specified strings  # 搜索匹配所有指定字符串的软件
  list                                   Lists packages depending on the packages' relation to the system  # 根据包与系统的关系列出包
  info                                   Lists packages depending on the packages' relation to the system with additional details  # 根据包与系统的关系列出包，并提供额外详细信息

# 子命令
Subcommands:
  group                                  Manage comps groups                              # 管理comps组
  environment                            Manage comps environments                        # 管理comps环境
  module                                 Manage modules                                   # 管理模块
  history                                Manage transaction history                       # 管理事务历史
  repo                                   Manage repositories                              # 管理软件仓库
  advisory                               Manage advisories                                # 管理安全公告
  versionlock                            Manage versionlock configuration                 # 管理版本锁定配置
  system-upgrade                         Prepare system for upgrade to a new release      # 准备系统升级到新版本
  offline-distrosync                     Store a distro-sync transaction to be performed offline  # 存储要离线执行的distro-sync事务
  offline-upgrade                        Store an upgrade transaction to be performed offline  # 存储要离线执行的升级事务
  offline                                Manage offline transactions                      # 管理离线事务
  config-manager                         Manage configuration                             # 管理配置

# 兼容性别名
Compatibility Aliases:
  check-update                           Alias for 'check-upgrade'                        # 'check-upgrade' 的别名
  dg                                     Alias for 'downgrade'                            # 'downgrade' 的别名
  dsync                                  Alias for 'distro-sync'                          # 'distro-sync' 的别名
  grp                                    Alias for 'group'                                # 'group' 的别名
  if                                     Alias for 'info'                                 # 'info' 的别名
  in                                     Alias for 'install'                              # 'install' 的别名
  ls                                     Alias for 'list'                                 # 'list' 的别名
  mc                                     Alias for 'makecache'                            # 'makecache' 的别名
  rei                                    Alias for 'reinstall'                            # 'reinstall' 的别名
  repoinfo                               Alias for 'repo info'                            # 'repo info' 的别名
  repolist                               Alias for 'repo list'                            # 'repo list' 的别名
  rm                                     Alias for 'remove'                               # 'remove' 的别名
  rq                                     Alias for 'repoquery'                            # 'repoquery' 的别名
  se                                     Alias for 'search'                               # 'search' 的别名
  up                                     Alias for 'upgrade'                              # 'upgrade' 的别名
  update                                 Alias for 'upgrade'                              # 'upgrade' 的别名
  updateinfo                             Alias for 'advisory'                             # 'advisory' 的别名
  upgrade-minimal                        Alias for 'upgrade --minimal'                    # 'upgrade --minimal' 的别名

# 其他命令
Commands:
  clean                                  Remove or expire cached data                     # 移除或过期缓存数据
  download                               Download software to the current directory       # 将软件下载到当前目录
  makecache                              Generate the metadata cache                      # 生成元数据缓存
  builddep                               Install build dependencies for package or spec file  # 安装包或spec文件的构建依赖
  changelog                              Show package changelogs                          # 显示软件包变更日志
  copr                                   Manage Copr repositories (add-ons provided by users/community/third-party)  # 管理Copr仓库（用户/社区/第三方提供的附加组件）
  needs-restarting                       Determine whether system or systemd services need restarting  # 确定系统或systemd服务是否需要重启
  repoclosure                            Print list of unresolved dependencies for repositories  # 打印仓库中未解决的依赖项列表
  repomanage                             Manage a directory with repodata or with rpm packages  # 管理包含repodata或rpm包的目录
  reposync                               Synchronize a remote DNF repository to a local directory.  # 将远程DNF仓库同步到本地目录
  build-dep                              Compatibility alias for 'builddep'               # 'builddep' 的兼容性别名

# 全局选项
Global options:
  -h, --help                             Print help                                       # 打印帮助信息
  --config=CONFIG_FILE_PATH              Configuration file location                      # 配置文件位置
  -q, --quiet                            In combination with a non-interactive command, shows just the relevant content. Suppresses messages notifying about the current state or actions of dnf5.  # 与非交互式命令结合使用时，仅显示相关内容。抑制通知dnf5当前状态或操作的消息
  -C, --cacheonly                        Run entirely from system cache, don't update the cache and use it even in case it is expired.  # 完全从系统缓存运行，不更新缓存，即使缓存过期也使用
  --color=COLOR                          Control whether color is used.                   # 控制是否使用颜色
  --refresh                              Force refreshing metadata before running the command.  # 在运行命令前强制刷新元数据
  --repofrompath=REPO_ID,REPO_PATH       create additional repository using id and path   # 使用id和路径创建额外的仓库
  --setopt=[REPO_ID.]OPTION=VALUE        set arbitrary config and repo options            # 设置任意配置和仓库选项
  --setvar=VAR_NAME=VALUE                set arbitrary variable                           # 设置任意变量
  -y, --assumeyes                        automatically answer yes for all questions       # 对所有问题自动回答yes
  --assumeno                             automatically answer no for all questions        # 对所有问题自动回答no
  --best                                 try the best available package versions in transactions  # 在事务中尝试最佳可用包版本
  --no-best                              do not limit the transaction to the best candidate  # 不将事务限制为最佳候选
  --no-docs                              Don't install files that are marked as documentation (which includes man pages and texinfo documents)  # 不安装标记为文档的文件（包括man页和texinfo文档）
  -x package,..., --exclude=package,...  exclude packages by name or glob                 # 按名称或通配符排除包
  --enable-repo=REPO_ID,...              Enable additional repositories. List option. Supports globs, can be specified multiple times.  # 启用额外仓库。列表选项，支持通配符，可多次指定
  --disable-repo=REPO_ID,...             Disable repositories. List option. Supports globs, can be specified multiple times.  # 禁用仓库。列表选项，支持通配符，可多次指定
  --repo=REPO_ID,...                     Enable just specific repositories. List option. Supports globs, can be specified multiple times.  # 仅启用特定仓库。列表选项，支持通配符，可多次指定
  --no-gpgchecks                         disable OpenPGP signature checking (if RPM policy allows)  # 禁用OpenPGP签名检查（如果RPM策略允许）
  --no-plugins                           Disable all libdnf5 plugins                      # 禁用所有libdnf5插件
  --enable-plugin=PLUGIN_NAME,...        Enable libdnf5 plugins by name. List option. Supports globs, can be specified multiple times.  # 按名称启用libdnf5插件。列表选项，支持通配符，可多次指定
  --disable-plugin=PLUGIN_NAME,...       Disable libdnf5 plugins by name. List option. Supports globs, can be specified multiple times.  # 按名称禁用libdnf5插件。列表选项，支持通配符，可多次指定
  --comment=COMMENT                      add a comment to transaction                     # 向事务添加注释
  --installroot=ABSOLUTE_PATH            set install root                                 # 设置安装根目录
  --use-host-config                      use configuration, reposdir, and vars from the host system rather than the installroot  # 使用主机系统的配置、仓库目录和变量，而非安装根目录中的
  --releasever=RELEASEVER                override the value of $releasever in config and repo files  # 覆盖配置和仓库文件中$releasever的值
  --releasever-major=RELEASEVER_MAJOR    override the value of $releasever_major in config and repo files  # 覆盖配置和仓库文件中$releasever_major的值
  --releasever-minor=RELEASEVER_MINOR    override the value of $releasever_minor in config and repo files  # 覆盖配置和仓库文件中$releasever_minor的值
  --show-new-leaves                      Show newly installed leaf packages and packages that became leaves after a transaction.  # 显示新安装的叶子包和事务后变为叶子包的包
  --debugsolver                          Dump detailed solving results into files         # 将详细的依赖解决结果转储到文件
  --dump-main-config                     Print main configuration values to stdout        # 将主配置值打印到标准输出
  --dump-repo-config=REPO_ID,...         Print repository configuration values to stdout. List option. Supports globs  # 将仓库配置值打印到标准输出。列表选项，支持通配符
  --dump-variables                       Print variable values to stdout                  # 将变量值打印到标准输出
  --version                              Show DNF5 version and exit                      # 显示DNF5版本并退出
  --forcearch=FORCEARCH                  Force the use of a different architecture.       # 强制使用不同的架构
  --skip-file-locks                      Skip acquiring file locks, such as the lock on the system repository  # 跳过获取文件锁，例如系统仓库的锁

# 选项兼容性别名
Options Compatibility aliases:
  -c CONFIG_FILE_PATH                    Alias for '--config'                             # '--config' 的别名
  --nobest                               Alias for '--no-best'                            # '--no-best' 的别名
  --nodocs                               Alias for '--no-docs'                            # '--no-docs' 的别名
  --enablerepo=REPO_ID,...               Alias for '--enable-repo'                        # '--enable-repo' 的别名
  --disablerepo=REPO_ID,...              Alias for '--disable-repo'                       # '--disable-repo' 的别名
  --repoid=REPO_ID,...                   Alias for '--repo'                               # '--repo' 的别名
  --nogpgcheck                           Alias for '--no-gpgchecks'                       # '--no-gpgchecks' 的别名
  --noplugins                            Alias for '--no-plugins'                         # '--no-plugins' 的别名
  --enableplugin=PLUGIN_NAME,...         Alias for '--enable-plugin'                      # '--enable-plugin' 的别名
  --disableplugin=PLUGIN_NAME,...        Alias for '--disable-plugin'                     # '--disable-plugin' 的别名
```
