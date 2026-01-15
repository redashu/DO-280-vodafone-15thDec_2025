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

