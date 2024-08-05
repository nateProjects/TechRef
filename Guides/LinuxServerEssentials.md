## Distro

``cat /etc/issue`` or ``cat /etc/os-release``

detailed - 

``echo /etc/*_ver* /etc/*-rel*; cat /etc/*_ver* /etc/*-rel*``

## Memory

Watch memory and highlight differences

``watch -n 5 -d 'free -m'``

List of top processes

``ps aux | sort -nk +4 | tail | tac``

## CPU

``ps auxf | sort -nr -k 3 | head -10``

## Drive

List partition -

``fdisk -l``

Show mounts -

``mount | column -t``

Space used -

``df -H``

Example Drive / Partion Setup

```
lsblk
mkfs.xfs /dev/sdc
parted -a optimal /dev/sdc
mklabel gpt
unit GB
mkpart primary 0 20
mkfs.xfs /dev/sdc1
```

/etc/fstab

``/dev/sdc1      /opt  xfs     defaults        0 0``

What is writing to the drive the most?

``iotop``

(press o to only show current processes)

What is taking the most directory space?

```
FS='./';resize;clear;date;df -h $FS; echo "Largest Directories:"; du -hcx --max-  depth=2 $FS 2>/dev/null | grep [0-9]G | sort -grk 1 | head -15 ;echo "Largest Files:"; nice -n 19 find $FS -mount -type f -print0 2>/dev/null| xargs -0 du -k | sort -rnk1| head -n20 |awk -F'\t' '{printf "%8d MB\t%s\n",($1/1024),$NF}'
```

## Logs

Quick error check - dmesg etc.

``tail -f -n 5 /var/log/syslog``

Find failures with journalctl

```
journalctl -f

journalctl -n25 --since "5min ago"

journalctl -p "emerg".."crit"

journalctl -n25 -p "emerg".."err"
```

Essential Logs -

```
tail -n 5 /var/log/syslog -n 5 /var/log/auth.log -n 5 /var/log/boot.log -n 5 /var/log/cron -n 5 /var/log/dmesg -n 5 /var/log/faillog -n 5 /var/log/kern.log -n 5 /var/log/maillog -n 5 /var/log/messages -n 5 /var/log/secure.log
```

See logged in users -

``w``

## MySQL

Monitor the queries being run by MySQL

``watch -n 1 mysqladmin --user=<user> --password=<password> processlist``

## Clean Up Linux

### Ubuntu

```
#!/bin/bash
# sudo apt-get purge $(dpkg -l linux-{image,headers}-"[0-9]*" | awk '/ii/{print $2}' | grep -ve "$(uname -r | sed -r 's/-[a-z]+//')")
apt-get clean
apt-get autoremove --purge
apt-get autoremove
journalctl --vacuum-time=3d
# Removes old revisions of snaps
# CLOSE ALL SNAPS BEFORE RUNNING THIS
set -eu
snap list --all | awk '/disabled/{print $1, $3}' |
    while read snapname revision; do
        snap remove "$snapname" --revision="$revision"
    done
```

Check DB size

```
SELECT table_schema "Data Base Name", sum( data_length + index_length ) / 1024 / 1024 "Data Base Size in MB" FROM information_schema.TABLES GROUP BY table_schema ;
```
