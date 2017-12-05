---
layout: default
---

Here are just some notes.

#How to install&config VirtualBox on Fedora 27

1. Switch to root

```shell
su -
```shell

2. Install Fedora Repo Files

```bash
cd /etc/yum.repos.d/
wget http://download.virtualbox.org/virtualbox/rpm/fedora/virtualbox.repo
```bash

3. Install following dependency packages

```bash
dnf install binutils gcc make patch libgomp glibc-headers glibc-devel dkms
dnf install kernel-headers-`uname -r` kernel-devel-`uname -r`
```bash

4. Install VirtualBox

```bash
dnf install VirtualBox-5.2
/sbin/vboxconfig
```bash

--*done*--

