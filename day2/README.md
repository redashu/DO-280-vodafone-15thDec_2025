# DO-280-vodafone-15thDec_2025

### checking docker thing 

```
humanfirmware@darwin  ~  ssh  ashu@3.210.241.71
ashu@3.210.241.71's password: 
   ,     #_
   ~\_  ####_        Amazon Linux 2023
  ~~  \_#####\
  ~~     \###|
  ~~       \#/ ___   https://aws.amazon.com/linux/amazon-linux-2023
   ~~       V~' '->
    ~~~         /
      ~~._.   _/
         _/ _/
       _/m/'
[ashu@ip-172-31-17-7 ~]$ 
[ashu@ip-172-31-17-7 ~]$ docker  version 
Client:
 Version:           25.0.14
 API version:       1.44
 Go version:        go1.24.11
 Git commit:        0bab007
 Built:             Wed Dec 10 00:00:00 2025
 OS/Arch:           linux/amd64
 Context:           default


```

### pulling all docker images we built 

```
ashu@ip-172-31-17-7 ~]$ docker  images
REPOSITORY   TAG       IMAGE ID   CREATED   SIZE
[ashu@ip-172-31-17-7 ~]$ 
[ashu@ip-172-31-17-7 ~]$ 
[ashu@ip-172-31-17-7 ~]$ docker pull dockerashu/vodafone-apps:versionv1
versionv1: Pulling from dockerashu/vodafone-apps
80fc8f9321b5: Pull complete 
b22766554d6b: Pull complete 
58f2d358b447: Pull complete 
dd420cee8193: Pull complete 
388c30f2dc56: Pull complete 
5f3f8a41ae1e: Pull complete 
56d5b0133943: Pull complete 
9fc045489f9a: Pull complete 
a3f532665528: Pull complete 
Digest: sha256:568a19c902e825d5a389faa60c5048b768450ad529c0abc3e8062e0b308b52a0
Status: Downloaded newer image for dockerashu/vodafone-apps:versionv1
docker.io/dockerashu/vodafone-apps:versionv1
[ashu@ip-172-31-17-7 ~]$ 
[ashu@ip-172-31-17-7 ~]$ 
[ashu@ip-172-31-17-7 ~]$ docker images
REPOSITORY                 TAG         IMAGE ID       CREATED       SIZE
dockerashu/vodafone-apps   versionv1   e3e553845411   3 weeks ago   1.12GB
[ashu@ip-172-31-17-7 ~]$ 

--->

docker pull dockerashu/ashu-appvodafone:v1
v1: Pulling from dockerashu/ashu-appvodafone
5ba766340b3d: Already exists 
5b219a92f92a: Already exists 
ee3a09d2248a: Already exists 
7382b41547b8: Already exists 
9ee60c6c0558: Already exists 
114e699da838: Already exists 
5b5fa0b64d74: Already exists 
862312bfff11: Pull complete 
Digest: sha256:a9ba4ac217dbafeb8e882bb131e78de3f40d670d5ff2509a57ec67e02a1e101d
Status: Downloaded newer image for dockerashu/ashu-appvodafone:v1
docker.io/dockerashu/ashu-appvodafone:v1
[ashu@ip-172-31-17-7 ~]$ docker images
REPOSITORY                       TAG         IMAGE ID       CREATED       SIZE
dershana/darshu-webappvodafone   version1    bbf02c912118   3 weeks ago   152MB
dockerashu/ashu-appvodafone      v1          a9913b5bf31f   3 weeks ago   152MB
dockerashu/vodafone-apps         versionv1   e3e553845411   3 weeks ago   1.12GB

```