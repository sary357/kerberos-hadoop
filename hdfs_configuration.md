# HDFS configuraton
## Principal generation
- generate principals for `hdfs` and `HTTP`
```
[root@ip-172-17-1-212 ~]# kadmin 
Authenticating as principal root/admin@EC2.INTERNAL with password.
Password for root/admin@EC2.INTERNAL: 
kadmin: addprinc -randkey hdfs/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
kadmin: addprinc -randkey HTTP/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
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

```
- You can use commands like
```
# kadmin.local -q "addprinc -randkey hdfs/ip-172-17-1-212.ec2.internal@EC2.INTERNAL"
# kadmin.local -q "addprinc -randkey HTTP/ip-172-17-1-212.ec2.internal@EC2.INTERNAL"
```
## keytab file generation
- execute the following commands
```
root@ip-172-17-1-212 tmp]# kadmin.local -q "xst -k hdfs-unmerged.keytab hdfs/ip-172-17-1-212.ec2.internal@EC2.INTERNAL"
Authenticating as principal root/admin@EC2.INTERNAL with password.
Entry for principal hdfs/ip-172-17-1-212.ec2.internal@EC2.INTERNAL with kvno 3, encryption type aes256-cts-hmac-sha1-96 added to keytab WRFILE:hdfs-unmerged.keytab.
Entry for principal hdfs/ip-172-17-1-212.ec2.internal@EC2.INTERNAL with kvno 3, encryption type aes128-cts-hmac-sha1-96 added to keytab WRFILE:hdfs-unmerged.keytab.
Entry for principal hdfs/ip-172-17-1-212.ec2.internal@EC2.INTERNAL with kvno 3, encryption type des3-cbc-sha1 added to keytab WRFILE:hdfs-unmerged.keytab.
Entry for principal hdfs/ip-172-17-1-212.ec2.internal@EC2.INTERNAL with kvno 3, encryption type arcfour-hmac added to keytab WRFILE:hdfs-unmerged.keytab.
Entry for principal hdfs/ip-172-17-1-212.ec2.internal@EC2.INTERNAL with kvno 3, encryption type camellia256-cts-cmac added to keytab WRFILE:hdfs-unmerged.keytab.
Entry for principal hdfs/ip-172-17-1-212.ec2.internal@EC2.INTERNAL with kvno 3, encryption type camellia128-cts-cmac added to keytab WRFILE:hdfs-unmerged.keytab.
Entry for principal hdfs/ip-172-17-1-212.ec2.internal@EC2.INTERNAL with kvno 3, encryption type des-hmac-sha1 added to keytab WRFILE:hdfs-unmerged.keytab.
Entry for principal hdfs/ip-172-17-1-212.ec2.internal@EC2.INTERNAL with kvno 3, encryption type des-cbc-md5 added to keytab WRFILE:hdfs-unmerged.keytab.

[root@ip-172-17-1-212 tmp]# kadmin.local -q "xst -k HTTP.keytab HTTP/ip-172-17-1-212.ec2.internal@EC2.INTERNAL"
Authenticating as principal root/admin@EC2.INTERNAL with password.
Entry for principal HTTP/ip-172-17-1-212.ec2.internal@EC2.INTERNAL with kvno 3, encryption type aes256-cts-hmac-sha1-96 added to keytab WRFILE:HTTP.keytab.
Entry for principal HTTP/ip-172-17-1-212.ec2.internal@EC2.INTERNAL with kvno 3, encryption type aes128-cts-hmac-sha1-96 added to keytab WRFILE:HTTP.keytab.
Entry for principal HTTP/ip-172-17-1-212.ec2.internal@EC2.INTERNAL with kvno 3, encryption type des3-cbc-sha1 added to keytab WRFILE:HTTP.keytab.
Entry for principal HTTP/ip-172-17-1-212.ec2.internal@EC2.INTERNAL with kvno 3, encryption type arcfour-hmac added to keytab WRFILE:HTTP.keytab.
Entry for principal HTTP/ip-172-17-1-212.ec2.internal@EC2.INTERNAL with kvno 3, encryption type camellia256-cts-cmac added to keytab WRFILE:HTTP.keytab.
Entry for principal HTTP/ip-172-17-1-212.ec2.internal@EC2.INTERNAL with kvno 3, encryption type camellia128-cts-cmac added to keytab WRFILE:HTTP.keytab.
Entry for principal HTTP/ip-172-17-1-212.ec2.internal@EC2.INTERNAL with kvno 3, encryption type des-hmac-sha1 added to keytab WRFILE:HTTP.keytab.
Entry for principal HTTP/ip-172-17-1-212.ec2.internal@EC2.INTERNAL with kvno 3, encryption type des-cbc-md5 added to keytab WRFILE:HTTP.keytab.

```
- verify them
```
[root@ip-172-17-1-212 tmp]# ls -l hdfs-unmerged.keytab HTTP.keytab 
-rw------- 1 root root 738 Jun 28 14:39 hdfs-unmerged.keytab
-rw------- 1 root root 738 Jun 28 14:40 HTTP.keytab
```
- merge these keytab files and generate a file `hdfs.keytab`
```
[root@ip-172-17-1-212 tmp]# cd /var/kerberos/krb5kdc/
[root@ip-172-17-1-212 tmp]# ktutil
ktutil: rkt hdfs-unmerged.keytab
ktutil: rkt HTTP.keytab
ktutil: wkt hdfs.keytab
```
- verify `hdfs.keytab`.
```
[root@ip-172-17-1-212 tmp]# klist -kt /var/kerberos/krb5kdc/hdfs.keytab 
Keytab name: FILE:/var/kerberos/krb5kdc/hdfs.keytab
KVNO Timestamp           Principal
---- ------------------- ------------------------------------------------------
   2 06/26/2022 14:42:16 hdfs/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
   2 06/26/2022 14:42:16 hdfs/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
   2 06/26/2022 14:42:16 hdfs/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
   2 06/26/2022 14:42:16 hdfs/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
   2 06/26/2022 14:42:16 hdfs/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
   2 06/26/2022 14:42:16 hdfs/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
   2 06/26/2022 14:42:16 hdfs/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
   2 06/26/2022 14:42:16 hdfs/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
   2 06/26/2022 14:42:16 HTTP/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
   2 06/26/2022 14:42:16 HTTP/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
   2 06/26/2022 14:42:16 HTTP/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
   2 06/26/2022 14:42:16 HTTP/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
   2 06/26/2022 14:42:16 HTTP/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
   2 06/26/2022 14:42:16 HTTP/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
   2 06/26/2022 14:42:16 HTTP/ip-172-17-1-212.ec2.internal@EC2.INTERNAL
   2 06/26/2022 14:42:16 HTTP/ip-172-17-1-212.ec2.internal@EC2.INTERNAL

```
- put `hdfs.keytab` in `/opt/kerberos-hadoop/keytabs/hdfs.keytab`
```
[root@ip-172-17-1-212 tmp]# cp /var/kerberos/krb5kdc/hdfs.keytab /opt/kerberos-hadoop/keytabs/hdfs.keytab
[root@ip-172-17-1-212 tmp]# chown hdfs:hadoop  /opt/kerberos-hadoop/keytabs/hdfs.keytab
```

## modify hadoop config files
- /opt/kerberos-hadoop/latest/etc/hadoop/core-site.xml
```
[root@ip-172-17-1-212 hadoop]# vim /opt/kerberos-hadoop/latest/etc/hadoop/core-site.xml 
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://ip-172-17-1-212.ec2.internal:9000</value>
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
-  /opt/kerberos-hadoop/latest/etc/hadoop/hadoop-env.sh
```
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk
export HADOOP_OS_TYPE=${HADOOP_OS_TYPE:-$(uname -s)}
export JSVC_HOME=/opt/kerberos-hadoop/jsvc
export HDFS_DATANODE_SECURE_USER=hdfs
export HADOOP_SECURE_PID_DIR=/var/run/hadoop
export HADOOP_SECURE_LOG_DIR=/var/run/hadoop
```
- create a folder /var/run/hadoop
```
[root@ip-172-17-1-212 hadoop]# mkdir -p /var/run/hadoop
[root@ip-172-17-1-212 hadoop]# chmod 1777 /var/run/hadoop
```
- /opt/kerberos-hadoop/latest/etc/hadoop/hdfs-site.xml
```
[root@ip-172-17-1-212 hadoop]# vim /opt/kerberos-hadoop/latest/etc/hadoop/hdfs-site.xml 
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
## JSVC
- Download from [Apache Commons](https://commons.apache.org/proper/commons-daemon/download_daemon.cgi)
```
[root@ip-172-17-1-212 tmp]# wget https://dlcdn.apache.org//commons/daemon/source/commons-daemon-1.3.1-src.zip
```
- unzip
```
[root@ip-172-17-1-212 tmp]# unzip commons-daemon-1.3.1-src.zip 

```
- configure
```
[root@ip-172-17-1-212 tmp]# cd commons-daemon-1.3.1-src/src/native/unix/
[root@ip-172-17-1-212 unix]# ./configure && make
```
- We will get a file `jsvc`. Copy this file to $JSVC_HOME(/opt/kerberos-hadoop/jsvc)
```
[root@ip-172-17-1-212 tmp]# mkdir /opt/kerberos-hadoop/jsvc
[root@ip-172-17-1-212 unix]# cp jsvc   /opt/kerberos-hadoop/jsvc
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

- use accpount `root` to start secure datanode
```
[root@ip-172-17-1-212 unix]# cd /opt/kerberos-hadoop/hadoop-3.3.3/sbin/
[root@ip-172-17-1-212 sbin]# ./start-secure-dns.sh
```

# References
- https://codertw.com/%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80/406868/
- https://ravi-chamarthy.medium.com/apache-hadoop-multi-node-kerberized-cluster-setup-9b6508c71794
- https://ravi-chamarthy.medium.com/kerberos-setup-for-apache-hadoop-multi-node-cluster-6bd8a2fbe680
- https://ravi-chamarthy.medium.com/hadoop-hdfs-and-yarn-kerberos-based-configuration-d23d286fdbcc
