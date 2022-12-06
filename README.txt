1.Get info server comput engine instance-1
modernlife1988@cloudshell:~ (dogwood-concept-370707)$ df -h
Filesystem                         Size  Used Avail Use% Mounted on
overlay                             60G   47G   14G  78% /
tmpfs                               64M     0   64M   0% /dev
tmpfs                              7.9G     0  7.9G   0% /sys/fs/cgroup
/dev/sda1                           60G   47G   14G  78% /root
/dev/disk/by-id/google-home-part1  4.8G   68K  4.6G   1% /home
/dev/root                          2.0G  1.1G  909M  54% /usr/lib/modules
shm                                 64M     0   64M   0% /dev/shm
tmpfs                              3.2G 1004K  3.2G   1% /google/host/var/run

2.Check CPU
modernlife1988@cloudshell:~ (dogwood-concept-370707)$ htop

3.Create instant demo (VM)
modernlife1988@cloudshell:~ (dogwood-concept-370707)$ gcloud compute instances create demo
Did you mean zone [asia-southeast1-a] for instance: [demo] (Y/n)?  Y

Created [https://www.googleapis.com/compute/v1/projects/dogwood-concept-370707/zones/asia-southeast1-a/instances/demo].
NAME: demo
ZONE: asia-southeast1-a
MACHINE_TYPE: n1-standard-1
PREEMPTIBLE:
INTERNAL_IP: 10.148.0.3
EXTERNAL_IP: 34.124.226.188         // IP for access
STATUS: RUNNING

4.SSH to servermodernlife1988@cloudshell:~ (dogwood-concept-370707)$ gcloud compute ssh demo
WARNING: The private SSH key file for gcloud does not exist.
WARNING: The public SSH key file for gcloud does not exist.
WARNING: You do not have an SSH key for gcloud.
WARNING: SSH keygen will be executed to generate a key.
This tool needs to create the directory [/home/modernlife1988/.ssh] before being able to generate SSH keys.

5.Check CPU serve 
modernlife1988@demo:~$ top

6.Update package server
modernlife1988@demo:~$ sudo apt-get update

7.Install git
modernlife1988@demo:~$ sudo apt-get install git

8.Install docker by curl จะลงได้ตาม OS นั้นๆ โดยใช้คำสั่งเหมือนกัน ปกติด แต่ละ OS จะมีวิธีการลงไม่เหมือนกัน
modernlife1988@demo:~$ curl -fsSL get.docker.io

9.Download finsh and install by sh
modernlife1988@demo:~$ curl -fsSL get.docker.io | sh

10.Add my user to group docker for can use docker
modernlife1988@demo:~$ sudo usermod -aG docker modernlife1988

11.exit and ssh again for use docker command
modernlife1988@demo:~$ docker

12.download file from git to learn
modernlife1988@demo:~/demo-nodejs-mongodb-rest$ git clone https://github.com/atisak/demo-nodejs-mongodb-rest.git

13.Install Jenkins (version 2.285)
modernlife1988@demo:~/demo-nodejs-mongodb-rest$ docker run -itd --name jenkins -p 8888:8080 jenkins/jenkins

14.Open Port
(Hamburger) NETWORKING > VPC network > Firewall > Create firewall rule
  Name: demo-jenkins-server
  Target tags: demo-jenkins
  Source filter: IPv4 ranges
  Source Ipv4 ranges: 0.0.0.0/0
  TCP Port: 8888

15.Open Port
(Hamburger) Compute > VM instances
  VM > Name > demo > Edit > Network tags
  Network tags: demo-jenkins

16.Install Docker Compose
modernlife1988@demo:~/demo-nodejs-mongodb-rest$ sudo curl -SL https://github.com/docker/compose/releases/download/v2.14.0/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose

17.Change premistion
modernlife1988@demo:~/demo-nodejs-mongodb-rest$ sudo chmod +x /usr/local/bin/docker-compose

18.Check docker compose can use
modernlife1988@demo:~/demo-nodejs-mongodb-rest$ docker-compose ps
NAME                COMMAND             SERVICE             STATUS              PORTS

19.Use Docker compose with jenkins
modernlife1988@demo:~/demo-nodejs-mongodb-rest$ docker-compose -f jenkins-docker-compose.yml up -d

20.Check Docker jenkins
modernlife1988@demo:~/demo-nodejs-mongodb-rest$ docker ps
CONTAINER ID   IMAGE                    COMMAND                  CREATED          STATUS         PORTS                                                  NAMES
48fef821090b   jenkins/jenkins:latest   "/usr/bin/tini -- /u…"   12 seconds ago   Up 8 seconds   50000/tcp, 0.0.0.0:8888->8080/tcp, :::8888->8080/tcp   demo-nodejs-mongodb-rest-jenkins-1


21.Get password docker have 2 way
URL: http://34.124.226.188:8888/login?from=%2F
modernlife1988@demo:~/demo-nodejs-mongodb-rest$ docker logs -f demo-nodejs-mongodb-rest-jenkins-1
modernlife1988@demo:~/demo-nodejs-mongodb-rest$ docker exec demo-nodejs-mongodb-rest-jenkins-1 cat var/jenlins_home/secrets/inittailAdminPassword


22.Check jenkins can run docker command
modernlife1988@demo:~/demo-nodejs-mongodb-rest$ docker exec -it -u root demo-nodejs-mongodb-rest-jenkins-1 bash           // login with user root
root@48fef821090b:/# docker
root@48fef821090b:/# docker-compose
root@48fef821090b:/# docker ps
root@48fef821090b:/# chown jenkins:jenkins /var/run/docker.sock       // change owner file for user jenkins
root@48fef821090b:/# ls -al /var/run/docker.sock
srw-rw---- 1 jenkins jenkins 0 Dec  5 12:33 /var/run/docker.sock
root@48fef821090b:/# su jenkins                                        // change user root to jenkins
jenkins@48fef821090b:/$ docker ps
CONTAINER ID   IMAGE                    COMMAND                  CREATED          STATUS          PORTS                                                  NAMES
48fef821090b   jenkins/jenkins:latest   "/usr/bin/tini -- /u…"   47 minutes ago   Up 47 minutes   50000/tcp, 0.0.0.0:8888->8080/tcp, :::8888->8080/tcp   demo-nodejs-mongodb-rest-jenkins-1

23.Build docker with Dockerfile
modernlife1988@demo:~/demo-nodejs-mongodb-rest$ cat Dockerfile
FROM node:latest

MAINTAINER atisak.k@gmail.com
#update
RUN apt-get update && apt-get dist-upgrade -y \
    && rm -rf /var/lib/apt/lists/*

COPY . /apps/
WORKDIR /apps/
RUN npm install
EXPOSE 3000
#run command
CMD ["npm","start"]

24.Build file docker
modernlife1988@demo:~/demo-nodejs-mongodb-rest$ docker build -t nongdocker/demo-nodejs .

25.Check docker images
modernlife1988@demo:~/demo-nodejs-mongodb-rest$ docker images
REPOSITORY               TAG       IMAGE ID       CREATED          SIZE
nongdocker/demo-nodejs   latest    7e49db30adb7   16 seconds ago   1.03GB

26.Docker login
modernlife1988@demo:~/demo-nodejs-mongodb-rest$ docker login

28.Docker push image
modernlife1988@demo:~/demo-nodejs-mongodb-rest$ docker push nongdocker/demo-nodejs:latest

29.View log
modernlife1988@demo:~/demo-nodejs-mongodb-rest$ docker logs -f demo-nodejs-mongodb-rest-web-node-js-1

30.Check docker-compose.yml
modernlife1988@demo:~/demo-nodejs-mongodb-rest$ cat docker-compose.yml


