# Ubuntu使用

必读：

一般安装完ubuntu之后，我们还需要进行一些操作，才能流畅的使用

(1) 安装open-vm-tools：主要是解决虚拟机和主机之间的复制粘贴问题，否则我们无法把网上找到的一些命令复制粘贴过来，纯手敲不现实

(2) 换源，我们之后需要安装很多软件包，如果使用官方提供的源速度太慢了，所以需要换源，提高速度

(3) 安装ssh相关服务，之后的开发我们一般都使用第三方软件，远程连接，会使用到ssh服务，所以我们要在ubuntu上下载ssh相关的服务，然后开启





## 安装open-vm-tools



一般虚拟机安装ubuntu系统后，需要安装open-vm-tools

参考：[https://linuxconfig.org/install-vmware-tools-on-ubuntu-22-04-jammy-jellyfish-linux](https://linuxconfig.org/install-vmware-tools-on-ubuntu-22-04-jammy-jellyfish-linux)

```
# UBUNTU 22.04 SERVER:
$ sudo apt install open-vm-tools

# UBUNTU 22.04 DESKTOP:
$ sudo apt install open-vm-tools-desktop open-vm-tools

$ reboot

$ dpkg -l | grep vm-tools
```



在 Ubuntu 22.04 中安装 `open-vm-tools` 的原因主要有以下几点：

#### 1. **增强虚拟机性能**

`open-vm-tools` 是 VMware 提供的开源工具集，旨在优化虚拟机的性能。安装它可以提高虚拟机的运行效率和响应速度。

#### 2. **改善用户体验**

安装 `open-vm-tools` 后，您可以获得更好的用户体验，包括：

* **自动调整屏幕分辨率**：根据虚拟机窗口的大小自动调整分辨率。
* **共享剪贴板**：允许在主机和虚拟机之间复制和粘贴文本。
* **拖放文件**：支持在主机和虚拟机之间拖放文件。

#### 3. **简化管理**

`open-vm-tools` 提供了一些管理功能，使得虚拟机的管理更加简单。例如：

* **自动启动和关闭**：可以配置虚拟机在主机启动或关闭时自动启动或关闭。
* **快照管理**：更好地支持快照功能。

#### 4. **提高安全性**

通过使用 `open-vm-tools`，您可以确保虚拟机与 VMware 环境的兼容性和安全性，减少潜在的安全风险。

#### 5. **开源和社区支持**

`open-vm-tools` 是开源的，意味着您可以获得社区的支持和更新，确保工具的持续改进和安全性。

#### 安装方法

在 Ubuntu 22.04 中，您可以通过以下命令安装 `open-vm-tools`：

```bash
bashsudo apt update  
sudo apt install open-vm-tools open-vm-tools-desktop  
```

* `open-vm-tools`：基本的工具集。
* `open-vm-tools-desktop`：如果您使用的是桌面环境，建议安装此包以获得更好的用户体验。

#### 总结

安装 `open-vm-tools` 可以显著提升在 VMware 虚拟机中运行 Ubuntu 的性能和用户体验，是在虚拟化环境中运行 Ubuntu 的推荐做法。







## ubuntu换源

### ubuntu22.04换源

参考：[https://blog.csdn.net/xiangxianghehe/article/details/122856771](https://blog.csdn.net/xiangxianghehe/article/details/122856771)

最后我选择使用网易云的源，速度比较快



在 Ubuntu 22.04 中更换软件源可以提高软件包下载速度，尤其是在某些地区。以下是更换源的步骤：

#### 1. 备份当前源列表

在更改源之前，建议备份当前的源列表，以防需要恢复。

```bash
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak  
```

#### 2. 编辑源列表

使用文本编辑器打开 `/etc/apt/sources.list` 文件。您可以使用 `nano` 或 `vim` 等编辑器。

```bash
sudo nano /etc/apt/sources.list  
```

#### 3. 更换源

在文件中，您会看到类似以下的行：

```plaintext
deb http://archive.ubuntu.com/ubuntu/ jammy main restricted  
deb http://archive.ubuntu.com/ubuntu/ jammy-updates main restricted  
deb http://archive.ubuntu.com/ubuntu/ jammy universe  
deb http://archive.ubuntu.com/ubuntu/ jammy-updates universe  
deb http://archive.ubuntu.com/ubuntu/ jammy multiverse  
deb http://archive.ubuntu.com/ubuntu/ jammy-updates multiverse  
deb http://archive.ubuntu.com/ubuntu/ jammy-backports main restricted universe multiverse  
deb http://security.ubuntu.com/ubuntu jammy-security main restricted  
deb http://security.ubuntu.com/ubuntu jammy-security universe  
deb http://security.ubuntu.com/ubuntu jammy-security multiverse  
```

您可以将这些行替换为其他镜像源。例如，使用清华大学的镜像源：

```plaintext
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy main restricted universe multiverse  
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-updates main restricted universe multiverse  
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-backports main restricted universe multiverse  
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-security main restricted universe multiverse  
```

#### 4. 保存并退出

如果您使用 `nano`，可以按 `CTRL + O` 保存文件，然后按 `CTRL + X` 退出。

#### 5. 更新软件包列表

更换源后，您需要更新软件包列表：

```bash
sudo apt update  
```

#### 6. 升级软件包（可选）

如果需要，您可以升级已安装的软件包：

```bash
sudo apt upgrade  
```

#### 总结

通过以上步骤，您可以轻松更换 Ubuntu 22.04 的软件源，以提高软件包下载速度和稳定性。选择一个离您较近的镜像源通常会带来更好的体验。





## ubuntu安装ssh服务

```bash
sudo systemctl status ssh  # 查看ssh状态，判断是否安装

# 安装ssh相关服务
sudo apt install openssh-server
sudo apt install openssh-client

# 启动ssh服务
sudo systemctl start ssh
```



在 Ubuntu 中，您可以通过以下步骤检测 SSH 相关服务是否已安装，并在未安装的情况下进行安装和启动：

#### 1. 检查 SSH 是否已安装

您可以使用以下命令检查 SSH 客户端和服务器是否已安装：

```bash
dpkg -l | grep openssh  
```

* 如果输出中包含 `openssh-client` 和 `openssh-server`，则说明 SSH 已安装。
* 如果没有输出，说明 SSH 可能未安装。

#### 2. 安装 SSH

如果 SSH 未安装，您可以使用以下命令安装 OpenSSH 服务器：

```bash
sudo apt update  
sudo apt install openssh-server  
```

#### 3. 启动 SSH 服务

安装完成后，您可以启动 SSH 服务：

```bash
sudo systemctl start ssh  
```

#### 4. 设置 SSH 服务开机自启

为了确保 SSH 服务在系统启动时自动启动，您可以使用以下命令：

```bash
sudo systemctl enable ssh  
```

#### 5. 检查 SSH 服务状态

最后，您可以检查 SSH 服务的状态以确保其正常运行：

```bash
sudo systemctl status ssh  
```

如果 SSH 服务正在运行，您应该会看到类似于 `active (running)` 的状态信息。

通过这些步骤，您可以确保在 Ubuntu 上安装并启动 SSH 服务。
