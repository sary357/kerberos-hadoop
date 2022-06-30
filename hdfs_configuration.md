# HDFS configuraton
## Principal generation on `172.17.1.212`
- generate principals for `hdfs` and `HTTP` on EACH host(172.17.2.110, 172.17.2.130, and 172.17.2.96).  You can use commands like
```
# kadmin.local -q "addprinc -randkey hdfs/ip-172-17-2-110.ec2.internal@EC2.INTERNAL"
# kadmin.local -q "addprinc -randkey hdfs/ip-172-17-2-130.ec2.internal@EC2.INTERNAL"
# kadmin.local -q "addprinc -randkey hdfs/ip-172-17-2-96.ec2.internal@EC2.INTERNAL"
# kadmin.local -q "addprinc -randkey HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL"
# kadmin.local -q "addprinc -randkey HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL"
# kadmin.local -q "addprinc -randkey HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL"
# kadmin.local -q "listprincs"
```
## keytab file generation
- execute the following commands to generate hdfs-unmerged.keytab
```
[root@ip-172-17-1-212 kerberos]# kadmin.local -q "xst -k hdfs-unmerged.keytab hdfs/ip-172-17-2-110.ec2.internal@EC2.INTERNAL"
[root@ip-172-17-1-212 kerberos]# kadmin.local -q "xst -k hdfs-unmerged.keytab hdfs/ip-172-17-2-130.ec2.internal@EC2.INTERNAL"
[root@ip-172-17-1-212 kerberos]# kadmin.local -q "xst -k hdfs-unmerged.keytab hdfs/ip-172-17-2-96.ec2.internal@EC2.INTERNAL"
[root@ip-172-17-1-212 kerberos]# klist -kt hdfs-unmerged.keytab  # verify keytab 
Keytab name: FILE:hdfs-unmerged.keytab
KVNO Timestamp           Principal
---- ------------------- ------------------------------------------------------
   2 06/30/2022 00:51:48 hdfs/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 06/30/2022 00:51:48 hdfs/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 06/30/2022 00:51:48 hdfs/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 06/30/2022 00:51:48 hdfs/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 06/30/2022 00:51:48 hdfs/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 06/30/2022 00:51:48 hdfs/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 06/30/2022 00:51:48 hdfs/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 06/30/2022 00:51:48 hdfs/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 06/30/2022 00:52:07 hdfs/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 06/30/2022 00:52:07 hdfs/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 06/30/2022 00:52:07 hdfs/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 06/30/2022 00:52:07 hdfs/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 06/30/2022 00:52:07 hdfs/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 06/30/2022 00:52:07 hdfs/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 06/30/2022 00:52:07 hdfs/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 06/30/2022 00:52:07 hdfs/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 06/30/2022 00:52:13 hdfs/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 06/30/2022 00:52:13 hdfs/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 06/30/2022 00:52:13 hdfs/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 06/30/2022 00:52:13 hdfs/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 06/30/2022 00:52:13 hdfs/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 06/30/2022 00:52:13 hdfs/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 06/30/2022 00:52:13 hdfs/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 06/30/2022 00:52:13 hdfs/ip-172-17-2-96.ec2.internal@EC2.INTERNAL

```
- execute the following commands to generate HTTP.keytab
```
[root@ip-172-17-1-212 kerberos]# kadmin.local -q "xst -k HTTP.keytab HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL"
[root@ip-172-17-1-212 kerberos]# kadmin.local -q "xst -k HTTP.keytab HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL"
[root@ip-172-17-1-212 kerberos]# kadmin.local -q "xst -k HTTP.keytab HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL"
[root@ip-172-17-1-212 kerberos]# klist -kt HTTP.keytab        # verify keytab
Keytab name: FILE:HTTP.keytab
KVNO Timestamp           Principal
---- ------------------- ------------------------------------------------------
   2 06/30/2022 01:00:02 HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:00:02 HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:00:02 HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:00:02 HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:00:02 HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:00:02 HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:00:02 HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:00:02 HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:00:08 HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:00:08 HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:00:08 HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:00:08 HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:00:08 HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:00:08 HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:00:08 HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:00:08 HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:00:14 HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:00:14 HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:00:14 HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:00:14 HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:00:14 HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:00:14 HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:00:14 HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:00:14 HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL

```

- merge these keytab files and generate a file `hdfs.keytab`
```
[root@ip-172-17-1-212 tmp]# ktutil
ktutil: rkt hdfs-unmerged.keytab
ktutil: rkt HTTP.keytab
ktutil: wkt hdfs.keytab
```
- verify `hdfs.keytab`.
```
[root@ip-172-17-1-212 tmp]# klist -kt /var/kerberos/krb5kdc/hdfs.keytab 
Keytab name: FILE:hdfs.keytab
KVNO Timestamp           Principal
---- ------------------- ------------------------------------------------------
   2 06/30/2022 01:15:54 hdfs/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:54 hdfs/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:54 hdfs/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:54 hdfs/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:54 hdfs/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:54 hdfs/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:54 hdfs/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:54 hdfs/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:54 hdfs/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:54 hdfs/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:54 hdfs/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:54 hdfs/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:54 hdfs/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:54 hdfs/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:54 hdfs/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:54 hdfs/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:54 hdfs/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:54 hdfs/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:54 hdfs/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:54 hdfs/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:54 hdfs/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:54 hdfs/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:54 hdfs/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:54 hdfs/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:54 HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:54 HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:54 HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:54 HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:54 HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:55 HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:55 HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:55 HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:55 HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:55 HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:55 HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:55 HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:55 HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:55 HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:55 HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:55 HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:55 HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:55 HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:55 HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:55 HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:55 HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:55 HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:55 HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 06/30/2022 01:15:55 HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL

```
- verify again
```
[root@ip-172-17-1-212 kerberos]# kinit  -k -t  hdfs.keytab hdfs/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
[root@ip-172-17-1-212 kerberos]# klist
Ticket cache: FILE:/tmp/root_krb5cc
Default principal: hdfs/ip-172-17-2-110.ec2.internal@EC2.INTERNAL

Valid starting       Expires              Service principal
06/30/2022 01:48:58  07/01/2022 01:48:58  krbtgt/EC2.INTERNAL@EC2.INTERNAL
	renew until 07/07/2022 01:48:58

[root@ip-172-17-1-212 kerberos]# kinit  -k -t  hdfs.keytab HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
[root@ip-172-17-1-212 kerberos]# klist
Ticket cache: FILE:/tmp/root_krb5cc
Default principal: HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL

Valid starting       Expires              Service principal
06/30/2022 01:49:16  07/01/2022 01:49:16  krbtgt/EC2.INTERNAL@EC2.INTERNAL
	renew until 07/07/2022 01:49:16

```
## Hadoop package deployment
- On each host (`172.17.2.110`, `172.17.2.130`, `172.17.2.96`), we need to download hadoop package.
- Please execute the following commands with `root` on `172.17.2.110`, `172.17.2.130`, `172.17.2.96`. (I assume the latest hadoop version is 3.3.3)
```
# HADOOP_VERSION=3.3.3
# cd /tmp
# wget https://dlcdn.apache.org/hadoop/common/hadoop-$HADOOP_VERSION/hadoop-$HADOOP_VERSION.tar.gz
# tar xzf hadoop-$HADOOP_VERSION.tar.gz
# mkdir /opt/kerberos-hadoop/
# mv hadoop-$HADOOP_VERSION /opt/kerberos-hadoop/
# chown hadoop:hadoop -R  /opt/kerberos-hadoop/hadoop-$HADOOP_VERSION
# ln -s /opt/kerberos-hadoop/hadoop-$HADOOP_VERSION /opt/kerberos-hadoop/latest
# mkdir -p /opt/kerberos-hadoop/keytabs/
# chown hadoop:hadoop /opt/kerberos-hadoop/keytabs/
# chmod g+w  /opt/kerberos-hadoop/keytabs/
# mkdir -p /var/run/hadoop
# chown hadoop:hadoop /var/run/hadoop
# chmod g+w /var/run/hadoop
# chmod 1777 /var/run/hadoop
# mkdir -p /opt/kerberos-hadoop/jsvc
# chown hadoop:hadoop /opt/kerberos-hadoop/jsvc
# mkdir -p /opt/kerberos-hadoop/data/
# chown hdfs:hadoop  /opt/kerberos-hadoop/data/
```

## keytab configuration (Cont'd)
- deploy `hdfs.keytab` from KDC host(`172.17.1.212`) to `/opt/kerberos-hadoop/keytabs/hdfs.keytab` of `172.17.2.110`, `172.17.2.130`, `172.17.2.96`.
```
[root@ip-172-17-1-212 tmp]# scp hdfs.keytab root@172.17.2.110:/opt/kerberos-hadoop/keytabs/hdfs.keytab
[root@ip-172-17-1-212 tmp]# scp hdfs.keytab root@172.17.2.130:/opt/kerberos-hadoop/keytabs/hdfs.keytab
[root@ip-172-17-1-212 tmp]# scp hdfs.keytab root@172.17.2.96:/opt/kerberos-hadoop/keytabs/hdfs.keytab
[root@ip-172-17-1-212 tmp]# ssh root@172.17.2.110 "chown hdfs:hadoop  /opt/kerberos-hadoop/keytabs/hdfs.keytab"
[root@ip-172-17-1-212 tmp]# ssh root@172.17.2.130 "chown hdfs:hadoop  /opt/kerberos-hadoop/keytabs/hdfs.keytab"
[root@ip-172-17-1-212 tmp]# ssh root@172.17.2.96 "chown hdfs:hadoop  /opt/kerberos-hadoop/keytabs/hdfs.keytab"
```

## modify hadoop config files on `172.17.2.110`, `172.17.2.130`, `172.17.2.96`
- /opt/kerberos-hadoop/latest/etc/hadoop/core-site.xml on `172.17.2.110`, `172.17.2.130`, `172.17.2.96` will look like
```
[root@ip-172-17-2-110 hadoop]# vim /opt/kerberos-hadoop/latest/etc/hadoop/core-site.xml 
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://ip-172-17-2-110.ec2.internal:9000</value>
    </property>

    <!-- kerberos -->
    <property>
        <name>hadoop.security.authentication</name>
        <value>kerberos</value>
    </property>
    <property>
        <name>hadoop.security.authorization</name>
        <value>true</value>
    </property>

</configuration>

``` 
-  /opt/kerberos-hadoop/latest/etc/hadoop/hadoop-env.sh  on `172.17.2.110`, `172.17.2.130`, `172.17.2.96` will look like
```
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk/
export HADOOP_OS_TYPE=${HADOOP_OS_TYPE:-$(uname -s)}
export JSVC_HOME=/opt/kerberos-hadoop/jsvc
export HDFS_DATANODE_SECURE_USER=hdfs
export HADOOP_SECURE_PID_DIR=/var/run/hadoop
export HADOOP_SECURE_LOG_DIR=/var/run/hadoop
```
- /opt/kerberos-hadoop/latest/etc/hadoop/hdfs-site.xml on `172.17.2.110`, `172.17.2.130`, `172.17.2.96` will look like
```
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<!--
  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License. See accompanying LICENSE file.
-->

<!-- Put site-specific property overrides in this file. -->

<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
</property>
    <property>
        <name>dfs.namenode.name.dir</name>
        <value>/opt/kerberos-hadoop/data/dfs/name</value>
</property>
    <property>
        <name>dfs.datanode.data.dir</name>
        <value>/opt/kerberos-hadoop/data/dfs/data</value>
</property>
    <property>
        <name>dfs.namenode.checkpoint.dir</name>
	<value>/opt/kerberos-hadoop/data/dfs/namesecondary</value>
</property>

<!-- kerberos -->
    <property>
        <name>dfs.permissions.enabled</name>
	<value>true</value>
</property>
    <property>
        <name>dfs.datanode.use.datanode.hostname</name>
	<value>true</value>
</property>
    <property>
        <name>dfs.namenode.datanode.registration.ip-hostname-check</name>
	<value>false</value>
</property>
    <property>
        <name>dfs.block.access.token.enable</name>
	<value>true</value>
</property>
    <property>
        <name>dfs.namenode.keytab.file</name>
	<value>/opt/kerberos-hadoop/keytabs/hdfs.keytab</value>
</property>
    <property>
        <name>dfs.namenode.kerberos.principal</name>
	<value>hdfs/_HOST@EC2.INTERNAL</value>
</property>
<property>
    <name>dfs.namenode.kerberos.internal.spnego.principal</name>
    <value>HTTP/_HOST@EC2.INTERNAL</value>
</property>
<property>
    <name>dfs.namenode.kerberos.https.principal</name>
    <value>HTTP/_HOST@EC2.INTERNAL</value>
</property>

<property>
    <name>dfs.secondary.namenode.keytab.file</name>
    <value>/opt/kerberos-hadoop/keytabs/hdfs.keytab</value>
</property>

<property>
    <name>dfs.secondary.namenode.kerberos.principal</name>
    <value>hdfs/_HOST@EC2.INTERNAL</value>
</property>

<property>
    <name>dfs.secondary.namenode.kerberos.internal.spnego.principal</name>
    <value>HTTP/_HOST@EC2.INTERNAL</value>
</property>

<property>
    <name>dfs.datanode.data.dir.perm</name>
    <value>700</value>
</property>

<property>
    <name>dfs.datanode.address</name>
    <value>0.0.0.0:1004</value>
</property>

<property>
    <name>dfs.datanode.http.address</name>
    <value>0.0.0.0:1006</value>
</property>

<property>
    <name>dfs.datanode.keytab.file</name>
    <value>/opt/kerberos-hadoop/keytabs/hdfs.keytab</value>
</property>

<property>
    <name>dfs.datanode.kerberos.principal</name>
    <value>hdfs/_HOST@EC2.INTERNAL</value>
</property>

<property>
    <name>dfs.datanode.kerberos.https.principal</name>
    <value>hdfs/_HOST@EC2.INTERNAL</value>
</property>

<property>
    <name>dfs.web.authentication.kerberos.principal</name>
    <value>HTTP/_HOST@EC2.INTERNAL</value>
</property>

</configuration>

```
- /opt/hadoop/latest/etc/hadoop/workers on `172.17.2.110`, `172.17.2.130`, `172.17.2.96`.
```
[ec2-user@ip-172-17-1-212 hadoop-prep]$ cat workers 
ip-172-17-2-96.ec2.internal
```
- /opt/hadoop/latest/etc/hadoop/masters on `172.17.2.110`, `172.17.2.130`, `172.17.2.96`.
```
[ec2-user@ip-172-17-1-212 hadoop-prep]$ cat masters 
ip-172-17-2-130.ec2.internal
```

## Install JSVC on `172.17.2.110`, `172.17.2.130`, `172.17.2.96`.
- On `172.17.2.110`, `172.17.2.130`, `172.17.2.96` , execute the following commands. They will 1) download jsvc 2) configure&make 3) copy jsvc to  /opt/kerberos-hadoop/jsvc/
```
cd /tmp/
wget https://dlcdn.apache.org//commons/daemon/source/commons-daemon-1.3.1-src.zip
unzip commons-daemon-1.3.1-src.zip
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk/
cd commons-daemon-1.3.1-src/src/native/unix/
./configure   && make
cp jsvc   /opt/kerberos-hadoop/jsvc/
chown hadoop:hadoop  /opt/kerberos-hadoop/jsvc/
```


## start hdfs service
- modify file `/opt/kerberos-hadoop/hadoop-3.3.3/sbin/start-dfs.sh` and comment out the following part
```
# echo "Starting datanodes: use start-secure.sh with root"
# hadoop_uservar_su hdfs datanode "${HADOOP_HDFS_HOME}/bin/hdfs" \
#    --workers \
#    --config "${HADOOP_CONF_DIR}" \
#    --daemon start \
#    datanode ${dataStartOpt}
# (( HADOOP_JUMBO_RETCOUNTER=HADOOP_JUMBO_RETCOUNTER + $? ))

```
- use account `hdfs` to start namenode and secondary namenode
```
[hdfs@ip-172-17-1-212 ~]$ whoami
hdfs

[hdfs@ip-172-17-1-212 ~]$ kinit -k -t /opt/kerberos-hadoop/keytabs/hdfs.keytab hdfs/`hostname`@EC2.INTERNAL
[hdfs@ip-172-17-1-212 ~]$ cd  /opt/kerberos-hadoop/hadoop-3.3.3/sbin/
[hdfs@ip-172-17-1-212 ~]$ ./start-dfs.sh
```
- Please allow `root` to login with ssh and set up root's ssh key

- use account `root` to start secure datanode
```
[root@ip-172-17-1-212 unix]# cd /opt/kerberos-hadoop/hadoop-3.3.3/sbin/
[root@ip-172-17-1-212 sbin]# ./start-secure-dns.sh
```

# References
- https://codertw.com/%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80/406868/
- https://ravi-chamarthy.medium.com/apache-hadoop-multi-node-kerberized-cluster-setup-9b6508c71794
- https://ravi-chamarthy.medium.com/kerberos-setup-for-apache-hadoop-multi-node-cluster-6bd8a2fbe680
- https://ravi-chamarthy.medium.com/hadoop-hdfs-and-yarn-kerberos-based-configuration-d23d286fdbcc
