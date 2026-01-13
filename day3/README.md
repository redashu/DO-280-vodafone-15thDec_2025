# DO-280-vodafone-15thDec_2025

### Revision 

<img src="rev1.png">

### login to openshift cluster using oc cli 

```
oc login  https://api.mayank.openshiftlab.xyz:6443 -u kubeadmin 
WARNING: Using insecure TLS client config. Setting this option is not supported!

Console URL: https://api.mayank.openshiftlab.xyz:6443/console
Authentication required for https://api.mayank.openshiftlab.xyz:6443 (openshift)
Username: kubeadmin
Password: 
Login successful.

You have access to 70 projects, the list has been suppressed. You can list all projects with 'oc projects'

Using project "internal-reg-demo".

```

### login to ocp cluster options 

<img src="login.png">

### listing all the projects 

```
 oc projects 
You have access to the following projects and can switch between them with ' project <projectname>':

    default
  * internal-reg-demo
    kube-node-lease
    kube-public
    kube-system
    openshift
    openshift-apiserver
    openshift-apiserver-operator
    openshift-authentication

```
### more project related things 

```
oc project
Using project "internal-reg-demo" on server "https://api.mayank.openshiftlab.xyz:6443".
[ec2-user@openshift ashu-manifestfiles]$ 
[ec2-user@openshift ashu-manifestfiles]$ 
[ec2-user@openshift ashu-manifestfiles]$ oc project  default 
Now using project "default" on server "https://api.mayank.openshiftlab.xyz:6443".
[ec2-user@openshift ashu-manifestfiles]$ 
[ec2-user@openshift ashu-manifestfiles]$ 
[ec2-user@openshift ashu-manifestfiles]$ 

```

### creating yaml 

```
 oc create deployment  ashu-deploy1  --image dockerashu/ashu-appvodafone:v2  --port 80 --dry-run=client  -o yaml >ashu-deploy.yaml 
```

### creating svc

```
oc create  -f  ashu-deploy.yaml 
deployment.apps/ashu-deploy1 created
[ec2-user@openshift ashu-manifestfiles]$ oc get deploy
NAME                READY   UP-TO-DATE   AVAILABLE   AGE
ashu-deploy1        1/1     1            1           4s
nilesh-app-deploy   1/1     1            1           3m8s
[ec2-user@openshift ashu-manifestfiles]$ oc expose deployment ashu-deploy1 --type ClusterIP --port 1234 --target-port 80 --dry-run=client -o yaml >ashusvc.yaml 
[ec2-user@openshift ashu-manifestfiles]$ ls
ashu-deploy.yaml  ashusvc.yaml
[ec2-user@openshift ashu-manifestfiles]$ oc create -f ashusvc.yaml 
service/ashu-deploy1 created
[ec2-user@openshift ashu-manifestfiles]$ oc get svc
NAME           TYPE           CLUSTER-IP     EXTERNAL-IP                            PORT(S)    AGE
ashu-deploy1   ClusterIP      172.30.82.94   <none>                                 1234/TCP   4s
kubernetes     ClusterIP      172.30.0.1     <none>                                 443/TCP    7m43s
openshift      ExternalName   <none>         kubernetes.default.svc.cluster.local   <none>     6m59s
[ec2-user@openshift ashu-manifestfiles]$ 
```

### creating nodeport service 

```
oc  expose deployment ashu-deploy1  --type NodePort --port 1234 --target-port 80 --name ashulb3 --dry-run=client -o yaml >ashunodeportsvc.yaml 
[ec2-user@openshift ashu-manifestfiles]$ oc create -f ashunodeportsvc.yaml 
service/ashulb3 created
[ec2-user@openshift ashu-manifestfiles]$ oc get svc
NAME               TYPE           CLUSTER-IP       EXTERNAL-IP                            PORT(S)          AGE
ashu-deploy1       ClusterIP      172.30.82.94     <none>                                 1234/TCP         19m
ashulb3            NodePort       172.30.53.52     <none>                                 1234:31367/TCP   3s

```

## checking openshift Ingress controller (openshift router)

```
oc get pods -n openshift-ingress
NAME                             READY   STATUS    RESTARTS   AGE
router-default-d97f4f994-94p6l   1/1     Running   1          27h
router-default-d97f4f994-zpg6b   1/1     Running   1          27h
[ec2-user@openshift ~]$ 
[ec2-user@openshift ~]$ 
[ec2-user@openshift ~]$ oc get deploy  -n openshift-ingress
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
router-default   2/2     2            2           27h
[ec2-user@openshift ~]$ oc get svc  -n openshift-ingress
NAME                      TYPE           CLUSTER-IP       EXTERNAL-IP                                                               PORT(S)                      AGE
router-default            LoadBalancer   172.30.229.136   aae8d8f17b1ed4e978fbe909da8c7407-1826622764.us-east-1.elb.amazonaws.com   80:32117/TCP,443:32622/TCP   27h
router-internal-default   ClusterIP      172.30.253.66    <none>                                                                    80/TCP,443/TCP,1936/TCP      27h

```

### openshift routing rules 

<img src="rules.png">

### creating route 

```
oc create route  edge ashu-app-route --service ashu-deploy1  --port 1234
```
