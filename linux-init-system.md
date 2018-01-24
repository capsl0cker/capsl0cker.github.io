---
layout: default
---

Linux Init System

Currently, there are three different init systems in Linux:
sysvinit, upstart, systemd.

## 1. Sysvinit

1. Configuration scripts are located in /etc/rc.d/init.d/.

2. Usually, each script has three functions: start, stop, and restart.

3. System has different run levels, ususally, 0-6 and S; And different scripts
should run on different levels.

4. Usually, we create one fold for each run level under /etc/rc.d/,
e.g. /etc/rc.d/rc0.d, /etc/rc.d/rc1.d, ..., /etc/rc.d/rc6.d.

5. We create various symbol links in /etc/rc.d/rcX.d which refers to scripts in /etc/rc.d/init.d.
Each link represents that this script will be run at this level when system boot up.

6. Command 'chkconfig' can be used to build such symbol link on RH/Fedora/CentOS.
Command 'update-rc.d' does the same thing for Debian/Ubuntu.

7. System default run level is set in /etc/inittab.


## 2. Upstart

Digests from [Upstart Getting Started](http://upstart.ubuntu.com/getting-started.html)

1. Upstart Jobs(scripts) are located in /etc/init/ , and have .conf extension.
And they are plain text files and are executable.

2. All jobs files must have either an _exec_ or _script_ stanza.
They specifies what will be run for the job.

3. Additional shell code can be given to be run **before** or **after** the job specified
with _exec_ or _script_. These are not expected to start the process, in fact, they are
intended for preparing the environment and cleaning up afterwards.

4. Jobs can be started and stopped manually. However, they can be done automatically when events
are emitted.

5. The primary event emitted by upstart is **startup** which is the machine is first started.
For sysvinit, we can have **runlevel X** events, where X is one of 0-6 or S.
Other jobs can also generate events as they are run. **stopped job** is used when another job stops.
**started job** is used when another job starts.

6. You can change the settings of jobs output and input use **console**.

7. Use command **start** or **stop** to start or stop job. Use **status** to query job status.
Use **initctl list** to get a list of all jobs and their states.

8. A custom event can be emitted by command **initctl emit**, any jobs started  or stopped by this
event will be affected.


## 3. [systemd](https://www.freedesktop.org/wiki/Software/systemd)

Digests from [Introduction to systemd](http://lea-linux.org/documentations/Systemd).

1. The configuration of the services is by default in _/lib/systemd/system_ or
_/usr/lib/systemd/system_. It's a good practice to store custom configurations in
_/etc/systemd/system_ because they won't be deleted in case of system update.

2. The basic object of systemd is _unit_, which has a name and type, such like
_NetworkManager.service_ defines a service to handle network management,
_systemd-shutdown.socket_ sets the socket for shutdown. Systemd types include service,
socket, busname, target, path, mount,...

3. Use command _systemctl list-units_ to get all units.

4. Use command _systemctl `<action`> `<service_name`>.service_ to manage service.
_action_ can be one of start, stop, restart, reload, enable, disable, mask, kill.

5. Use _systemctl `<action`> `<xxx`>_ to get info of a service.
_action_ can be one of list-unit-files, status, is-active, is-enabled, is-failed,...

6. Use _journalctl_ to view log information.


## 4. How to find out which one your machine are using?

1. check output of _stat /proc/1/exe_

2. if it gives _/sbin/init_, then try _stat /sbin/init_ or
_/sbin/init --version_

---END---
