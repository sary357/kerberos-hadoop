# YARN configuration
## create principal for `mapred` and `yarn` for each host
- use kadmin.local on KDC (`172.17.1.212`)
```
[root@ip-172-17-1-212 kerberos]# kadmin.local
Authenticating as principal root/admin@EC2.INTERNAL with password.

kadmin.local:  addprinc yarn/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
WARNING: no policy specified for yarn/ip-172-17-2-110.ec2.internal@EC2.INTERNAL; defaulting to no policy
Enter password for principal "yarn/ip-172-17-2-110.ec2.internal@EC2.INTERNAL": 
Re-enter password for principal "yarn/ip-172-17-2-110.ec2.internal@EC2.INTERNAL": 
Principal "yarn/ip-172-17-2-110.ec2.internal@EC2.INTERNAL" created.

kadmin.local:  addprinc yarn/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
WARNING: no policy specified for yarn/ip-172-17-2-130.ec2.internal@EC2.INTERNAL; defaulting to no policy
Enter password for principal "yarn/ip-172-17-2-130.ec2.internal@EC2.INTERNAL": 
Re-enter password for principal "yarn/ip-172-17-2-130.ec2.internal@EC2.INTERNAL": 
Principal "yarn/ip-172-17-2-130.ec2.internal@EC2.INTERNAL" created.

kadmin.local:  addprinc yarn/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
WARNING: no policy specified for yarn/ip-172-17-2-96.ec2.internal@EC2.INTERNAL; defaulting to no policy
Enter password for principal "yarn/ip-172-17-2-96.ec2.internal@EC2.INTERNAL": 
Re-enter password for principal "yarn/ip-172-17-2-96.ec2.internal@EC2.INTERNAL": 
Principal "yarn/ip-172-17-2-96.ec2.internal@EC2.INTERNAL" created.

kadmin.local:  addprinc mapred/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
WARNING: no policy specified for mapred/ip-172-17-2-110.ec2.internal@EC2.INTERNAL; defaulting to no policy
Enter password for principal "mapred/ip-172-17-2-110.ec2.internal@EC2.INTERNAL": 
Re-enter password for principal "mapred/ip-172-17-2-110.ec2.internal@EC2.INTERNAL": 
Principal "mapred/ip-172-17-2-110.ec2.internal@EC2.INTERNAL" created.

kadmin.local:  addprinc mapred/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
WARNING: no policy specified for mapred/ip-172-17-2-130.ec2.internal@EC2.INTERNAL; defaulting to no policy
Enter password for principal "mapred/ip-172-17-2-130.ec2.internal@EC2.INTERNAL": 
Re-enter password for principal "mapred/ip-172-17-2-130.ec2.internal@EC2.INTERNAL": 
Principal "mapred/ip-172-17-2-130.ec2.internal@EC2.INTERNAL" created.

kadmin.local:  addprinc mapred/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
WARNING: no policy specified for mapred/ip-172-17-2-96.ec2.internal@EC2.INTERNAL; defaulting to no policy
Enter password for principal "mapred/ip-172-17-2-96.ec2.internal@EC2.INTERNAL": 
Re-enter password for principal "mapred/ip-172-17-2-96.ec2.internal@EC2.INTERNAL": 
Principal "mapred/ip-172-17-2-96.ec2.internal@EC2.INTERNAL" created.
```
- Verify whether we have `mapre` and `yarn`'s principals on each host. We must see 6 lines like what we see in the following.
```
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
mapred/ip-172-17-2-110.ec2.internal@EC2.INTERNAL <----- *
mapred/ip-172-17-2-130.ec2.internal@EC2.INTERNAL <----- *
mapred/ip-172-17-2-96.ec2.internal@EC2.INTERNAL  <----- *
root/admin@EC2.INTERNAL
yarn/ip-172-17-2-110.ec2.internal@EC2.INTERNAL   <----- *
yarn/ip-172-17-2-130.ec2.internal@EC2.INTERNAL   <----- *
yarn/ip-172-17-2-96.ec2.internal@EC2.INTERNAL    <----- *
kadmin.local:  
```

## Keytab generation
- generated `yarn-unmerged.keytab` and `mapred-unmerged.keytab`.
```
kadmin.local:  xst -k yarn-unmerged.keytab yarn/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
Entry for principal yarn/ip-172-17-2-110.ec2.internal@EC2.INTERNAL with kvno 2, encryption type aes256-cts-hmac-sha1-96 added to keytab WRFILE:yarn-unmerged.keytab.
Entry for principal yarn/ip-172-17-2-110.ec2.internal@EC2.INTERNAL with kvno 2, encryption type aes128-cts-hmac-sha1-96 added to keytab WRFILE:yarn-unmerged.keytab.
Entry for principal yarn/ip-172-17-2-110.ec2.internal@EC2.INTERNAL with kvno 2, encryption type des3-cbc-sha1 added to keytab WRFILE:yarn-unmerged.keytab.
Entry for principal yarn/ip-172-17-2-110.ec2.internal@EC2.INTERNAL with kvno 2, encryption type arcfour-hmac added to keytab WRFILE:yarn-unmerged.keytab.
Entry for principal yarn/ip-172-17-2-110.ec2.internal@EC2.INTERNAL with kvno 2, encryption type camellia256-cts-cmac added to keytab WRFILE:yarn-unmerged.keytab.
Entry for principal yarn/ip-172-17-2-110.ec2.internal@EC2.INTERNAL with kvno 2, encryption type camellia128-cts-cmac added to keytab WRFILE:yarn-unmerged.keytab.
Entry for principal yarn/ip-172-17-2-110.ec2.internal@EC2.INTERNAL with kvno 2, encryption type des-hmac-sha1 added to keytab WRFILE:yarn-unmerged.keytab.
Entry for principal yarn/ip-172-17-2-110.ec2.internal@EC2.INTERNAL with kvno 2, encryption type des-cbc-md5 added to keytab WRFILE:yarn-unmerged.keytab.

kadmin.local:  xst -k yarn-unmerged.keytab yarn/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
Entry for principal yarn/ip-172-17-2-130.ec2.internal@EC2.INTERNAL with kvno 2, encryption type aes256-cts-hmac-sha1-96 added to keytab WRFILE:yarn-unmerged.keytab.
Entry for principal yarn/ip-172-17-2-130.ec2.internal@EC2.INTERNAL with kvno 2, encryption type aes128-cts-hmac-sha1-96 added to keytab WRFILE:yarn-unmerged.keytab.
Entry for principal yarn/ip-172-17-2-130.ec2.internal@EC2.INTERNAL with kvno 2, encryption type des3-cbc-sha1 added to keytab WRFILE:yarn-unmerged.keytab.
Entry for principal yarn/ip-172-17-2-130.ec2.internal@EC2.INTERNAL with kvno 2, encryption type arcfour-hmac added to keytab WRFILE:yarn-unmerged.keytab.
Entry for principal yarn/ip-172-17-2-130.ec2.internal@EC2.INTERNAL with kvno 2, encryption type camellia256-cts-cmac added to keytab WRFILE:yarn-unmerged.keytab.
Entry for principal yarn/ip-172-17-2-130.ec2.internal@EC2.INTERNAL with kvno 2, encryption type camellia128-cts-cmac added to keytab WRFILE:yarn-unmerged.keytab.
Entry for principal yarn/ip-172-17-2-130.ec2.internal@EC2.INTERNAL with kvno 2, encryption type des-hmac-sha1 added to keytab WRFILE:yarn-unmerged.keytab.
Entry for principal yarn/ip-172-17-2-130.ec2.internal@EC2.INTERNAL with kvno 2, encryption type des-cbc-md5 added to keytab WRFILE:yarn-unmerged.keytab.

kadmin.local:  xst -k yarn-unmerged.keytab yarn/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
Entry for principal yarn/ip-172-17-2-96.ec2.internal@EC2.INTERNAL with kvno 2, encryption type aes256-cts-hmac-sha1-96 added to keytab WRFILE:yarn-unmerged.keytab.
Entry for principal yarn/ip-172-17-2-96.ec2.internal@EC2.INTERNAL with kvno 2, encryption type aes128-cts-hmac-sha1-96 added to keytab WRFILE:yarn-unmerged.keytab.
Entry for principal yarn/ip-172-17-2-96.ec2.internal@EC2.INTERNAL with kvno 2, encryption type des3-cbc-sha1 added to keytab WRFILE:yarn-unmerged.keytab.
Entry for principal yarn/ip-172-17-2-96.ec2.internal@EC2.INTERNAL with kvno 2, encryption type arcfour-hmac added to keytab WRFILE:yarn-unmerged.keytab.
Entry for principal yarn/ip-172-17-2-96.ec2.internal@EC2.INTERNAL with kvno 2, encryption type camellia256-cts-cmac added to keytab WRFILE:yarn-unmerged.keytab.
Entry for principal yarn/ip-172-17-2-96.ec2.internal@EC2.INTERNAL with kvno 2, encryption type camellia128-cts-cmac added to keytab WRFILE:yarn-unmerged.keytab.
Entry for principal yarn/ip-172-17-2-96.ec2.internal@EC2.INTERNAL with kvno 2, encryption type des-hmac-sha1 added to keytab WRFILE:yarn-unmerged.keytab.
Entry for principal yarn/ip-172-17-2-96.ec2.internal@EC2.INTERNAL with kvno 2, encryption type des-cbc-md5 added to keytab WRFILE:yarn-unmerged.keytab.

kadmin.local:  xst -k mapred-unmerged.keytab mapred/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
Entry for principal mapred/ip-172-17-2-110.ec2.internal@EC2.INTERNAL with kvno 2, encryption type aes256-cts-hmac-sha1-96 added to keytab WRFILE:mapred-unmerged.keytab.
Entry for principal mapred/ip-172-17-2-110.ec2.internal@EC2.INTERNAL with kvno 2, encryption type aes128-cts-hmac-sha1-96 added to keytab WRFILE:mapred-unmerged.keytab.
Entry for principal mapred/ip-172-17-2-110.ec2.internal@EC2.INTERNAL with kvno 2, encryption type des3-cbc-sha1 added to keytab WRFILE:mapred-unmerged.keytab.
Entry for principal mapred/ip-172-17-2-110.ec2.internal@EC2.INTERNAL with kvno 2, encryption type arcfour-hmac added to keytab WRFILE:mapred-unmerged.keytab.
Entry for principal mapred/ip-172-17-2-110.ec2.internal@EC2.INTERNAL with kvno 2, encryption type camellia256-cts-cmac added to keytab WRFILE:mapred-unmerged.keytab.
Entry for principal mapred/ip-172-17-2-110.ec2.internal@EC2.INTERNAL with kvno 2, encryption type camellia128-cts-cmac added to keytab WRFILE:mapred-unmerged.keytab.
Entry for principal mapred/ip-172-17-2-110.ec2.internal@EC2.INTERNAL with kvno 2, encryption type des-hmac-sha1 added to keytab WRFILE:mapred-unmerged.keytab.
Entry for principal mapred/ip-172-17-2-110.ec2.internal@EC2.INTERNAL with kvno 2, encryption type des-cbc-md5 added to keytab WRFILE:mapred-unmerged.keytab.

kadmin.local:  xst -k mapred-unmerged.keytab mapred/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
Entry for principal mapred/ip-172-17-2-130.ec2.internal@EC2.INTERNAL with kvno 2, encryption type aes256-cts-hmac-sha1-96 added to keytab WRFILE:mapred-unmerged.keytab.
Entry for principal mapred/ip-172-17-2-130.ec2.internal@EC2.INTERNAL with kvno 2, encryption type aes128-cts-hmac-sha1-96 added to keytab WRFILE:mapred-unmerged.keytab.
Entry for principal mapred/ip-172-17-2-130.ec2.internal@EC2.INTERNAL with kvno 2, encryption type des3-cbc-sha1 added to keytab WRFILE:mapred-unmerged.keytab.
Entry for principal mapred/ip-172-17-2-130.ec2.internal@EC2.INTERNAL with kvno 2, encryption type arcfour-hmac added to keytab WRFILE:mapred-unmerged.keytab.
Entry for principal mapred/ip-172-17-2-130.ec2.internal@EC2.INTERNAL with kvno 2, encryption type camellia256-cts-cmac added to keytab WRFILE:mapred-unmerged.keytab.
Entry for principal mapred/ip-172-17-2-130.ec2.internal@EC2.INTERNAL with kvno 2, encryption type camellia128-cts-cmac added to keytab WRFILE:mapred-unmerged.keytab.
Entry for principal mapred/ip-172-17-2-130.ec2.internal@EC2.INTERNAL with kvno 2, encryption type des-hmac-sha1 added to keytab WRFILE:mapred-unmerged.keytab.
Entry for principal mapred/ip-172-17-2-130.ec2.internal@EC2.INTERNAL with kvno 2, encryption type des-cbc-md5 added to keytab WRFILE:mapred-unmerged.keytab.

kadmin.local:  xst -k mapred-unmerged.keytab mapred/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
Entry for principal mapred/ip-172-17-2-96.ec2.internal@EC2.INTERNAL with kvno 2, encryption type aes256-cts-hmac-sha1-96 added to keytab WRFILE:mapred-unmerged.keytab.
Entry for principal mapred/ip-172-17-2-96.ec2.internal@EC2.INTERNAL with kvno 2, encryption type aes128-cts-hmac-sha1-96 added to keytab WRFILE:mapred-unmerged.keytab.
Entry for principal mapred/ip-172-17-2-96.ec2.internal@EC2.INTERNAL with kvno 2, encryption type des3-cbc-sha1 added to keytab WRFILE:mapred-unmerged.keytab.
Entry for principal mapred/ip-172-17-2-96.ec2.internal@EC2.INTERNAL with kvno 2, encryption type arcfour-hmac added to keytab WRFILE:mapred-unmerged.keytab.
Entry for principal mapred/ip-172-17-2-96.ec2.internal@EC2.INTERNAL with kvno 2, encryption type camellia256-cts-cmac added to keytab WRFILE:mapred-unmerged.keytab.
Entry for principal mapred/ip-172-17-2-96.ec2.internal@EC2.INTERNAL with kvno 2, encryption type camellia128-cts-cmac added to keytab WRFILE:mapred-unmerged.keytab.
Entry for principal mapred/ip-172-17-2-96.ec2.internal@EC2.INTERNAL with kvno 2, encryption type des-hmac-sha1 added to keytab WRFILE:mapred-unmerged.keytab.
Entry for principal mapred/ip-172-17-2-96.ec2.internal@EC2.INTERNAL with kvno 2, encryption type des-cbc-md5 added to keytab WRFILE:mapred-unmerged.keytab.

```

- merge mapred-unmerged.keytab and yarn-unmerged.keytab with `HTTP.keytab`
```
[root@ip-172-17-1-212 kerberos]# ktutil 
ktutil:  rkt mapred-unmerged.keytab 
ktutil:  rkt HTTP.keytab 
ktutil:  wkt mapred.keytab
ktutil:  clear
ktutil:  rkt yarn-unmerged.keytab 
ktutil:  rkt HTTP.keytab 
ktutil:  wkt yarn.keytab
ktutil:  clear
ktutil:  exit
```

- Verify content in keytab
```
[root@ip-172-17-1-212 kerberos]# klist -kt mapred.keytab
Keytab name: FILE:mapred.keytab
KVNO Timestamp           Principal
---- ------------------- ------------------------------------------------------
   2 07/02/2022 14:48:24 mapred/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 mapred/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 mapred/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 mapred/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 mapred/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 mapred/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 mapred/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 mapred/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 mapred/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 mapred/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 mapred/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 mapred/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 mapred/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 mapred/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 mapred/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 mapred/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 mapred/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 mapred/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 mapred/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 mapred/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 mapred/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 mapred/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 mapred/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 mapred/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:24 HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL

[root@ip-172-17-1-212 kerberos]#  klist -kt yarn.keytab 
Keytab name: FILE:yarn.keytab
KVNO Timestamp           Principal
---- ------------------- ------------------------------------------------------
   2 07/02/2022 14:48:48 yarn/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 yarn/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 yarn/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 yarn/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 yarn/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 yarn/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 yarn/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 yarn/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 yarn/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 yarn/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 yarn/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 yarn/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 yarn/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 yarn/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 yarn/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 yarn/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 yarn/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 yarn/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 yarn/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 yarn/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 yarn/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 yarn/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 yarn/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 yarn/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 HTTP/ip-172-17-2-96.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 HTTP/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
   2 07/02/2022 14:48:48 HTTP/ip-172-17-2-130.ec2.internal@EC2.INTERNAL
```

- Deploy these 2 keytab into `/opt/kerberos-hadoop/keytabs/` of each host (172.17.2.110, 172.17.2.130, 172.17.2.96). (I use 172.17.2.110 as an example here).
```
# scp mapred.keytab 172.17.2.110:/opt/kerberos-hadoop/keytabs/
# scp yarn.keytab 172.17.2.110:/opt/kerberos-hadoop/keytabs/
# ssh 172.17.2.110 "chown mapred:hadoop /opt/kerberos-hadoop/keytabs/mapred.keytab; chown yarn:hadoop /opt/kerberos-hadoop/keytabs/yarn.keytab"
```

## Modify configuration on (172.17.2.110, 172.17.2.130, 172.17.2.96)
- /opt/hadoop/latest/etc/hadoop/mapred-site.xml  on 172.17.2.110, 172.17.2.130, 172.17.2.96
```

<?xml version="1.0"?>
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
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
    <property>
        <name>mapreduce.application.classpath</name>
        <value>$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/*:$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/lib/*</value>
    </property>

    <property>
        <name>mapreduce.jobhistory.address</name>
        <value>0.0.0.0:10020</value>
    </property>
    <property>
        <name>mapreduce.jobhistory.keytab</name>
        <value>/opt/kerberos-hadoop/keytabs/mapred.keytab</value>
    </property>
    <property>
        <name>mapreduce.jobhistory.principal</name>
        <value>mapred/_HOST@EC2.INTERNAL</value>
    </property>
</configuration>

```
- yarn-site.xml on 172.17.2.110, 172.17.2.130, 172.17.2.96
```
<?xml version="1.0"?>
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
<configuration>

<!-- Site specific YARN configuration properties -->
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
    <property>
        <name>yarn.nodemanager.env-whitelist</name>
        <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_HOME,PATH,LANG,TZ,HADOOP_MAPRED_HOME</value>
    </property>

    <!-- kerberos -->
    <property>
        <name>yarn.resourcemanager.scheduler.address</name>
        <value>ip-172-17-2-110.ec2.internal:8030</value>
    </property>
    <property>
        <name>yarn.nodemanager.pmem-check-enabled</name>
        <value>false</value>
    </property>
    <property>
        <name>yarn.nodemanager.vmem-check-enabled</name>
        <value>false</value>
    </property>
    <property>
        <name>yarn.resourcemanager.keytab</name>
	<value>/opt/kerberos-hadoop/keytabs/yarn.keytab</value>
    </property>
    <property>
        <name>yarn.resourcemanager.principal</name>
        <value>yarn/_HOST@EC2.INTERNAL</value>
    </property>
    <property>
        <name>yarn.nodemanager.keytab</name>
	<value>/opt/kerberos-hadoop/keytabs/yarn.keytab</value>
    </property>
    <property>
        <name>yarn.nodemanager.principal</name>
        <value>yarn/_HOST@EC2.INTERNAL</value>
    </property>
    <property>
        <name>yarn.resourcemanager.address</name>
        <value>ip-172-17-2-110.ec2.internal:8032</value>
    </property>
    <property>
        <name>yarn.resourcemanager.resource-tracker.address</name>
        <value>ip-172-17-2-110.ec2.internal:8031</value>
    </property>

</configuration>
```
- /opt/kerberos-hadoop/latest/etc/hadoop/hadoop-en.sh on 172.17.2.110, 172.17.2.130, 172.17.2.96
```
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk/
export HADOOP_OS_TYPE=${HADOOP_OS_TYPE:-$(uname -s)}
export HADOOP_PID_DIR=/var/run/hadoop
export JSVC_HOME=/opt/kerberos-hadoop/jsvc
export HDFS_DATANODE_SECURE_USER=hdfs
export HADOOP_SECURE_PID_DIR=/var/run/hadoop
export HADOOP_SECURE_LOG_DIR=/var/run/hadoop
export YARN_RESOURCEMANAGER_USER=yarn
export YARN_NODEMANAGER_USER=yarn
```


## Start YARN cluster
### start resource manager
- logon 172.17.2.110 with `yarn`
```
[yarn@ip-172-17-2-110 bin]$  whoami
whoami

```
- go to `/opt/kerberos-hadoop/hadoop-3.3.3/sbin/`
```
[yarn@ip-172-17-2-110 bin]$# cd /opt/kerberos-hadoop/hadoop-3.3.3/bin/
```
- start resource manager
```
[yarn@ip-172-17-2-110 bin]$ ./yarn --daemon start resourcemanager
```
- verify with `ps`
```
[yarn@ip-172-17-2-110 bin]$ ps axuww |grep -i ResourceManager
yarn      6130  0.0  0.0 119440   952 pts/0    S+   15:53   0:00 grep --color=auto -i ResourceManager
yarn     31535  100 14.6 2499920 291572 pts/0  Sl   15:42  11:00 /usr/lib/jvm/java-1.8.0-openjdk//bin/java -Dproc_resourcemanager -Djava.net.preferIPv4Stack=true -Dservice.libdir=/opt/kerberos-hadoop/hadoop-3.3.3/share/hadoop/yarn,/opt/kerberos-hadoop/hadoop-3.3.3/share/hadoop/yarn/lib,/opt/kerberos-hadoop/hadoop-3.3.3/share/hadoop/hdfs,/opt/kerberos-hadoop/hadoop-3.3.3/share/hadoop/hdfs/lib,/opt/kerberos-hadoop/hadoop-3.3.3/share/hadoop/common,/opt/kerberos-hadoop/hadoop-3.3.3/share/hadoop/common/lib -Dyarn.log.dir=/opt/kerberos-hadoop/hadoop-3.3.3/logs -Dyarn.log.file=hadoop-yarn-resourcemanager-ip-172-17-2-110.ec2.internal.log -Dyarn.home.dir=/opt/kerberos-hadoop/hadoop-3.3.3 -Dyarn.root.logger=INFO,console -Djava.library.path=/opt/kerberos-hadoop/hadoop-3.3.3/lib/native -Dhadoop.log.dir=/opt/kerberos-hadoop/hadoop-3.3.3/logs -Dhadoop.log.file=hadoop-yarn-resourcemanager-ip-172-17-2-110.ec2.internal.log -Dhadoop.home.dir=/opt/kerberos-hadoop/hadoop-3.3.3 -Dhadoop.id.str=yarn -Dhadoop.root.logger=INFO,RFA -Dhadoop.policy.file=hadoop-policy.xml -Dhadoop.security.logger=INFO,NullAppender org.apache.hadoop.yarn.server.resourcemanager.ResourceManager
```
- verify  log /opt/kerberos-hadoop/latest/logs/hadoop-yarn-resourcemanager-ip-172-17-2-110.ec2.internal.log
```
[yarn@ip-172-17-2-110 bin]$ vim /opt/kerberos-hadoop/latest/logs/hadoop-yarn-resourcemanager-ip-172-17-2-110.ec2.internal.log 
``` 

### start node manager
- logon 172.17.2.110 with `yarn
```
[yarn@ip-172-17-2-96 bin]$ whoami
yarn
```
- go to /opt/kerberos-hadoop/latest/bin
```
[yarn@ip-172-17-2-96 bin]$ cd /opt/kerberos-hadoop/latest/bin

```
- start node manager
```
[yarn@ip-172-17-2-96 bin]$ ./yarn --daemon start nodemanager
```
- verify with `ps`
```
[yarn@ip-172-17-2-96 bin]$ ps xauww |grep NodeManager
yarn     13522  5.4 13.0 2358540 260280 pts/0  Sl   15:46   0:10 /usr/lib/jvm/java-1.8.0-openjdk//bin/java -Dproc_nodemanager -Djava.net.preferIPv4Stack=true -Dyarn.log.dir=/opt/kerberos-hadoop/hadoop-3.3.3/logs -Dyarn.log.file=hadoop-yarn-nodemanager-ip-172-17-2-96.ec2.internal.log -Dyarn.home.dir=/opt/kerberos-hadoop/hadoop-3.3.3 -Dyarn.root.logger=INFO,console -Djava.library.path=/opt/kerberos-hadoop/hadoop-3.3.3/lib/native -Dhadoop.log.dir=/opt/kerberos-hadoop/hadoop-3.3.3/logs -Dhadoop.log.file=hadoop-yarn-nodemanager-ip-172-17-2-96.ec2.internal.log -Dhadoop.home.dir=/opt/kerberos-hadoop/hadoop-3.3.3 -Dhadoop.id.str=yarn -Dhadoop.root.logger=INFO,RFA -Dhadoop.policy.file=hadoop-policy.xml -Dhadoop.security.logger=INFO,NullAppender org.apache.hadoop.yarn.server.nodemanager.NodeManager

```
- verify log `/opt/kerberos-hadoop/latest/logs/hadoop-yarn-nodemanager-ip-172-17-2-96.ec2.internal.log`
```
[yarn@ip-172-17-2-96 bin]$ vim /opt/kerberos-hadoop/latest/logs/hadoop-yarn-nodemanager-ip-172-17-2-96.ec2.internal.log 
```

## Execute a map reduce job
- use `hdfs` to modify permission of `/` and create `/tmp/`
```
[hdfs@ip-172-17-2-110 keytabs]$ kinit -k -t /opt/kerberos-hadoop/keytabs/hdfs.keytab  hdfs/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
[hdfs@ip-172-17-2-110 keytabs]$ hdfs
hdfs      hdfs.cmd  
[hdfs@ip-172-17-2-110 keytabs]$ hdfs dfs -chmod 775 /
[hdfs@ip-172-17-2-110 keytabs]$ hdfs dfs -mkdir /tmp
[hdfs@ip-172-17-2-110 keytabs]$ hdfs dfs -chmod 777  /tmp

```
- logon with `ec2-user` and execute `kinit`
```
[ec2-user@ip-172-17-2-110 ~]$ kinit -kt ec2-user.keytab ec2-user/ip-172-17-2-110.ec2.internal@EC2.INTERNAL
[ec2-user@ip-172-17-2-110 ~]$ klist
Ticket cache: FILE:/tmp/ec2-user_krb5cc
Default principal: ec2-user/ip-172-17-2-110.ec2.internal@EC2.INTERNAL

Valid starting       Expires              Service principal
07/02/2022 15:57:47  07/03/2022 15:57:47  krbtgt/EC2.INTERNAL@EC2.INTERNAL
	renew until 07/09/2022 15:57:47

```
- run sample map reduce job - Pi
```
[ec2-user@ip-172-17-2-110 ~]$ hadoop jar /opt/kerberos-hadoop/latest/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.3.jar pi 10 10
Number of Maps  = 10
Samples per Map = 10
Wrote input for Map #0
Wrote input for Map #1
Wrote input for Map #2
Wrote input for Map #3
Wrote input for Map #4
Wrote input for Map #5
Wrote input for Map #6
Wrote input for Map #7
Wrote input for Map #8
Wrote input for Map #9
Starting Job
2022-07-02 16:07:51,727 INFO client.DefaultNoHARMFailoverProxyProvider: Connecting to ResourceManager at ip-172-17-2-110.ec2.internal/172.17.2.110:8032
2022-07-02 16:07:52,151 INFO hdfs.DFSClient: Created token for ec2-user: HDFS_DELEGATION_TOKEN owner=ec2-user/ip-172-17-2-110.ec2.internal@EC2.INTERNAL, renewer=yarn, realUser=, issueDate=1656778072146, maxDate=1657382872146, sequenceNumber=3, masterKeyId=7 on 172.17.2.110:9000
2022-07-02 16:07:52,208 INFO security.TokenCache: Got dt for hdfs://ip-172-17-2-110.ec2.internal:9000; Kind: HDFS_DELEGATION_TOKEN, Service: 172.17.2.110:900
```
