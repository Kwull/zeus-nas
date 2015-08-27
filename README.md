# zeus-nas
Scripts, how-to, articles to configure zeus, etc...

Features:
- Hardware 
- vivid kernel on ubuntu 14.04
- Read-only root file system locate on USB Flash drive
- NAS disk consolidation by aufs
- HDD S.M.A.R.T Check
- Samba - windows share
- Serviio + serviio web console - DLNA server
- transmission - torrents
- Crashplan - offsite backups
- System emails via Gmail and sSMTP
- UPS 
- Webmin - web config
- NewRelic for server monitoring
- Add a HDD
- How to remove duplicates
- Direct Connect with NCDC

# Hardware 
http://n40l.wikia.com/wiki/HP_MicroServer_N40L_Wiki
http://homeservershow.com/forums/index.php?/topic/3506-more-interesting-microserver-links/

# vivid kernel on ubuntu 14.04
apt-get update
apt-get upgrade
apt-get install --install-recommends linux-generic-lts-vivid 

# Read-only root file system
Original artticle from: https://help.ubuntu.com/community/aufsRootFileSystemOnUsbFlash

for kernels >= 3.18 add overlay 
for kernels < 3.18 add overlayfs 
to \etc\initramfs-tools\modules 

apt-get install aufs-tools
put root-ro file into /etc/initramfs-tools/scripts/init-bottom
chmod 0755 root-ro
update-initramfs -u 

Remount root (/mnt/root-ro) scripts:
/usr/bin/remountrw
/usr/bin/remountro

You can chroot to the /mnt/root-ro to install updates

# NAS disk consolidation by aufs
/etc/fstab
aufs manual: http://aufs.sourceforge.net/aufs3/man.html

# transmission - torrents
apt-get install transmission-daemon
service transmission-daemon stop
nano /etc/transmission-daemon/settings.json

# Crashplan - offsite backups
http://support.code42.com/CrashPlan/Latest/Configuring/Using_CrashPlan_On_A_Headless_Computer
http://support.code42.com/CrashPlan/Latest/Configuring/Using_CrashPlan_On_A_Headless_Computer_Version_4.2_And_Earlier

# System emails via Gmail ans sSMTP
apt-get install ssmtp
nano /etc/ssmtp/ssmtp.conf
nano /etc/ssmtp/revaliases

can be replaced by mergefs (user mode/fuse)

apt-get install libfuse-dev libattr1-dev pandoc git -y
git clone https://github.com/trapexit/mergerfs.git
cd mergerfs
make
make install

nano /etc/fstab
/mnt/data/*  /storage  fuse.mergerfs  defaults,allow_other  0       0

# UPS
apt-get install apcupsd
nano /etc/apcupsd/apcupsd.conf

UPSNAME BX650CI
UPSCABLE usb
UPSTYPE usb
STATFILE /media/nas/logs/apcupsd.status

nano /etc/default/apcupsd
ISCONFIGURED=yes

Start:
/etc/init.d/apcupsd restart

Status:
apcaccess status

# Webmin - web config
apt-get install webmin

# HDD S.M.A.R.T Check
apt-get install smartmontools

Enable S.M.A.R.T on a HDD
smartctl -s on -d ata /dev/sdX

nano /etc/smartd.conf
/dev/sda -H -s (O/../../4/02|L/../../4/04|C/../../4/06) -d sat -m user@gmail.com
/dev/sdb -H -s (O/../../4/02|L/../../4/04|C/../../4/06) -d sat -m user@gmail.com
/dev/sdc -H -s (O/../../4/02|L/../../4/04|C/../../4/06) -d sat -m user@gmail.com

nano /etc/default/smartmontools
start_smartd=yes

Restart:
/etc/init.d/smartmontools restart

# Samba - windows share
apt-get install samba smbfs
mv /etc/samba/smb.conf /etc/samba/smb.conf.orig
nano /etc/samba/smb.conf

# Serviio + serviio web console - DLNA server
Java 8 is required to run latest serviio

add-apt-repository ppa:webupd8team/java
apt-get update
apt-get install oracle-java8-installer




WebUI: https://github.com/SwoopX/Web-UI-for-Serviio/tree/Serviio-1.5

# Add a HDD
apt-get install parted gdisk
parted -a optimal /dev/sdb

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

mkdir /media/diskN

mkfs.ext4 -m 2 -T largefile4 /dev/sdb1
blkid
nano /etc/fstab

# How to remove duplicates
Replace duplicates by hardlinks:

apt-get install fdupes
fdupes -r -L /media/nas

# Direct Connect with NCDC
http://blog.kriswema.de/2012/12/direct-connect-with-ncdc.html