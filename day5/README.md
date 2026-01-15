# DO-280-vodafone-15thDec_2025

### some important oc commands 

```
 oc whoami
kube:admin
[ec2-user@openshift ~]$ oc whoami --show-console 

https://console-openshift-console.apps.mayank.openshiftlab.xyz
[ec2-user@openshift ~]$ 
[ec2-user@openshift ~]$ 
[ec2-user@openshift ~]$ 

[ec2-user@openshift ~]$ oc whoami --show-server
https://api.mayank.openshiftlab.xyz:6443


```

### basic podman commands

```
c2-user@openshift ~]$ podman ps
CONTAINER ID  IMAGE       COMMAND     CREATED     STATUS      PORTS       NAMES
[ec2-user@openshift ~]$ podman ps -a
CONTAINER ID  IMAGE       COMMAND     CREATED     STATUS      PORTS       NAMES
[ec2-user@openshift ~]$ podman pull alpine 
Resolved "alpine" as an alias (/etc/containers/registries.conf.d/000-shortnames.conf)
Trying to pull docker.io/library/alpine:latest...
Getting image source signatures
Copying blob 1074353eec0d done   | 
Copying config e7b39c54cd done   | 
Writing manifest to image destination
e7b39c54cdeca0d2aae83114bb605753a5f5bc511fe8be7590e38f6d9f915dad
[ec2-user@openshift ~]$ podman run -itd --name x1  alpine sleep 10000 
0bc7ee479b7f0f32ad1edd25fd13bd29b131acbd51f16ee3477b70029957079d
[ec2-user@openshift ~]$ podman ps 
CONTAINER ID  IMAGE                            COMMAND      CREATED        STATUS        PORTS       NAMES
0bc7ee479b7f  docker.io/library/alpine:latest  sleep 10000  3 seconds ago  Up 3 seconds              x1
[ec2-user@openshift ~]$ 

```

## Understanding Helm 

<img src="helm1.png">

### using events 

```
 oc get events 
LAST SEEN   TYPE     REASON                   OBJECT                           MESSAGE
2m37s       Normal   Scheduled                pod/ashu-db-7bf96f445-jghs4      Successfully assigned ashu-project/ashu-db-7bf96f445-jghs4 to ip-10-0-55-78.ec2.internal
2m34s       Normal   SuccessfulAttachVolume   pod/ashu-db-7bf96f445-jghs4      AttachVolume.Attach succeeded for volume "pvc-aa9e7a81-c3c8-4d8d-aa9a-cfcf9153bc19"
2m29s       Normal   AddedInterface           pod/ashu-db-7bf96f445-jghs4      Add eth0 [10.131.0.19/23] from ovn-kubernetes
2m29s       Normal   Pulled                   pod/ashu-db-7bf96f445-jghs4      Container image "vodaserver.azurecr.io/mysql:v1" already present on machine
2m28s       Normal   Created                  pod/ashu-db-7bf96f445-jghs4      Created container mysql
2m28s       Normal   Started                  pod/ashu-db-7bf96f445-jghs4      Started container mysql

```

### CRD in k8s / ocp 

<img src="crd1.png">

### checking cluster health with /healthz url 

#### Note ocp cluster operator are the reason 

```
[ec2-user@openshift ~]$ oc get --raw  /healthz 
ok[ec2-user@openshift ~]$ 
[ec2-user@openshift ~]$ 
[ec2-user@openshift ~]$ oc get  co 
NAME                                       VERSION   AVAILABLE   PROGRESSING   DEGRADED   SINCE   MESSAGE
authentication                             4.16.0    True        False         False      2d1h    
baremetal                                  4.16.0    True        False         False      3d3h    
cloud-controller-manager                   4.16.0    True        False         False      3d3h    
cloud-credential                           4.16.0    True        False         False      3d3h    
cluster-autoscaler                         4.16.0    True        False         False      3d3h    

```

### few more commands 

```
677  oc get --raw  / 
  678  oc get --raw  /healthz 
  679  oc get  co 
  680  oc get  co  etcd  
  681  oc describe   co  etcd  
  682  oc projects  | grep -i apiserver
  683  oc get po -n openshift-kube-apiserver
```

