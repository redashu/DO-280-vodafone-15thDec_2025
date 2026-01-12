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

### OCP architecture layer 

<img src="l1.png">

### building one container image with sample webapp code

```
mkdir   myapps
mkdir: cannot create directory ‘myapps’: File exists
[ashu@ip-172-31-17-7 ~]$ ls
myapps
[ashu@ip-172-31-17-7 ~]$ cd myapps/
[ashu@ip-172-31-17-7 myapps]$ git clone https://github.com/codingstella/vCard-personal-portfolio.git
Cloning into 'vCard-personal-portfolio'...
remote: Enumerating objects: 72, done.
remote: Counting objects: 100% (22/22), done.
remote: Compressing objects: 100% (18/18), done.
remote: Total 72 (delta 9), reused 4 (delta 4), pack-reused 50 (from 1)
Receiving objects: 100% (72/72), 1.13 MiB | 58.01 MiB/s, done.
Resolving deltas: 100% (9/9), done.
[ashu@ip-172-31-17-7 myapps]$ ls
vCard-personal-portfolio
[ashu@ip-172-31-17-7 myapps]$ 

===>
 21  cd vCard-personal-portfolio/
   22  ls
   23  ls -a
   24  touch Dockerfile 
   25  ls
   26  touch  .dockerignore 
   27  ls -a
   28  history 
[ashu@ip-172-31-17-7 vCard-personal-portfolio]$ ls -a
.  ..  .dockerignore  .git  Dockerfile  README.md  assets  index.html  index.txt  website-demo-image
[ashu@ip-172-31-17-7 vCard-personal-portfolio]$ vim .dockerignore 
[ashu@ip-172-31-17-7 vCard-personal-portfolio]$ ls -a
.  ..  .dockerignore  .git  Dockerfile  README.md  assets  index.html  index.txt  website-demo-image
[ashu@ip-172-31-17-7 vCard-personal-portfolio]$ cat .dockerignore 
Dockerfile
.dockerignore
.git
README.md
index.txt

```

### building container image

```
38  docker build -t  ashuapp:v0011 . 
   39  history 
[ashu@ip-172-31-17-7 vCard-personal-portfolio]$ ls
Dockerfile  README.md  assets  index.html  index.txt  website-demo-image
[ashu@ip-172-31-17-7 vCard-personal-portfolio]$ docker images
REPOSITORY                       TAG         IMAGE ID       CREATED          SIZE
ashuapp                          v0011       942889ad2679   10 seconds ago   162MB

```
### app is running in container env 

```
 41  docker images
   42  docker run -itd --name ashuc1  -p 1234:80 ashuapp:v0011
   43  docker ps

```

### stop container and remove 

```
docker  rm ashuc1 -f
```


### pushing image to docker hub 

```
[ashu@ip-172-31-17-7 vCard-personal-portfolio]$ docker  tag  ashuapp:v0011  dockerashu/ashu-appvodafone:v2
[ashu@ip-172-31-17-7 vCard-personal-portfolio]$ docker login 
Log in with your Docker ID or email address to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com/ to create one.
You can log in with your password or a Personal Access Token (PAT). Using a limited-scope PAT grants better security and is required for organizations using SSO. Learn more at https://docs.docker.com/go/access-tokens/

Username: dockerashu
Password: 
WARNING! Your password will be stored unencrypted in /home/ashu/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
[ashu@ip-172-31-17-7 vCard-personal-portfolio]$ docker push dockerashu/ashu-appvodafone:v2
The push refers to repository [docker.io/dockerashu/ashu-appvodafone]
84d2754b6213: Pushed 
7bc3e3f1caf4: Mounted from library/nginx 
a91b21b8651d: Mounted from library/nginx 
f248169098dd: Mounted from library/nginx 
62bbe9e286ea: Mounted from library/nginx 
675cd787fdd9: Mounted from library/nginx 
22851f4f9a0b: Mounted from library/nginx 
6a7f953ae30c: Mounted from library/nginx 
v2: digest: sha256:ac262c567024007d15d828ef82b589ac106157e697a14a4437355ba65f8f8d58 size: 1989
[ashu@ip-172-31-17-7 vCard-personal-portfolio]$ 

```