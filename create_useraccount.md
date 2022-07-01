## Assumption
- I need to create a user account `ec2-user` and its group is also `ec2-user`.

## Account creation
- Execute the following command to create an account `ec2-user` and set up password on `172.17.2.110`. Be sure to use `root` to execute.
```
[root@ip-172-17-2-110 ~]# whoami
root
[root@ip-172-17-2-110 ~]# useradd ec2-user
[root@ip-172-17-2-110 ~]# passwd ec2-user
```
## Generate a principal for the account `ec2-user` on KDC (172.17.1.212)
- execute the following to generate principal for `ec2-user` for every host(172.17.2.110, 172.17.2.130, 172.17.2.96).
```
[root@ip-172-17-1-212 ~]# kadmin.local 
Authenticating as principal root/admin@EC2.INTERNAL with password.

kadmin.local:  addprinc ec2-user/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
WARNING: no policy specified for ec2-user/ip-172-17-2-110.ec2.internal@EC2.INTERNAL; defaulting to no policy
Enter password for principal "ec2-user/ip-172-17-2-110.ec2.internal@EC2.INTERNAL": 
Re-enter password for principal "ec2-user/ip-172-17-2-110.ec2.internal@EC2.INTERNAL": 
Principal "ec2-user/ip-172-17-2-110.ec2.internal@EC2.INTERNAL" created.

kadmin.local:  addprinc ec2-user/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
WARNING: no policy specified for ec2-user/ip-172-17-2-130.ec2.internal@EC2.INTERNAL; defaulting to no policy
Enter password for principal "ec2-user/ip-172-17-2-130.ec2.internal@EC2.INTERNAL": 
Re-enter password for principal "ec2-user/ip-172-17-2-130.ec2.internal@EC2.INTERNAL": 
Principal "ec2-user/ip-172-17-2-130.ec2.internal@EC2.INTERNAL" created.

kadmin.local:  addprinc ec2-user/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
WARNING: no policy specified for ec2-user/ip-172-17-2-96.ec2.internal@EC2.INTERNAL; defaulting to no policy
Enter password for principal "ec2-user/ip-172-17-2-96.ec2.internal@EC2.INTERNAL": 
Re-enter password for principal "ec2-user/ip-172-17-2-96.ec2.internal@EC2.INTERNAL": 
Principal "ec2-user/ip-172-17-2-96.ec2.internal@EC2.INTERNAL" created.
```
- Verify. 
```
kadmin.local:  listprincs
HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
K/M@EC2.INTERNAL
ec2-user/ip-172-17-2-110.ec2.internal@EC2.INTERNAL ---> as long as we can see this line and the following 2 lines
ec2-user/ip-172-17-2-130.ec2.internal@EC2.INTERNAL ---> that means we are done.
ec2-user/ip-172-17-2-96.ec2.internal@EC2.INTERNAL  --->
hdfs/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
hdfs/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
hdfs/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
kadmin/admin@EC2.INTERNAL
kadmin/changepw@EC2.INTERNAL
kadmin/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
kiprop/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
krbtgt/EC2.INTERNAL@EC2.INTERNAL
root/admin@EC2.INTERNAL

```

## create a home HDFS dir for `ec2-user`
- load hdfs keytab. The following commands can be executed with the account `hdfs` on namenode `172.17.2.110`.

```
[hdfs@ip-172-17-2-110 ~]$ kinit -k -t /opt/kerberos-hadoop/keytabs/hdfs.keytab  hdfs/ip-172-17-2-110.ec2.internal@EC2.INTERNAL

```
- verify
```
[hdfs@ip-172-17-2-110 ~]$ klist
Ticket cache: FILE:/tmp/hdfs_krb5cc
Default principal: hdfs/ip-172-17-2-110.ec2.internal@EC2.INTERNAL

Valid starting       Expires              Service principal
07/01/2022 14:20:25  07/02/2022 14:20:25  krbtgt/EC2.INTERNAL@EC2.INTERNAL
	renew until 07/08/2022 14:20:25

```
- create hdfs home dir
```
[hdfs@ip-172-17-2-110 ~]$ /opt/kerberos-hadoop/latest/bin/hdfs dfs -mkdir /user/ec2-user
```
- set up owner&group
```
[hdfs@ip-172-17-2-110 ~]$ /opt/kerberos-hadoop/latest/bin/hdfs dfs -chown ec2-user:ec2-user /user/ec2-user
```
- verify
```
[hdfs@ip-172-17-2-110 ~]$ /opt/kerberos-hadoop/latest/bin/hdfs dfs -ls /user
Found 2 items
drwxr-xr-x   - ec2-user ec2-user            0 2022-06-27 14:33 /user/ec2-user
```

## try to upload a file with the account `ec2-user`
- login as `ec2-user`
```
[ec2-user@ip-172-17-2-110 ~]$ whoami
ec2-user
```
- ensure /home/ec2-user/.bash_profile has following statements. P.S KRB5CCNAME, HADOOP_HOME and HADOOP_CONF_DIR must be correct
```
[ec2-user@ip-172-17-2-110 ~]$ vim .bash_profile 
# .bash_profile

# Get the aliases and functions
if [ -f ~/.bashrc ]; then
        . ~/.bashrc
fi

# User specific environment and startup programs
export HADOOP_HOME=/opt/kerberos-hadoop/latest/
export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop/

# User specific environment and startup programs
alias python=python3.7
export PYSPARK_PYTHON=python3.7
export PYSPARK_DRIVER_PYTHON=python3.7
export KRB5CCNAME=/tmp/$USER\_krb5cc
PATH=$PATH:$HOME/.local/bin:$HOME/bin:/opt/kerberos-hadoop/hadoop-3.3.3/bin/:/opt/kerberos-hadoop/hadoop-3.3.3/sbin/

export PATH


```
- execute kinit
```
[ec2-user@ip-172-17-2-110 ~]$ kinit ec2-user/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
Password for ec2-user/ip-172-17-2-110.ec2.internal@EC2.INTERNAL: 
```
- verify token
```
[ec2-user@ip-172-17-2-110 ~]$ klist
Ticket cache: FILE:/tmp/ec2-user_krb5cc
Default principal: ec2-user/ip-172-17-2-110.ec2.internal@EC2.INTERNAL

Valid starting       Expires              Service principal
07/01/2022 14:27:32  07/02/2022 14:27:32  krbtgt/EC2.INTERNAL@EC2.INTERNAL
	renew until 07/08/2022 14:27:32

```

- upload a file `README.txt`

```
[ec2-user@ip-172-17-2-110 ~]$ cat README.txt 
I love you
[ec2-user@ip-172-17-2-110 ~]$ /opt/kerberos-hadoop/latest/bin/hdfs  dfs -put README.txt /user/ec2-user/
```

- verify whether we can see the file
```
[ec2-user@ip-172-17-2-110 ~]$ hdfs dfs -ls  /user/ec2-user
Found 1 items
-rw-r--r--   1 ec2-user ec2-user         11 2022-07-01 14:30 /user/ec2-user/README.txt
[ec2-user@ip-172-17-2-110 ~]$ hdfs dfs -cat  /user/ec2-user/README.txt
I love you

```
---
## How can I genate a keytab for `ec2-user`?
- use `kadmin` to write a file `ec2-user.keytab` on KDC (`172.17.1.212`)
```
[root@ip-172-17-1-212 ~]# kadmin.local
Authenticating as principal root/admin@EC2.INTERNAL with password.
kadmin.local:  listprincs
HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
K/M@EC2.INTERNAL
ec2-user/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
ec2-user/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
ec2-user/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
hdfs/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
hdfs/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
hdfs/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
kadmin/admin@EC2.INTERNAL
kadmin/changepw@EC2.INTERNAL
kadmin/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
kiprop/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
krbtgt/EC2.INTERNAL@EC2.INTERNAL
root/admin@EC2.INTERNAL

kadmin.local:  xst -k ec2-user.keytab ec2-user/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
Entry for principal ec2-user/ip-172-17-2-110.ec2.internal@EC2.INTERNAL with kvno 2, encryption type aes256-cts-hmac-sha1-96 added to keytab WRFILE:ec2-user.keytab.
Entry for principal ec2-user/ip-172-17-2-110.ec2.internal@EC2.INTERNAL with kvno 2, encryption type aes128-cts-hmac-sha1-96 added to keytab WRFILE:ec2-user.keytab.
Entry for principal ec2-user/ip-172-17-2-110.ec2.internal@EC2.INTERNAL with kvno 2, encryption type des3-cbc-sha1 added to keytab WRFILE:ec2-user.keytab.
Entry for principal ec2-user/ip-172-17-2-110.ec2.internal@EC2.INTERNAL with kvno 2, encryption type arcfour-hmac added to keytab WRFILE:ec2-user.keytab.
Entry for principal ec2-user/ip-172-17-2-110.ec2.internal@EC2.INTERNAL with kvno 2, encryption type camellia256-cts-cmac added to keytab WRFILE:ec2-user.keytab.
Entry for principal ec2-user/ip-172-17-2-110.ec2.internal@EC2.INTERNAL with kvno 2, encryption type camellia128-cts-cmac added to keytab WRFILE:ec2-user.keytab.
Entry for principal ec2-user/ip-172-17-2-110.ec2.internal@EC2.INTERNAL with kvno 2, encryption type des-hmac-sha1 added to keytab WRFILE:ec2-user.keytab.
Entry for principal ec2-user/ip-172-17-2-110.ec2.internal@EC2.INTERNAL with kvno 2, encryption type des-cbc-md5 added to keytab WRFILE:ec2-user.keytab.

kadmin.local:  xst -k ec2-user.keytab ec2-user/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
Entry for principal ec2-user/ip-172-17-2-130.ec2.internal@EC2.INTERNAL with kvno 2, encryption type aes256-cts-hmac-sha1-96 added to keytab WRFILE:ec2-user.keytab.
Entry for principal ec2-user/ip-172-17-2-130.ec2.internal@EC2.INTERNAL with kvno 2, encryption type aes128-cts-hmac-sha1-96 added to keytab WRFILE:ec2-user.keytab.
Entry for principal ec2-user/ip-172-17-2-130.ec2.internal@EC2.INTERNAL with kvno 2, encryption type des3-cbc-sha1 added to keytab WRFILE:ec2-user.keytab.
Entry for principal ec2-user/ip-172-17-2-130.ec2.internal@EC2.INTERNAL with kvno 2, encryption type arcfour-hmac added to keytab WRFILE:ec2-user.keytab.
Entry for principal ec2-user/ip-172-17-2-130.ec2.internal@EC2.INTERNAL with kvno 2, encryption type camellia256-cts-cmac added to keytab WRFILE:ec2-user.keytab.
Entry for principal ec2-user/ip-172-17-2-130.ec2.internal@EC2.INTERNAL with kvno 2, encryption type camellia128-cts-cmac added to keytab WRFILE:ec2-user.keytab.
Entry for principal ec2-user/ip-172-17-2-130.ec2.internal@EC2.INTERNAL with kvno 2, encryption type des-hmac-sha1 added to keytab WRFILE:ec2-user.keytab.
Entry for principal ec2-user/ip-172-17-2-130.ec2.internal@EC2.INTERNAL with kvno 2, encryption type des-cbc-md5 added to keytab WRFILE:ec2-user.keytab.

kadmin.local:  xst -k ec2-user.keytab ec2-user/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
Entry for principal ec2-user/ip-172-17-2-96.ec2.internal@EC2.INTERNAL with kvno 2, encryption type aes256-cts-hmac-sha1-96 added to keytab WRFILE:ec2-user.keytab.
Entry for principal ec2-user/ip-172-17-2-96.ec2.internal@EC2.INTERNAL with kvno 2, encryption type aes128-cts-hmac-sha1-96 added to keytab WRFILE:ec2-user.keytab.
Entry for principal ec2-user/ip-172-17-2-96.ec2.internal@EC2.INTERNAL with kvno 2, encryption type des3-cbc-sha1 added to keytab WRFILE:ec2-user.keytab.
Entry for principal ec2-user/ip-172-17-2-96.ec2.internal@EC2.INTERNAL with kvno 2, encryption type arcfour-hmac added to keytab WRFILE:ec2-user.keytab.
Entry for principal ec2-user/ip-172-17-2-96.ec2.internal@EC2.INTERNAL with kvno 2, encryption type camellia256-cts-cmac added to keytab WRFILE:ec2-user.keytab.
Entry for principal ec2-user/ip-172-17-2-96.ec2.internal@EC2.INTERNAL with kvno 2, encryption type camellia128-cts-cmac added to keytab WRFILE:ec2-user.keytab.
Entry for principal ec2-user/ip-172-17-2-96.ec2.internal@EC2.INTERNAL with kvno 2, encryption type des-hmac-sha1 added to keytab WRFILE:ec2-user.keytab.
Entry for principal ec2-user/ip-172-17-2-96.ec2.internal@EC2.INTERNAL with kvno 2, encryption type des-cbc-md5 added to keytab WRFILE:ec2-user.keytab.
kadmin.local:

```
- verify keytab file `ec2-user.keytab`
```
[root@ip-172-17-1-212 ~]# klist -kt ec2-user.keytab 
Keytab name: FILE:ec2-user.keytab
KVNO Timestamp           Principal
---- ------------------- ------------------------------------------------------
   3 07/01/2022 14:40:44 ec2-user/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   3 07/01/2022 14:40:44 ec2-user/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   3 07/01/2022 14:40:44 ec2-user/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   3 07/01/2022 14:40:44 ec2-user/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   3 07/01/2022 14:40:44 ec2-user/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   3 07/01/2022 14:40:44 ec2-user/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   3 07/01/2022 14:40:44 ec2-user/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   3 07/01/2022 14:40:44 ec2-user/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   3 07/01/2022 14:40:51 ec2-user/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   3 07/01/2022 14:40:51 ec2-user/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   3 07/01/2022 14:40:51 ec2-user/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   3 07/01/2022 14:40:51 ec2-user/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   3 07/01/2022 14:40:51 ec2-user/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   3 07/01/2022 14:40:51 ec2-user/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   3 07/01/2022 14:40:51 ec2-user/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   3 07/01/2022 14:40:51 ec2-user/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   3 07/01/2022 14:40:56 ec2-user/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   3 07/01/2022 14:40:56 ec2-user/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   3 07/01/2022 14:40:56 ec2-user/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   3 07/01/2022 14:40:56 ec2-user/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   3 07/01/2022 14:40:56 ec2-user/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   3 07/01/2022 14:40:56 ec2-user/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   3 07/01/2022 14:40:56 ec2-user/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   3 07/01/2022 14:40:56 ec2-user/ip-172-17-2-130.ec2.internal@EC2.INTERNAL

```
- copy this file to /home/ec2-user/ of `172.17.2.110` and change owner
```
# scp ec2-user.keytab 172.17.2.110:/home/ec2-user/
# chown ec2-user:ec2-user ec2-user.keytab 
```
- login as `ec2-user` on `172.17.2.110`
```
[ec2-user@ip-172-17-2-110 ~]$ whoami
ec2-user

```
- execute `kinit`
```
[ec2-user@ip-172-17-2-110 ~]$ kinit -k -t ec2-user.keytab  ec2-user/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
[ec2-user@ip-172-17-2-110 ~]$ klist
Ticket cache: FILE:/tmp/ec2-user_krb5cc
Default principal: ec2-user/ip-172-17-2-110.ec2.internal@EC2.INTERNAL

Valid starting       Expires              Service principal
2022-07-01T14:44:04  2022-07-02T14:44:04  krbtgt/EC2.INTERNAL@EC2.INTERNAL
	renew until 2022-07-08T14:44:04

```
- try to check files in HDFS folder
```
[ec2-user@ip-172-17-2-110 ~]$ hdfs dfs -ls /user/ec2-user
Found 1 items
-rw-r--r--   1 ec2-user ec2-user         11 2022-07-01 14:30 /user/ec2-user/README.txt
[ec2-user@ip-172-17-2-110 ~]$ hdfs dfs -cat  /user/ec2-user/README.txt
I love you

```
