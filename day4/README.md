# DO-280-vodafone-15thDec_2025

### version check with arch discussion 

```
[ec2-user@openshift ~]$ oc get nodes
NAME                          STATUS   ROLES                  AGE    VERSION
ip-10-0-28-245.ec2.internal   Ready    worker                 2d1h   v1.29.5+29c95f3
ip-10-0-4-37.ec2.internal     Ready    control-plane,master   2d1h   v1.29.5+29c95f3
ip-10-0-55-78.ec2.internal    Ready    worker                 2d1h   v1.29.5+29c95f3
ip-10-0-59-36.ec2.internal    Ready    control-plane,master   2d1h   v1.29.5+29c95f3
ip-10-0-67-253.ec2.internal   Ready    control-plane,master   2d1h   v1.29.5+29c95f3
ip-10-0-68-70.ec2.internal    Ready    worker                 2d1h   v1.29.5+29c95f3
[ec2-user@openshift ~]$ oc version 
Client Version: 4.16.0
Kustomize Version: v5.0.4-0.20230601165947-6ce0bf390ce3
Server Version: 4.16.0
Kubernetes Version: v1.29.5+29c95f3
[ec2-user@openshift ~]$ kubectl  version 
Client Version: v1.29.1
Kustomize Version: v5.0.4-0.20230601165947-6ce0bf390ce3
Server Version: v1.29.5+29c95f3
[ec2-user@openshift ~]$ 

```
### master node components 

<img src="cp1.png">

### check ocp internal pods 

```
345  oc get pods -A   | grep -i etcd
  346  oc get pods -A   | grep -i sche
  347  oc get pods -A   | grep -i apiser
```

### secret in ocp 

```
oc create  secret 
Create a secret with specified type.

 A docker-registry type secret is for accessing a container registry.

 A generic type secret indicate an Opaque secret type.

 A tls type secret holds TLS certificate and its associated key.

Available Commands:
  docker-registry   Create a secret for use with a Docker registry
  generic           Create a secret from a local file, directory, or literal value
  tls               Create a TLS secret


```

### storing my db password in oc secret 

```
oc create  secret  generic  ashu-db-password  --from-literal  ashudbpass="MYdbPass12345"  --dry-run=client -o yaml 
apiVersion: v1
data:
  ashudbpass: TVlkYlBhc3MxMjM0NQ==
kind: Secret
metadata:
  creationTimestamp: null
  name: ashu-db-password


[ec2-user@openshift ashu-db-deploy]$ oc create  secret  generic  ashu-db-password  --from-literal  ashudbpass="MYdbPass12345"  --dry-run=client -o yaml   >db-secret.yaml 


[ec2-user@openshift ashu-db-deploy]$ ls
db-deploy.yaml  db-secret.yaml


[ec2-user@openshift ashu-db-deploy]$ oc create -f db-secret.yaml 
secret/ashu-db-password created


[ec2-user@openshift ashu-db-deploy]$ oc get secrets
NAME                       TYPE                      DATA   AGE
ashu-db-password           Opaque                    1      3s
builder-dockercfg-wkw92    kubernetes.io/dockercfg   1      17h

```

### using configMap to store data 

```
oc create  configmap ashu-cm1  --from-literal  dbname=ashudb --dry-run=client -o yaml 
apiVersion: v1
data:
  dbname: ashudb
kind: ConfigMap
metadata:
  creationTimestamp: null
  name: ashu-cm1
[ec2-user@openshift ashu-db-deploy]$ oc create  configmap ashu-cm1  --from-literal  dbname=ashudb --dry-run=client -o yaml >dbcm.yaml
[ec2-user@openshift ashu-db-deploy]$ oc create -f dbcm.yaml 
configmap/ashu-cm1 created
[ec2-user@openshift ashu-db-deploy]$ oc get configmap 
NAME                       DATA   AGE
ashu-cm1                   1      6s
kube-root-ca.crt           1      18h

```
### finally doing db deployment 

```
 oc get deploy 
No resources found in ashu-project namespace.
[ec2-user@openshift ashu-db-deploy]$ ls
dbcm.yaml  db-deploy.yaml  db-secret.yaml
[ec2-user@openshift ashu-db-deploy]$ 
[ec2-user@openshift ashu-db-deploy]$ 
[ec2-user@openshift ashu-db-deploy]$ oc create -f db-deploy.yaml 
deployment.apps/ashu-db created
[ec2-user@openshift ashu-db-deploy]$ oc get deploy 
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
ashu-db   1/1     1            1           7s
[ec2-user@openshift ashu-db-deploy]$ oc get pod
NAME                     READY   STATUS    RESTARTS   AGE
ashu-db-cd579776-ss57d   1/1     Running   0          11s
[ec2-user@openshift ashu-db-deploy]$ oc logs  ashu-db-cd579776-ss57d  
2026-01-14 06:34:00+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 9.5.0-1.el9 started.
2026-01-14 06:34:01+00:00 [Note] [Entrypoint]: Initializing database files
2026-01-14T06:34:01.076850Z 0 [System] [MY-015017] [Server] MySQL Server Initialization - start.
2026-01-14T06:34:01.078998Z 0 [System] [MY-013169] [Server] /usr/sbin/mysqld (mysqld 9

```

### login with db to check details 

```
oc get pods
NAME                     READY   STATUS    RESTARTS   AGE
ashu-db-cd579776-ss57d   1/1     Running   0          19m

[ec2-user@openshift ashu-db-deploy]$ 

[ec2-user@openshift ashu-db-deploy]$ oc rsh  ashu-db-cd579776-ss57d
sh-5.1$ 
sh-5.1$ mysql -u root -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 9
Server version: 9.5.0 MySQL Community Server - GPL

Copyright (c) 2000, 2025, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| ashudb             |
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.004 sec)

mysql> exit;
Bye
sh-5.1$ exit
exit
[ec2-user@openshift ashu-db-deploy]$ 

```

### checking memory 

```
 oc adm top pod 
NAME                       CPU(cores)   MEMORY(bytes)   
ashu-db-57bcbb7488-nqwhp   3m           437Mi           
[ec2-user@openshift ~]$ oc adm top node 
NAME                          CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%   
ip-10-0-28-245.ec2.internal   277m         7%     3896Mi          26%       
ip-10-0-4-37.ec2.internal     1228m        35%    11451Mi         77%       
ip-10-0-55-78.ec2.internal    504m         14%    6551Mi          44%       
ip-10-0-59-36.ec2.internal    1507m        43%    6584Mi          44%       
ip-10-0-67-253.ec2.internal   1014m        28%    9991Mi          67%       
ip-10-0-68-70.ec2.internal    492m         14%    6147Mi          41%       
[ec2-user@openshift ~]$ 

```

### storage in ocp 

<img src="st1.png">


### understanding pv and pvc

<img src="st2.png">

### to dynamically create pv we have storage class 

```
 oc  get  storageclass
NAME                PROVISIONER       RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
gp2-csi             ebs.csi.aws.com   Delete          WaitForFirstConsumer   true                   2d5h
gp3-csi (default)   ebs.csi.aws.com   Delete          WaitForFirstConsumer   true                   2d5h
[ec2-user@openshift ~]$ oc  get pv
No resources found
[ec2-user@openshift ~]$ 



```
