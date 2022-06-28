# Background information
- Our `REALM` is `EC2.INTERNAL`
- We install KDC on a host `ip-172-17-1-212.ec2.internal` and its IP is `172.17.1.212`.

# Ensure hostname and time
## Hostname(主機名)
- You can use 1) `/etc/hosts` or 2) DNS to define hostname. Here, we use `/etc/hosts`
- use `hostname` to get name of this host
```
[root@ip-172-17-1-212 ~]# hostname
ip-172-17-1-212.ec2.internal
```
- update `/etc/hosts`: add `172.17.1.212 ip-172-17-1-212.ec2.internal` 
```
[root@ip-172-17-1-212 ~]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost6 localhost6.localdomain6
127.0.0.1	host.minikube.internal
172.17.1.212	ip-172-17-1-212.ec2.internal

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
## KDC package installation
- Package installation
```
[root@ip-172-17-1-212 ~]# yum install -y krb5-server krb5-libs krb5-auth-dialog krb5-workstation
```
## KDC configuration
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
```
## References
- [鳥哥的私房菜](https://linux.vbird.org/events/kerberos.php)
