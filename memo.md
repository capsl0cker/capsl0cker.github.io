---
layout: default
---

Here are just some notes.

## How to install&config VirtualBox on Fedora 27

#### 1. Switch to root

```bash
su -
```

#### 2. Install Fedora Repo Files

```bash
cd /etc/yum.repos.d/
wget http://download.virtualbox.org/virtualbox/rpm/fedora/virtualbox.repo
```

#### 3. Install following dependency packages

```bash
dnf install binutils gcc make patch libgomp glibc-headers glibc-devel dkms
dnf install kernel-headers-`uname -r` kernel-devel-`uname -r`
```

#### 4. Install VirtualBox

```bash
dnf install VirtualBox-5.2
/sbin/vboxconfig
```

## How to transfer files from Linux to Android

Android uses MTP(Media Transfer Protocol) protocol to transfer files.
So we need install MTP driver.  On fedora,
```bash
dnf install gvfs-mtp.x86_64
```
After that, we can find our phone from our computer.


## Use Putty to connect Hyper-V Linux VM by serial console

### 1. Run below commands in PowerShell as Administrator

```PowerShell
PS C:\Users\wangj85> Get-VMComPort -VMName wj-centos

VMName    Name    Path
------    ----    ----
wj-centos COM 1
wj-centos COM 2

//'.' means localhost, use hostname or IP for remote host
PS C:\Users\wangj85> Set-VMComPort -VMName wj-centos -Path \\.\pipe\centos -Number 1
PS C:\Users\wangj85> Get-VMComPort -VMName wj-centos
VMName    Name    Path
------    ----    ----
wj-centos COM 1   \\.\pipe\centos
wj-cetnos COM 2
```

### 2. Add kernel parameters to Linux boot parameter

```bash
console=ttyS0,115200
```

### 3. Putty setting

'Host Name(or IP address)' -> '\\.\pipe\centos'

'Connection type'          -> 'Serial'

---End---

