# Background information
- Our `REALM` is `EC2.INTERNAL`
- We install KDC on a host `ip-172-17-1-212.ec2.internal` and its IP is `172.17.1.212`.
- REALM: EC2.INTERNAL
- hadoop folder: `/opt/kerberos-hadoop/latest/`
- we use openjdk 1.8
- Python version we use is Python 3.7
- Make sure selinux is disabled. Please check `/etc/selinux/config`. It should look like
```
SELINUX=disabled
SELINUXTYPE=targeted
```
- host list
```
172.17.1.212	ip-172-17-1-212.ec2.internal time server + KDC
172.17.2.110    ip-172-17-2-110.ec2.internal namenode
172.17.2.130    ip-172-17-2-130.ec2.internal secondary namenode
172.17.2.96     ip-172-17-2-96.ec2.internal  datanode
```
- on each host (172.17.2.110, 172.17.2.130, 172.17.2.96), we install `java-1.8.0-openjdk-1.8.0.312.b07-1.amzn2.0.2.x86_64` + `java-1.8.0-openjdk-devel-1.8.0.312.b07-1.amzn2.0.2.x86_64`
