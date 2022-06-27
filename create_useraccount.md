## Assumption
- I need to create a user account `ec2-user` and its group is also `ec2-user`.
- all keytabs for hdfs, mapred, and spark are in `/opt/kerberos-hadoop/keytabs`.
- REALM: EC2.INTERNAL
- host I'm working: ip-172-17-1-212.ec2.internal. But we need to add principals for all hosts in reality.
- hadoop folder: `/opt/kerberos-hadoop/latest/`

## Account creation
- Execute the following command to create an account `ec2-user` and set up password. Be sure to use `root` to execute.
```
# whoami
root
# useradd ec2-user
# passwd ec2-user
```
## Generate a principal for the account `ec2-user`
- execute the following to generate principal for `ec2-user`. As long as you remember `kadmin` password, you can use any account to execute the following commands.
```
# kadmin
Authenticating as principal root/admin@EC2.INTERNAL with password.
Password for root/admin@EC2.INTERNAL: 
kadmin:  listprincs
HTTP/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
K/M@EC2.INTERNAL
hdfs/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
kadmin/admin@EC2.INTERNAL
kadmin/changepw@EC2.INTERNAL
kadmin/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
kiprop/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
krbtgt/EC2.INTERNAL@EC2.INTERNAL
root/admin@EC2.INTERNAL

kadmin:  addprinc ec2-user/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
WARNING: no policy specified for ec2-user/ip-172-17-1-212.ec2.internal@EC2.INTERNAL; defaulting to no policy
Enter password for principal "ec2-user/ip-172-17-1-212.ec2.internal@EC2.INTERNAL": 
Re-enter password for principal "ec2-user/ip-172-17-1-212.ec2.internal@EC2.INTERNAL": 
Principal "ec2-user/ip-172-17-1-212.ec2.internal@EC2.INTERNAL" created.

kadmin:  listprincs
HTTP/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
K/M@EC2.INTERNAL
ec2-user/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
hdfs/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
kadmin/admin@EC2.INTERNAL
kadmin/changepw@EC2.INTERNAL
kadmin/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
kiprop/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
krbtgt/EC2.INTERNAL@EC2.INTERNAL
root/admin@EC2.INTERNAL

```

## create a home HDFS dir for ec2-user
- ensure HADOOP_HOME and HADOOP_CONF_DIR must be correct
```
$ export HADOOP_HOME=/opt/kerberos-hadoop/latest/
$ export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop/
```
- load hdfs keytab

```
# kinit -k -t /opt/kerberos-hadoop/keytabs/hdfs.keytab  hdfs/ip-172-17-1-212.ec2.internal@EC2.INTERNAL

```
- verify
```
# klist
Ticket cache: FILE:/tmp/root_krb5cc
Default principal: hdfs/ip-172-17-1-212.ec2.internal@EC2.INTERNAL

Valid starting       Expires              Service principal
06/27/2022 14:49:05  06/28/2022 14:49:05  krbtgt/EC2.INTERNAL@EC2.INTERNAL
	renew until 07/04/2022 14:49:05

```
- create hdfs home dir
```
# /opt/kerberos-hadoop/latest/bin/hdfs dfs -mkdir /user/ec2-user
```
- set up owner&group
```
# /opt/kerberos-hadoop/latest/bin/hdfs dfs chown ec2-user:ec2-user /user/ec2-user
```
- verify
```
# /opt/kerberos-hadoop/latest/bin/hdfs dfs -ls /user
Found 2 items
drwxr-xr-x   - ec2-user ec2-user            0 2022-06-27 14:33 /user/ec2-user
```

## try to upload a file
- login as `ec2-user`
```
$ whoami
ec2-user

```
- execute kinit
```
$ kinit ec2-user/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
Password for ec2-user/ip-172-17-1-212.ec2.internal@EC2.INTERNAL: 

```
- verify
```
$ klist
Ticket cache: FILE:/tmp/ec2-user_krb5cc
Default principal: ec2-user/ip-172-17-1-212.ec2.internal@EC2.INTERNAL

Valid starting       Expires              Service principal
2022-06-27T14:56:24  2022-06-28T14:56:24  krbtgt/EC2.INTERNAL@EC2.INTERNAL
	renew until 2022-07-04T14:56:24

```

- upload a file `README.txt`

```
$ /opt/kerberos-hadoop/latest/bin/hdfs  dfs -put README.txt /user/ec2-user/
```

- verify
```
$ /opt/kerberos-hadoop/latest/bin/hdfs dfs -ls  /user/ec2-user
Found 1 items
-rw-r--r--   1 ec2-user ec2-user         61 2022-06-27 14:59 /user/ec2-user/README.txt

```
