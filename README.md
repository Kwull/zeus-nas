# zeus-nas
Scripts, how-to, articles to configure zeus, etc...

Features:
- Samba - windows share
- Serviio + serviio web console - DLNA server
- transmission - torrents
- Crashplan - offsite backups
- Direct Connect with NCDC

System:
- Hardware 
- vivid kernel on ubuntu 14.04
- Read-only root file system locate on USB Flash drive
- NAS disk consolidation by aufs
- HDD S.M.A.R.T Check
- System emails via Gmail and sSMTP
- UPS 
- Webmin - web config
- NewRelic for server monitoring

How to:
- Add a HDD
- How to remove duplicates

# Hardware 
http://n40l.wikia.com/wiki/HP_MicroServer_N40L_Wiki

http://homeservershow.com/forums/index.php?/topic/3506-more-interesting-microserver-links/

# vivid kernel on ubuntu 14.04
```sh
apt-get update
apt-get upgrade
apt-get install --install-recommends linux-generic-lts-vivid 
```

# Read-only root file system
Original artticle from: https://help.ubuntu.com/community/aufsRootFileSystemOnUsbFlash

for kernels >= 3.18 add overlay 

for kernels < 3.18 add overlayfs 

to \etc\initramfs-tools\modules 

```sh
apt-get install aufs-tools
```
Copy root-ro file into /etc/initramfs-tools/scripts/init-bottom
```sh
chmod 0755 root-ro
update-initramfs -u 
```
Remount root (/mnt/root-ro) scripts:
```sh
/usr/bin/remountrw
/usr/bin/remountro
```

You can chroot to the /mnt/root-ro to install updates

# NAS disk consolidation by aufs
/etc/fstab

aufs manual: http://aufs.sourceforge.net/aufs3/man.html

can be replaced by mergefs (user mode/fuse)
```sh
apt-get install libfuse-dev libattr1-dev pandoc git -y
git clone https://github.com/trapexit/mergerfs.git
cd mergerfs
make
make install
```

```sh
nano /etc/fstab
```
/mnt/data/*  /storage  fuse.mergerfs  defaults,allow_other  0       0

# transmission - torrents
```sh
apt-get install transmission-daemon
service transmission-daemon stop
nano /etc/transmission-daemon/settings.json
```

# Crashplan - offsite backups
http://support.code42.com/CrashPlan/Latest/Configuring/Using_CrashPlan_On_A_Headless_Computer

http://support.code42.com/CrashPlan/Latest/Configuring/Using_CrashPlan_On_A_Headless_Computer_Version_4.2_And_Earlier

# System emails via Gmail and sSMTP
```sh
apt-get install ssmtp
nano /etc/ssmtp/ssmtp.conf
nano /etc/ssmtp/revaliases
```

# UPS
```sh
apt-get install apcupsd
nano /etc/apcupsd/apcupsd.conf
```

UPSNAME BX650CI
UPSCABLE usb
UPSTYPE usb
STATFILE /media/nas/logs/apcupsd.status

```sh
nano /etc/default/apcupsd
```

ISCONFIGURED=yes

Start:
```sh
/etc/init.d/apcupsd restart
```

Status:
```sh
apcaccess status
```

# Webmin - web config
```sh
apt-get install webmin
```

# HDD S.M.A.R.T Check
```sh
apt-get install smartmontools
```

Enable S.M.A.R.T on a HDD
```sh
smartctl -s on -d ata /dev/sdX
```

```sh
nano /etc/smartd.conf
```

/dev/sda -H -s (O/../../4/02|L/../../4/04|C/../../4/06) -d sat -m user@gmail.com

/dev/sdb -H -s (O/../../4/02|L/../../4/04|C/../../4/06) -d sat -m user@gmail.com

/dev/sdc -H -s (O/../../4/02|L/../../4/04|C/../../4/06) -d sat -m user@gmail.com

```sh
nano /etc/default/smartmontools
```

start_smartd=yes

Restart:
```sh
/etc/init.d/smartmontools restart
```

# Samba - windows share
```sh
apt-get install samba smbfs
mv /etc/samba/smb.conf /etc/samba/smb.conf.orig
nano /etc/samba/smb.conf
```

# Serviio + serviio web console - DLNA server
Java 8 is required to run latest serviio

```sh
add-apt-repository ppa:webupd8team/java
apt-get update
apt-get install oracle-java8-installer
```

Install latest ffmpeg
```sh
sudo add-apt-repository ppa:kirillshkrogalev/ffmpeg-next
sudo apt-get update
sudo apt-get install ffmpeg
```

WebUI: https://github.com/SwoopX/Web-UI-for-Serviio/tree/Serviio-1.5

# Add a HDD
```sh
apt-get install parted gdisk
parted -a optimal /dev/sdb
```

```
GNU Parted 2.3
Using /dev/sdb
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted) mklabel gpt 
(parted) mkpart primary 1 -1
(parted) align-check                                                      
alignment type(min/opt)  [optimal]/minimal? optimal                       
Partition number? 1                                                       
1 aligned
(parted) quit
```

```sh
mkdir /media/diskN
mkfs.ext4 -m 2 -T largefile4 /dev/sdb1
blkid
nano /etc/fstab
```

# How to remove duplicates
Replace duplicates by hardlinks:

```sh
apt-get install fdupes
fdupes -r -L /media/nas
```

# Direct Connect with NCDC
http://blog.kriswema.de/2012/12/direct-connect-with-ncdc.html
