# Disclaimer

I take no responsability for any problems a user might have on following this gist. This includes university problems.  

The motivation for this is to document, as dummy-oriented as possible, a way to setup and add a system call to Minix OS. This is a classic assignment at Operational Systems classes (_and is pretty cool tbh_)


# Configuration: 

**ISO used:** minix_R3.3.0-588a35b.iso  
Download: http://download.minix3.org/iso/minix_R3.3.0-588a35b.iso.bz2
```
md5: 78cc6093bc9e68c74f32f37e144451bd
SHA256: 389be8cbc2b74022671c082d0b88511b17e63b18003a14647b03331e3467b4c4
```

**Virtualization software used:** VirtualBox  5.0.40_Ubuntu r115130  
**Configurations:**
* Name: minix  
* Type: Other, Version: Other/Unknown  
* 1 GB ram  
* VDI (Fixed size, 6 GB)  

---

# Installation

* Insert the ISO in your virtualized machine and power it on
* Enter `root`
* Enter `setup`
* Press enter to continue
* Enter your keyboard layout (I'm using without any problems an irish keyboard with uk layout)
* Press enter to continue until prompted on a yes/no question, then you enter `yes`
* When prompted to reboot, type `poweroff` to shutdown the system instead
* Remove the ISO from your virtualized machine

The system is now installed. You can boot the virtual machine and enter `root` to log as root user. Any command below is expected to be ran as root unless stated.

---

# Before anything else...

**...update the pkgin packages:**
```sh
pkgin update
```

---

# C utilities

**Installing binutils, clang, nano (if you're hacky, you can install vim instead of nano)**
```sh
pkgin install binutils clang nano
```
**Optional: installing Vim:** _(Vim is nicer, but further instructions will support Nano instead)_
```sh
pkgin install vim
printf 'set backspace=indent,eol,start' >> /root/vim.rc # fix backspace behaviour
```
**And from now, you can write text (or source codes) with nano and compile C source code with Clang.**  
e.g.:
```sh
nano hello_world.c
# edit a hello_world program, ctrl-x to exit, Y to save, enter to confirm
clang hello_world.c -o hello_world
./hello_world
```

---

# Create a new user (optional but recommended)

**Add a new user and set a password for it (because you know, _safety_)** _(password is semi-mandatory for using SSH, just do it)_
```sh
useradd -m giba_god # giba_god is only an example whose name reflects the truth
passwd giba_god
```

---

# OpenSSH (optional)

**Installing openssh and running it and setting it to start automatically**
```sh
pkgin install openssh
printf 'sshd=YES\n' >> /etc/rc.conf # enable SSH daemon
/usr/pkg/etc/rc.d/sshd start # start SSH
```

**Optional: allowing to SSH into root** (not recommended)
```sh
printf 'PermitRootLogin yes' >> /usr/pkg/etc/ssh/sshd_config # useful in case you opted for not creating another users besides root
```

**Setting a root password** (recommended. Semi-mandatory for using ssh into root)
```sh
passwd root
```

**Now Minix is running a SSH server. That means you can connect to it from other clients (e.g.: from your Windows)**

---

# Connecting via SSH from host OS (optional)
#### _Instructions below are for VirtualBox_

##### If host OS is Linux:

```sh
VBoxManage modifyvm "<YOUR VM NAME GOES HERE>" --natpf1 "guestssh,tcp,,2222,,22" # my VM name is minix, but yours may be different
ssh -l <USERNAME> -p 2222 localhost
```
_And now you are connected remotely to your Minix!_ **Huzzah!**

##### If host OS is Windows:

* With cmd on VirtualBox folder  
```sh
VBoxManage modifyvm "<YOUR VM NAME GOES HERE>" --natpf1 "guestssh,tcp,,2222,,22" # my VM name is minix, but yours may be different
```
* Close cmd. Download and open PuTTY
* On Connection>Data, change 'Auto-login username' to the user you want to connect to (e.g.: root)
* On Session, type 'localhost' on 'Host Name' and '2222' on port and connect

_And now you are connected remotely to your Minix!_ **Huzzah!**

---

# Getting Minix source code

**For whatever reason, Minix source files are NOT included since 3.3 _(am I right?)_. To get them, follow bellow**
```sh
pkgin install git-base
cd /usr/pkg/etc/rc.d/
rm kadmind kcm kdc kpasswdd # these files may cause a bunch of errors, see https://groups.google.com/forum/#!topic/minix3/HrQe7xJbnDQ
git clone git://git.minix3.org/minix /usr/src
cd /usr/src
git checkout v3.3.0 # we do not want to mess with bleeding edge code (do we?)
```

Now you have Minix source on your system! **Huzzah!**  
You can add system calls to it, recompile it, and have fun with it!

---

# To The Action!
### (a.k.a. modifiying and recompilling the system)

```sh
pkgin install bmake gmake # netBSD make and GNU make are needed to recompile minix
# edit whatever you want into the system (refer to the other document provided to create system calls)
cd /usr/src
make build # this will recompile EVERYTHING. make services or make install may also work; more testing is needed
```

---

# Info resources:
* http://www.cis.syr.edu/~wedu/seed/Documentation/Minix3/How_to_add_system_call.pdf
* http://homepages.cs.ncl.ac.uk/nick.cook/csc2025/minix/syscall-exercise1.html


# Good luck on your exams! :-)
