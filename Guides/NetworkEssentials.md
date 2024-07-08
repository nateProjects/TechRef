## IPs & Ports

Find Primary IP

`ip a s $(ip r s 0/0 | awk '{print $5;exit}') | awk '$1 ~ /inet$/ {print $2;exit}'`

Find Public IP

`curl icanhazip.com # note: may give IPv6`

`curl ifconfig.me`

Where is most traffic coming from?

`sudo apt install tcptrack`

`# sudo tcptrack -i eth0`

`# tcptrack -i eth0 port 443`

also consider

`sudo apt install iftop / iptraf / ntop # etc.`

or nethogs for process level

`sudo nethogs -v 3`

Check Local / Remote Port Open

```
nc -zv 127.0.0.1 22 # localhost

nc -zv 10.0.10.1 22 # local network

nc -zv here.there.com 22-25 # remote range
```

List Open Ports

`netstat -tulanp`

Remote host - `nc -z 172.16.100.196 1-80`

Monitor Open Netstat Connections

`watch -n 1 "netstat -tpanl | grep ESTABLISHED"`

Top IP Addresses Hitting Webserver

Nginx

`cat /var/log/nginx/access.log | cut -f 1 -d ' ' | sort | uniq -c | sort -hr | head -n 10`

Apache - RH /var/log/http/access_log or Deb /var/log/apache2/access.log

`awk '{ print $1}' access.log | sort | uniq -c | sort -nr | head -n 10`

Ping IP Range

`nmap -sP 192.168.1.100-254`

Show Network Service Activity

```
lsof -i

lsof -P -i4 -n

lsof -i tcp:80
```

Connections to a service -

`netstat -np | awk '{print $7}' | awk -F/ '{count[$2]++}END{for(j in count) print count[j],j}' | sort -nr`

Apps using the internet -

`ss -p`

## SSH

SSH connection through host in the middle

`ssh -t reachable_host ssh unreachable_host`

copy ssh key

`ssh-copy-id username@hostname`

or

`cat ~/.ssh/id_rsa.pub | ssh user@machine "mkdir ~/.ssh; cat >> ~/.ssh/authorized_keys"`

Single use VNC-over-SSH connection

`ssh -f -L 5900:localhost:5900 your.ssh.server "x11vnc -safer -localhost -nopw -once -display :0"; vncviewer localhost:5900`

```
Supported escape sequences:
     ~.   - terminate connection (and any multiplexed sessions)
     ~B   - send a BREAK to the remote system
     ~C   - open a command line
     ~R   - request rekey
     ~V/v - decrease/increase verbosity (LogLevel)
     ~^Z  - suspend ssh
     ~#   - list forwarded connections
     ~&   - background ssh (when waiting for connections to terminate)
     ~?   - this message
     ~~   - send the escape character by typing it twice
(Note that escapes are only recognized immediately after newline.)
```

## Firewalls

### IPTables

Show -

```
iptables -L INPUT -n -v --line-numbers

iptables -L OUTPUT -n -v --line-numbers

iptables -L FORWARD -n -v --line-numbers
```

Block IP -

`iptables -A INPUT -s 10.10.10.10 -j DROP`

Block Port -

`iptables -A INPUT -p tcp --dport ssh -s 10.10.10.10 -j DROP`

Disable -

```
sudo iptables-save > /root/firewall.rules
iptables -X
iptables -t nat -F
iptables -t nat -X
iptables -t mangle -F
iptables -t mangle -X
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT
```

### Firewalld - Redhat / AWS

Show -

`firewall-cmd --list-all`

Block IP -

`firewall-cmd --permanent --add-rich-rule="rule family='ipv4' source address='192.168.0.11' reject"`

Block Port -

```
firewall-cmd --permanent --add-rich-rule='rule family=ipv4 port port="443" protocol="tcp" reject'

# Allow IP -   firewall-cmd --add-source=172.16.100.175 --permanent   Allow Port -

firewall-cmd --add-port=5666/tcp --permanent
```

Commit changes -

`firewall-cmd --reload`

Disable -

`systemctl disable firewalld`

### UFW / gufw - Ubuntu

Show -

`sudo ufw status`

Block IP -

`sudo ufw deny from {ip-address-here} to any`

Block Port -

`ufw deny 21/tcp`

Disable -

`sudo ufw disable`

SELinux Disable -

```
vi /etc/sysconfig/selinux

SELINUX=disabled

setenforce 0
```