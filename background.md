# Background information
- Our `REALM` is `EC2.INTERNAL`
- We install KDC on a host `ip-172-17-1-212.ec2.internal` and its IP is `172.17.1.212`.
- REALM: EC2.INTERNAL
- hadoop folder: `/opt/kerberos-hadoop/latest/`
- Make sure selinux is disabled. Please check `/etc/selinux/config`. It should look like
```
SELINUX=disabled
SELINUXTYPE=targeted
```
- host list
```
172.17.1.212	ip-172-17-1-212.ec2.internal time server + KDC
172.17.2.110    ip-172-17-2-110.ec2.internal namenode + hadoop client
172.17.2.130    ip-172-17-2-130.ec2.internal secondary namenode
172.17.2.96     ip-172-17-2-96.ec2.internal  datanode
```
- on each host (172.17.2.110, 172.17.2.130, 172.17.2.96), we install `python3-3.7.10-1.amzn2.0.1.x86_64` + `java-1.8.0-openjdk-1.8.0.312.b07-1.amzn2.0.2.x86_64` + `java-1.8.0-openjdk-devel-1.8.0.312.b07-1.amzn2.0.2.x86_64`
```
[root@ip-172-17-2-110 ~]# yum install -y java-1.8.0-openjdk-1.8.0.312.b07-1.amzn2.0.2.x86_64 java-1.8.0-openjdk-devel-1.8.0.312.b07-1.amzn2.0.2.x86_64 python3-3.7.10-1.amzn2.0.1.x86_64
```
- I use account `hdfs` from `172.17.2.110` to login `172.17.2.130` and `172.17.2.96` without password
```
## Please make sure hdfs can login  `172.17.2.130` and `172.17.2.96` without password
## 1. make sure the account we use is hdfs and host we use is ip-172-17-2-110.ec2.internal (172.17.2.110) 
[hdfs@ip-172-17-2-110 ~]$ hostname
ip-172-17-2-110.ec2.internal
[hdfs@ip-172-17-2-110 ~]$ whoami
hdfs

## 2. login ip-172-17-2-96.ec2.internal (172.17.2.110)
[hdfs@ip-172-17-2-110 ~]$ ssh ip-172-17-2-96.ec2.internal
Last login: Fri Jul  1 02:07:16 2022 from ip-172-17-2-110.ec2.internal

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
[hdfs@ip-172-17-2-96 ~]$ logout
Connection to ip-172-17-2-96.ec2.internal closed.

## 3. login ip-172-17-2-130.ec2.internal (172.17.2.130)
[hdfs@ip-172-17-2-110 ~]$ ssh ip-172-17-2-130.ec2.internal
Last login: Fri Jul  1 02:07:10 2022 from ip-172-17-2-110.ec2.internal

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
[hdfs@ip-172-17-2-130 ~]$ logout

```
- I use account `root` from `172.17.2.110` to login `172.17.2.130` and `172.17.2.96` without password
```
## Please make sure root can login  `172.17.2.130` and `172.17.2.96` without password
## 1. make sure the account we use is root and host we use is ip-172-17-2-110.ec2.internal (172.17.2.110)
[root@ip-172-17-2-110 ~]# hostname
ip-172-17-2-110.ec2.internal
[root@ip-172-17-2-110 ~]# whoami
root


## 2. login ip-172-17-2-96.ec2.internal (172.17.2.110)
[root@ip-172-17-2-110 ~]# ssh 172.17.2.110
.Last login: Fri Jul  1 02:25:49 2022 from ip-172-17-1-212.ec2.internal

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
[root@ip-172-17-2-110 ~]# logout
Connection to 172.17.2.110 closed.
[root@ip-172-17-2-110 ~]# ssh ip-172-17-2-130.ec2.internal
Last login: Fri Jul  1 02:25:27 2022 from ip-172-17-1-212.ec2.internal

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
[root@ip-172-17-2-130 ~]# logout


## 3. login ip-172-17-2-130.ec2.internal (172.17.2.130)
Connection to ip-172-17-2-130.ec2.internal closed.
[root@ip-172-17-2-110 ~]# ssh  ip-172-17-2-96.ec2.internal
Last login: Fri Jul  1 02:22:08 2022 from ip-172-17-1-212.ec2.internal

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
[root@ip-172-17-2-96 ~]# logout


```

