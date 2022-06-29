# Ensure hostname and time
## Hostname(主機名)
- You can use 1) `/etc/hosts` or 2) DNS to define hostname. Here, we use `/etc/hosts`
- use `hostname` to get name of EVERY host
```
[root@ip-172-17-1-212 ~]# hostname
ip-172-17-1-212.ec2.internal
```
- update `/etc/hosts` on EVERY host.
```
[root@ip-172-17-2-110 ~]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost6 localhost6.localdomain6
172.17.1.212	ip-172-17-1-212.ec2.internal
172.17.2.110    ip-172-17-2-110.ec2.internal
172.17.2.130    ip-172-17-2-130.ec2.internal
172.17.2.96     ip-172-17-2-96.ec2.internal

```
## Time(時間)
- We must ensure time every host controlled by KDC must be in sync
- On linux, we use `chrony`.
- Let's modify `/etc/chrony.conf`
```
[root@ip-172-17-1-212 ~]# vim /etc/chrony.conf 
server 169.254.169.123 prefer iburst minpoll 4 maxpoll 4
pool 0.amazon.pool.ntp.org iburst maxsources 1
pool 1.amazon.pool.ntp.org iburst maxsources 1
pool 2.amazon.pool.ntp.org iburst maxsources 2
driftfile /var/lib/chrony/drift
makestep 1.0 3
rtcsync
allow 172.17.0.0/16        # ---> I only modified this one.
keyfile /etc/chrony.keys
logdir /var/log/chrony
dumponexit
dumpdir /var/run/chrony
sourcedir /run/chrony-dhcp
```
- Start chronyd
```
[root@ip-172-17-1-212 ~]# systemctl restart chronyd
[root@ip-172-17-1-212 ~]# systemctl enable chronyd # ---> start this service when booting up this host.
```
- Verify
```
[root@ip-172-17-1-212 ~]#  systemctl status chronyd
● chronyd.service - NTP client/server
   Loaded: loaded (/usr/lib/systemd/system/chronyd.service; enabled; vendor preset: enabled)
   Active: active (running) since Tue 2022-06-28 01:41:09 UTC; 3min 13s ago
     Docs: man:chronyd(8)
           man:chrony.conf(5)
  Process: 8666 ExecStart=/usr/sbin/chronyd $OPTIONS (code=exited, status=0/SUCCESS)
 Main PID: 8707 (chronyd)
    Tasks: 1
   Memory: 1.0M
   CGroup: /system.slice/chronyd.service
           └─8707 /usr/sbin/chronyd

Jun 28 01:41:09 ip-172-17-1-212.ec2.internal systemd[1]: Starting NTP client/server...
Jun 28 01:41:09 ip-172-17-1-212.ec2.internal chronyd[8707]: chronyd version 4.0 starting (+CMDMON +NTP +REFCLOCK +RTC +PRIVDROP +SCFILTER +SIGND +ASYNCDNS -NTS +SECHASH +IPV6 +DEBUG)
Jun 28 01:41:09 ip-172-17-1-212.ec2.internal chronyd[8707]: Frequency -28.099 +/- 0.024 ppm read from /var/lib/chrony/drift
Jun 28 01:41:09 ip-172-17-1-212.ec2.internal systemd[1]: Started NTP client/server.
Jun 28 01:41:15 ip-172-17-1-212.ec2.internal chronyd[8707]: Selected source 169.254.169.123

```
- set up other hosts: ntp. On each host, /etc/chronyd should look like
```
server 169.254.169.123 prefer iburst minpoll 4 maxpoll 4

pool 172.17.1.212 iburst maxsources 1
pool 0.amazon.pool.ntp.org iburst maxsources 1
pool 1.amazon.pool.ntp.org iburst maxsources 1
pool 2.amazon.pool.ntp.org iburst maxsources 2
driftfile /var/lib/chrony/drift
makestep 1.0 3
rtcsync
keyfile /etc/chrony.keys
logdir /var/log/chrony
dumponexit
dumpdir /var/run/chrony
sourcedir /run/chrony-dhcp
```
- set up other hosts: enable chronyd. On each host, enable/start chronyd
```
# systemctl start chronyd
# systemctl enable chronyd
# systemctl status chronyd
● chronyd.service - NTP client/server
   Loaded: loaded (/usr/lib/systemd/system/chronyd.service; enabled; vendor preset: enabled)
   Active: active (running) since 三 2022-06-29 02:17:31 UTC; 46min ago
     Docs: man:chronyd(8)
           man:chrony.conf(5)
 Main PID: 1804 (chronyd)
   CGroup: /system.slice/chronyd.service
           └─1804 /usr/sbin/chronyd

 6月 29 02:17:31 localhost systemd[1]: Starting NTP client/server...
 6月 29 02:17:31 localhost chronyd[1804]: chronyd version 4.0 starting (+CMDMON +NTP +REFCLOCK +RTC +PRIVDROP +SCFILTER +SIGND +ASYNCDNS -NTS +SECHASH +IPV6 +DEBUG)
 6月 29 02:17:31 localhost systemd[1]: Started NTP client/server.
 6月 29 02:17:37 ip-172-17-2-96.ec2.internal chronyd[1804]: Selected source 169.254.169.123

```
## create group: `hadoop` and user account: `hdfs`, `mapred`, `hadoop`, `yarn`, `HTTP`
```
[root@ip-172-17-1-212 ~]# groupadd hadoop
[root@ip-172-17-1-212 ~]# useradd hadoop -g hadoop
[root@ip-172-17-1-212 ~]#  useradd hdfs -g hadoop
[root@ip-172-17-1-212 ~]#  useradd mapred -g hadoop
[root@ip-172-17-1-212 ~]#  useradd yarn -g hadoop
[root@ip-172-17-1-212 ~]#  useradd yarn -g hadoop
[root@ip-172-17-1-212 ~]#  useradd HTTP -g hadoop
```
## set up environment in `~/.bash_profile` of `root`
```
[root@ip-172-17-1-212 ~]# cat ~/.bash_profile 
# .bash_profile

# Get the aliases and functions
if [ -f ~/.bashrc ]; then
	. ~/.bashrc
fi

# User specific environment and startup programs

PATH=$PATH:$HOME/bin
alias python=python3.7
export PYSPARK_PYTHON=python3.7
export PYSPARK_DRIVER_PYTHON=python3.7
export KRB5CCNAME=/tmp/$USER\_krb5cc
export PATH
```
# KDC package installation
- Package installation
```
[root@ip-172-17-1-212 ~]# yum install -y krb5-server krb5-libs krb5-auth-dialog krb5-workstation
```
# KDC configuration
- modify `/etc/krb5.conf`
```
[root@ip-172-17-1-212 ~]# vim /etc/krb5.conf
# Configuration snippets may be placed in this directory as well
includedir /etc/krb5.conf.d/

[logging]
 default = FILE:/var/log/krb5libs.log
 kdc = FILE:/var/log/krb5kdc.log
 admin_server = FILE:/var/log/kadmind.log

[libdefaults]
 dns_lookup_realm = false
 ticket_lifetime = 24h
 renew_lifetime = 7d
 forwardable = true
 rdns = false
 pkinit_anchors = /etc/pki/tls/certs/ca-bundle.crt
 default_realm = EC2.INTERNAL
 default_ccache_name = KEYRING:persistent:%{uid}
 dns_lookup_kdc = false
 udp_preference_limit=1
 clockskew=180
 renewable = true
 default_tgs_enctypes = arcfour-hmac
 default_tkt_enctypes = arcfour-hmac

[realms]
 EC2.INTERNAL = {
  kdc = ip-172-17-1-212.ec2.internal
  admin_server = ip-172-17-1-212.ec2.internal
 }

[domain_realm]
 .ec2.internal = EC2.INTERNAL
 ec2.internal = EC2.INTERNAL

[kdc]
 profile=/var/kerberos/krb5kdc/kdc.conf

```
- modify `/var/kerberos/krb5kdc/kdc.conf`
```
[root@ip-172-17-1-212 ~]#  vim /var/kerberos/krb5kdc/kdc.conf
[kdcdefaults]
 kdc_ports = 88
 kdc_tcp_ports = 88

[realms]
 EC2.INTERNAL = {
  #master_key_type = aes256-cts
  acl_file = /var/kerberos/krb5kdc/kadm5.acl
  dict_file = /usr/share/dict/words
  admin_keytab = /var/kerberos/krb5kdc/kadm5.keytab
  supported_enctypes = aes256-cts:normal aes128-cts:normal des3-hmac-sha1:normal arcfour-hmac:normal camellia256-cts:normal camellia128-cts:normal des-hmac-sha1:normal des-cbc-md5:normal des-cbc-crc:normal
  max_life = 24h
  max_renewable_life = 10d
  default_principal_flags =  renewable,  forwardable
 }

```
- modify `/var/kerberos/krb5kdc/kadm5.acl`
```
[root@ip-172-17-1-212 ~]# vim /var/kerberos/krb5kdc/kadm5.acl
*/admin@EC2.INTERNAL *

```
- create database
```
[root@ip-172-17-1-212 ~]# kdb5_util create -r EC2.INTERNAL -s
Loading random data
Initializing database '/var/kerberos/krb5kdc/principal' for realm 'BOOK.VBIRD',
master key name 'K/M@BOOK.VBIRD'
You will be prompted for the database Master Password.
It is important that you NOT FORGET this password.
Enter KDC database master key:  <------------------------ Enter password
Re-enter KDC database master key to verify:  <----------- Enter password
```
- verify
```
[root@ip-172-17-1-212 ~]# ls -l  /var/kerberos/krb5kdc/ 
total 56
-rw------- 1 root root    23 Jun 26 14:22 kadm5.acl
-rw------- 1 root root    23 Jun 20 14:44 kadm5.acl.rpmsave
-rw------- 1 root root   549 Jun 28 01:58 kdc.conf
-rw------- 1 root root   604 Jun 26 14:11 kdc.conf.rpmsave
-rw------- 1 root root 20480 Jun 27 15:08 principal
-rw------- 1 root root  8192 Jun 26 14:23 principal.kadm5
-rw------- 1 root root     0 Jun 26 14:23 principal.kadm5.lock
-rw------- 1 root root     0 Jun 27 15:08 principal.ok
```
- start services: `krb5kdc` and `kadmin`
```
[root@ip-172-17-1-212 ~]# systemctl enable krb5kdc
[root@ip-172-17-1-212 ~]# systemctl start krb5kdc
[root@ip-172-17-1-212 ~]# systemctl status krb5kdc
● krb5kdc.service - Kerberos 5 KDC
   Loaded: loaded (/usr/lib/systemd/system/krb5kdc.service; enabled; vendor preset: disabled)
   Active: active (running) since Sun 2022-06-26 14:24:24 UTC; 1 day 23h ago
 Main PID: 17890 (krb5kdc)
   CGroup: /system.slice/krb5kdc.service
           └─17890 /usr/sbin/krb5kdc -P /var/run/krb5kdc.pid

Jun 26 14:24:24 ip-172-17-1-212.ec2.internal systemd[1]: Starting Kerberos 5 KDC...
Jun 26 14:24:24 ip-172-17-1-212.ec2.internal systemd[1]: Started Kerberos 5 KDC.

[root@ip-172-17-1-212 ~]# systemctl enable kadmin
[root@ip-172-17-1-212 ~]# systemctl start kadmin
[root@ip-172-17-1-212 ~]# systemctl status kadmin
● kadmin.service - Kerberos 5 Password-changing and Administration
   Loaded: loaded (/usr/lib/systemd/system/kadmin.service; enabled; vendor preset: disabled)
   Active: active (running) since Sun 2022-06-26 14:27:19 UTC; 1 day 23h ago
 Main PID: 19012 (kadmind)
   CGroup: /system.slice/kadmin.service
           └─19012 /usr/sbin/kadmind -P /var/run/kadmind.pid

Jun 26 14:27:18 ip-172-17-1-212.ec2.internal systemd[1]: Starting Kerberos 5 Password-changing and Administration...
Jun 26 14:27:19 ip-172-17-1-212.ec2.internal systemd[1]: Started Kerberos 5 Password-changing and Administration.

```
- create kerberos admin account - root/admin@EC2.INTERNAL
```
[root@ip-172-17-1-212 ~]# kadmin.local
kadmin:  listprincs
K/M@EC2.INTERNAL
kadmin/admin@EC2.INTERNAL
kadmin/changepw@EC2.INTERNAL
kadmin/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
kiprop/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
krbtgt/EC2.INTERNAL@EC2.INTERNAL

kadmin: addprinc root/admin 
WARNING: no policy specified for root/admin@EC2.INTERNAL; defaulting to no policy
Enter password for principal "root/admin@EC2.INTERNAL":  -------------> input password
Re-enter password for principal "root/admin@EC2.INTERNAL":  ----------> input password
Principal "root/admin@EC2.INTERNAL" created.

```
- verify with `kadmin`
```
kadmin:  listprincs
HTTP/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
K/M@EC2.INTERNAL
kadmin/admin@EC2.INTERNAL
kadmin/changepw@EC2.INTERNAL
kadmin/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
kiprop/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
krbtgt/EC2.INTERNAL@EC2.INTERNAL
root/admin@EC2.INTERNAL ---------------------------> if you see this, that should be no problem
```
- verify with `kinit`
```
[root@ip-172-17-1-212 ~]# kinit root/admin
Password for root/admin@EC2.INTERNAL: 
[root@ip-172-17-1-212 ~]# klist
Ticket cache: FILE:/tmp/root_krb5cc
Default principal: root/admin@EC2.INTERNAL

Valid starting       Expires              Service principal
06/28/2022 14:09:27  06/29/2022 14:09:27  krbtgt/EC2.INTERNAL@EC2.INTERNAL
	renew until 07/05/2022 14:09:27
```
- export and save admin keytab
```
[root@ip-172-17-1-212 krb5kdc]# kadmin.local -q "ktadd kadmin/admin"
Authenticating as principal root/admin@EC2.INTERNAL with password.
Entry for principal kadmin/admin with kvno 2, encryption type aes256-cts-hmac-sha1-96 added to keytab FILE:/etc/krb5.keytab.
Entry for principal kadmin/admin with kvno 2, encryption type aes128-cts-hmac-sha1-96 added to keytab FILE:/etc/krb5.keytab.
Entry for principal kadmin/admin with kvno 2, encryption type des3-cbc-sha1 added to keytab FILE:/etc/krb5.keytab.
Entry for principal kadmin/admin with kvno 2, encryption type arcfour-hmac added to keytab FILE:/etc/krb5.keytab.
Entry for principal kadmin/admin with kvno 2, encryption type camellia256-cts-cmac added to keytab FILE:/etc/krb5.keytab.
Entry for principal kadmin/admin with kvno 2, encryption type camellia128-cts-cmac added to keytab FILE:/etc/krb5.keytab.
Entry for principal kadmin/admin with kvno 2, encryption type des-hmac-sha1 added to keytab FILE:/etc/krb5.keytab.
Entry for principal kadmin/admin with kvno 2, encryption type des-cbc-md5 added to keytab FILE:/etc/krb5.keytab.

[root@ip-172-17-1-212 krb5kdc]# klist -k /etc/krb5.keytab 
Keytab name: FILE:/etc/krb5.keytab
KVNO Principal
---- --------------------------------------------------------------------------
   2 kadmin/admin@EC2.INTERNAL
   2 kadmin/admin@EC2.INTERNAL
   2 kadmin/admin@EC2.INTERNAL
   2 kadmin/admin@EC2.INTERNAL
   2 kadmin/admin@EC2.INTERNAL
   2 kadmin/admin@EC2.INTERNAL
   2 kadmin/admin@EC2.INTERNAL
   2 kadmin/admin@EC2.INTERNAL

```

## References
- [鳥哥的私房菜](https://linux.vbird.org/events/kerberos.php)
