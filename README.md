# restic_backup
A restic backup wrapper script with systemd timers <p>
Restic: https://github.com/restic/restic <p>
Rest-Server: https://github.com/restic/rest-server <p>
Docs: https://restic.readthedocs.io/en/latest/030_preparing_a_new_repo.html

## Install
### Debian / Ubuntu / CentOS / Arch / FreeBSD

1) download restic backup wrapper:<br>
<code>wget https://raw.githubusercontent.com/adminforge/restic_backup/master/backup</code>
2) create a RESTIC_REPOSITORY encryption password RESTIC_PASSWORD:<br>
<code>< /dev/urandom tr -dc A-Za-z0-9 | head -c16; echo</code>
3) choose repository type in top of backup script and set credentials (see Docs)
4) install: <code>bash backup install</code>
5) a daily backup will run every day between 0-5am

#### (optional) dependencies to backup mysql:
##### Debian / Ubuntu:
apt-get update && apt-get -y install mariadb-client

##### CentOS:
yum install -y mariadb-libs.x86_64

<code>systemctl start daily-backup-mysql.timer && systemctl enable daily-backup-mysql.timer</code>

#### FreeBSD cronjobs:
<pre>
0    3    *    *    *    (/usr/local/bin/backup run) > /dev/null
0    2    *    *    *    (/usr/local/bin/backup cleanup) > /dev/null
</pre>


## Usage
You can run <code>backup -h</code> to list wrapper help or <code>backup</code> to list restic original help. <p>

### run backup
<pre>
# backup run
open repository
repository be3c73d6 opened successfully, password is correct

Files:        3 new, 10 changed, 45330 unmodified
Dirs:         0 new,  0 changed,  0 unmodified
Added to the repo: 6.646 MiB

processed 45343 files, 1.745 GiB in 0:04
snapshot 56c0c21d saved
</pre>

### list backups
<pre>
# backup snapshots
repository be3c73d6 opened successfully, password is correct
ID     Time              Host       Tags     Paths
----------------------------------------------------------------------------------------------
cbd28eab  2019-03-27 14:29:13  srv01   /56c0c21d  2019-03-27 15:51:26  srv01           /
----------------------------------------------------------------------------------------------
2 snapshots
</pre>

### restore complete backup
<pre>
# backup restoresnap 56c0c21d
repository be3c73d6 opened successfully, password is correct
restoring <Snapshot 56c0c21d of [/] at 2019-03-27 15:51:26.549106581 +0100 CET by root@srv01> to /tmp/restore-2019-03-27_1602[...]
</pre>

### restore single folder
<pre>
# backup restore 56c0c21d /etc/default
repository be3c73d6 opened successfully, password is correct
restoring <Snapshot 56c0c21d of [/] at 2019-03-27 15:51:26.549106581 +0100 CET by root@srv01> to /tmp/restore-2019-03-27_1602[...]
# ls -lah /tmp/restore-2019-03-27_1602/etc/default/
total 80K
drwxr-xr-x 2 root root 4.0K Mar 21 10:50 .
drwx------ 3 root root 4.0K Mar 27 16:10 ..
-rw-r--r-- 1 root root  357 Oct  4  2014 arping
-rw-r--r-- 1 root root  222 Oct 25  2016 bsdmainutils
-rw-r--r-- 1 root root  194 Jul 22  2017 chrony
-rw-r--r-- 1 root root  955 May  3  2015 cron
-rw-r--r-- 1 root root  297 Mar  2  2018 dbus
-rw-r--r-- 1 root root 1014 Mar 21 10:35 exim4
-rw-r--r-- 1 root root 1.2K Jun 10  2018 grub
-rw-r--r-- 1 root root  657 Mar 22  2017 hwclock
-rw-r--r-- 1 root root  931 Jun 10  2018 irqbalance
-rw-r--r-- 1 root root   74 Jun 10  2018 locale
-rw-r--r-- 1 root root  306 Jun  2  2015 networking
-rw-r--r-- 1 root root  793 Dec 15  2016 nfs-common
-rw-r--r-- 1 root root  632 Dec 15  2016 nfs-kernel-server
-rw-r--r-- 1 root root 1.8K Jun 15  2017 nss
-rw-r--r-- 1 root root 2.1K Dec 10  2017 rsync
-rw-r--r-- 1 root root  124 Jan 18  2017 rsyslog
-rw-r--r-- 1 root root  133 Mar  1  2018 ssh
-rw-r--r-- 1 root root 1.1K May 17  2017 useradd
</pre>

### cleanup backups
<pre>
# backup cleanup
repository be3c73d6 opened successfully, password is correct
Applying Policy: keep the last 7 daily, 4 weekly, 2 monthly snapshots
[...]
1 snapshots have been removed, running prune
counting files in repo
building new index for repo
[0:13] 100.00%  791 / 791 packs
repository contains 791 packs (56758 blobs) with 3.230 GiB
processed 56758 blobs: 0 duplicate blobs, 0 B duplicate
load all snapshots
find data that is still in use for 17 snapshots
[0:02] 100.00%  17 / 17 snapshots
found 56718 of 56758 data blobs still in use, removing 40 blobs
will remove 0 invalid files
will delete 2 packs and rewrite 4 packs, this frees 6.740 MiB
[0:00] 100.00%  4 / 4 packs rewritten
counting files in repo
[0:13] 100.00%  789 / 789 packs
finding old index files
saved new indexes as [bae5475c]
remove 2 old index files
[0:00] 100.00%  6 / 6 packs deleted
done
</pre>


## Install
### Windows
1) open PowerShell ISE
2) install scoop <code>iex (new-object net.webclient).downloadstring('https://get.scoop.sh')</code>
3) install restic <code>scoop install restic</code>
4) PowerShell Wrapper:

* C:\Windows\System32\backup.ps1
<pre>
$env:AWS_ACCESS_KEY_ID = ""
$env:AWS_SECRET_ACCESS_KEY = ""
$env:RESTIC_REPOSITORY = "s3:https://gos3.io/bucketname"
$env:RESTIC_PASSWORD = ""

& "restic.exe" $args
</pre>

## Usage

### run backup
<pre>
Windows PowerShell
Copyright (C) 2016 Microsoft Corporation. All rights reserved.

PS C:\Users\Administrator> backup.ps1 backup --exclude-caches C:\Users
open repository
repository be3c73d6 opened successfully, password is correct

Files:        2 new,  5 changed,  4150 unmodified
Dirs:         0 new,  1 changed,  0 unmodified
Added to the repo: 32.619 KiB

processed 4157 files, 575.661 MiB in 0:02
snapshot e4aa8baa saved
</pre>

### cleanup backups
<pre>
Windows PowerShell
Copyright (C) 2016 Microsoft Corporation. All rights reserved.

PS C:\Users\Administrator> backup.ps1 forget --keep-daily 7 --keep-weekly 4 --keep-monthly 2 --prune
repository be3c73d6 opened successfully, password is correct
Applying Policy: keep the last 7 daily, 4 weekly, 2 monthly snapshots

[...]

6 snapshots have been removed, running prune
counting files in repo
building new index for repo
[0:09] 100.00%  583 / 583 packs
repository contains 583 packs (56586 blobs) with 2.331 GiB
processed 56586 blobs: 16 duplicate blobs, 78.772 KiB duplicate
load all snapshots
find data that is still in use for 13 snapshots
[0:04] 100.00%  13 / 13 snapshots
found 56414 of 56586 data blobs still in use, removing 172 blobs
will remove 0 invalid files
will delete 4 packs and rewrite 10 packs, this frees 5.769 MiB
[0:00] 100.00%  10 / 10 packs rewritten
counting files in repo
[0:09] 100.00%  571 / 571 packs
finding old index files
saved new indexes as [cffecfbd]
remove 7 old index files
[0:00] 100.00%  14 / 14 packs deleted
done
</pre>
