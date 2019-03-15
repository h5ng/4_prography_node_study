
컨테이너
 - 시스템 컨테이너와 application 컨테이너
 - OS level isolation : 일부 커널의 기능을 개별적으로 isolation하게 지원
 - Cgroup : Control Group을 통한 resource 할당과 통제
 - Application의 실행환경에 필요한 사용자 정보와 Library/binary set
 - Horizontal partition 유형
 - 주로 Application을 빠르게 배포하기 위한 목적에 적합
 - Baremetal과 거의 유사한 성능
 - process group을 독립적으로 관리하기 위한 목적

docker란?
 - 2013년 3월 docker.inc에서 출시한 open source container project.
 - 실행 드라이버를 초창기 LXC 기반으로 구현 -> 0.9부터 libcontainer(native) 기반
 - docker engine 기반 이미지와 컨테이너 방식
 - 특정 실행파일이나 스크립트를 위한 실행환경 제공

특징
 - 이미지 생성과 배포에 특화된 기능 제공
 - 이미지 버전 관리 기능
 - 중앙 개인/공개 저장소 Docker Hub 제공(유/무료)
 - 다양한 API 제공 -> auto provisioning
 - 개발/서버운영에 유용
 - 이식성이 뛰어남
 - 서버 정보 유출 및 피해 최소화(immutable infrastructure)
 - cross cluod platform(AWS, Azure, google...)

docker 설치
 - Ubuntu/CentOS
   배포판별 제공되는 pkg를 이용한 설치
   최근 Docker 버전은 Docker-ce를 설치(자동화 스크립트 제공)
   도커를 실행하기 위한 kernel 버전은 3.10.x 이상 권장
   ubuntu 14.04 이상 사용 권장
 - Windows
   Docker for windows(window 10이상)
   Docker Toolbox(legacy type:Kitematic, Text Terminal)
   Boot2Docker for windows
 - Mac OS
   Docker for Mac
   Docker Toolbox
   Boot2Docker
 - CoreOS

Ubuntu
(ubuntu 16.04)
#curl -s https://get.docker.com/ | sudo sh

이미지 만들기
Docker image란?
 - base image : 부팅에 필요한 실행파일과 라이브러리, packaging 시스템 등을 포함한 최소 파일 모음 --> 리눅스 
   배포판 이름으로 되어 있음
 - docker image : base image에 필요한 프로그램과 라이브러리, 소스를 설치한 하나의 파일
   base image + 변경된 델타 이미지 --> 실행시 함께 load
 - 이미지간 의존성 --> 부모/자식 관계(layer)
 - 16진수 64bit ID 부여
 - 저장소에 upload(부모/자식 이미지)
 - anywhere download(부모/자식 이미지 --> 이후 delta image만 교환)
 - read only
 - 서비스에 필요한 구성을 최적화함
 - 동일 구성으로 단시간에 여러 컨테이너 확보

image list 보기
 - docker images
 - imageID : 12 Hex digits
 - root가 아닌 사용자가 sudo없이 사용하려면 해당 사용자를 docker그룹에 추가
   #usermod -aG docker USER (or useradd -G docker USER)
image 조회
 - docker search ubuntu
 - https://hub.docker.com

image download
 - docker pull <image_name>:<tag>
   ex) docker pull ubuntu:lastest

image 삭제
 - docker rmi <image_name>:<tag>
   tag없이 사용시 관련 이미지 모두 삭제

docker image 생성
 - dockerfile 작성
   구성요소 : #주석, <명령><매개변수>
   <명령> 대소문자 구분하지 않으나 일반적으로 대문자 사용
   FROM으로 반드시 시작
   각 명령은 독립적


#vi Dockerfile
FROM : base image (local 이미지 확인 후 없으면 Docker Hub에서 download)
MAINTAINER : 생성자 정보 ex)hhs, <hhs@hhs.com>
RUN : Run a command ADD : add a file or directory
ENV : Create an environment variable
CMD : What process to run when launching a container from this image
...


 - docker build <option><Dockerfile 경로>
   options : -t or --tag : ex)hhs/hello_world:1.0 (생략시 기본 tag는 latest)
             사용자ID/이미지이름:tag(Docker Hub에 올리기 위해 사용자 ID를 준다. 그렇지 않은 경우는 생략)
   Dockerfile이 있는 위치에서 build 한다.
   Docker는 Dockerfile 구문을 처리하여 이미지를 만들어 return 한다.

docker file 구문
 - .dockerignore file
   context : Dockerfile과 같은 위치에 있는 모든 파일
             build시 docker 엔진에 전송
             불필요한 파일이 없도록 주의
   제외 하고자 하는 파일을 GO언어 규칙을 이용하여 작성해 둔다.

#vi .dockerignore
sub_class/test.txt
sub_class/*.ppt
*.cpp
.snv
.git


 - RUN 명령 | [“<실행파일>”, “<매개변수1>”, “<매개변수2>”... ]
   build시점에 처리하여 이미지로 생성 -> 이미지 history에 기록
   shell script구문 사용가능
   FROM base image에 포함된 /bin/sh를 이용하여 처리
   실행 결과는 caching되며 다음 build시 재사용(--no-cache)
   /bin/sh로 처리하지 않을 경우 배열 처리 ex) RUN ["yum", "install", "-y", "httpd"]

 - CMD 명령 | [“<실행파일>”, “<매개변수1>”, “<매개변수2>”... ]
   container 실행 시점에 처리(docker run | start)
   dockerfile에서 한 번만 사용(main service)
   /bin/sh 기반 실행
   /bin/sh 없이 처리시(CMD [“mysqld", "--datadir=/var/lib/mysql", "..."...]
   ENTRYPOINT와 사용시 매개 변수만 전달하는 기능 ex) ENTRYPOINT["mysqld"]
                                                    CMD ["==datadir=/var/lib/mysql"... ]

 - ENTRYPOINT 명령 | [“<실행파일>”, “<매개변수1>”, “<매개변수2>”... ]
   container 실행 시점에 처리(docker run | start)
   dockerfile에서 한 번만 사용(main service)
   /bin/sh 기반 실행
   /bin/sh 없이 처리시 (ENTRYPOINT ["echo", "I love", "..."...]
   docker run으로 실행 명령을 지정시 CMD라인은 무시되나 ENTRYPOINT는 무시되지 않는다. 
   ex) #docker run test :entry echo I love you
        hello echo I love you
       #docker run test :cmd echo I love you
        I love you   
   --entrypoint를 지정하여 실행하면 무시된다.
   ex) #docker run --entrypoint="cat" test:entry /etc/bosts

 - EXPOSE <port no>...
   host와 연결만을 목적으로 하며 외부에 노출되지 않음
   #docker run --expose
   -p|-P : 외부 포트 설정

 - ENV <환경변수> <value>
   RUN, CMD, ENTRYPOINT에 적용
   $변수 구문으로 사용
   -e로 overwrite 가능
   ex) docker run -e PATH=/tmp/project test:env

 - COPY <source_file> <image안의 절대 path>
   source_file은 Context를 기준으로 한다.(.dockeringore에 등록한 파일 제외)
   context외의 file과 경로는 지정 불가
   절대 path 불가
   file URL 사용 불가
   압축해제 안함
   target path를 /path/와 같이 /로 끝나는 경우 path 생성
   UID/GID는 root, 기존 permission으로 추가

 - VOLUME <container dir> | ["dir1", "dir2"...]
   container에 저장하지 않고 Host에 저장하기 위한 dir를 설정
   host 특정 path와 연결하지 못함 --> docker run -v option으로 연결
   ex) docker run -v /share/app:/app test:vol

 - USER <사용자명>
   명령을 실행할 USER 정의
   정의 후에 처리되는 CMD/RUN/ENTRYPOINT에 적용

 - WORKDIR <절대 path | 상대 path>
   명령을 실행할 위치를 지정
   정의 후에 처리되는 모든 CMD/RUN/ENTRYPOINT에 적용
   중간에 실행 위치를 바꿀 수 있다
   상대 경로는 앞에 정의된 절대 path 기준으로 결졍
   기본 경로는 /를 기준
 - ONBUILD <dockerfile 명령> <명령의 매개변수>...
   이미지를 FROM으로 사용하여 BUILD시 실행할 명령을 예약(trigger 기능)
   FROM/MAINTAINER/ONBUILD를 제외하고 모두 사용가능
   해당 이미지 기반 customizing 용도로 사용

 - 이미지 히스토리 : docker history <image:tag>
 - 컨테이너와 호스트간 file 전송
   docker cp <container name>:<path> <host 경로>
   docker cp <host 경로> <conainer name>:<path>
 - 변경된 컨테이너 file 조회 : docker diff <container name>
 - 변경된 컨테이너 이미지 생성 : codker commit <options> <container name> <image>:<tag>
 - 컨테이너 상세 정보 조회 : docker inspect <container name>

docker conatiner
 - docker image를 실행한 상태
 - 하나의 이미지로 여러 개의 컨테이너 생성
 - Uinon mount(Union filesystem)

docker container 생성과 목록
 - docker run <options> <이미지> <실행 app>
   options
   -i (interactive), -t (terminal mode), -d (daemon 형으로 동작 : background)
   --name : 컨테이너 이름
   --rm : 컨테이너 삭제
   -v host_dir:container_dir : host dir에 data 저장

 - docker ps <options>
   options
   기본 출력은 실행 중인 컨테이너만 출력
   -a : 정지된 컨테이너 포함
   -q : Container ID만 display

docker container 시작과 제거
 - docker [start|restart|stop|...] <컨테이너 id | name>
   start : 컨테이너 시작
   restart : 컨테이너 재시작
   stop : 컨테이너 종료
   pause : 일시 정지
   unpause : 일시 정지 해제

 - docker rm <컨테이너 id | name>
   rm : 정지된 컨테이너 제거

docker container 접속
 - docker attach <container_id | name>
 - docker run -it <이미지> <실행 app>
 - docker exec <컨테이너 id | name> <command> <argument>
   exec : 컨테이너에 접속 없이 host에서 컨테이너에 명령을 실행한다

container registry
 - registry : docker 이미지 저장소 서버
 - 기본 registry : Docker Hub를 사용
 - Docker Hub : Docker 공식 이미지를 제공 / 사용자간 이미지를 공유
 - docker pull/push : 이미지 다운로드/업로드 가능
   #docker pull registry:latest
   #docker run -d -p 5000:5000 --name registry-server -v /registry:/var/lib/registry/registry
   #docker tag test:env localhost:5000/test:env
   #docker push localhost:5000/test:env
   #docker pull registry_server_ip:5000/test:env

container 관리 명령
 - 실시간 events 출력 : #docker events --since '2017-8-2'
 - container의 filesystem을 tar로 저장 : #docker export <container> xxx.tar
 - tar 이미지를 import : #docker import <tar_file>|URL 저장소/이미지:tag
 - container의 process monitoring : #docker top container aux

docker storage(volume 사용하기)
 - docker run -v <Host_dir|file>:<container_dir|file>
   container가 아닌 호스트에 저장하는 방식
   permanent 저장
   container 간 data sharing
   docker commit 시 이미지에 포함되지 않음

docker monitoring
 - build-in 명령
 - graphite : 서버 자원 graphic 모니터링 도구로 여러 도구와 연계하여 사용 가능 
   ex) carbon, graphite web, grafna, elasticsearch, diamond

docker cluster
 - swarm-docker cluster 관리도구 : 표준 docker API를 제공
 - kubernets(google)
 - docker compose
 - Mesos(apache)
 - CoreOs - docker 
   전용 리눅스 배포판
   clustering, 동적확장, 고가용성 제공
   fleet/etcd/systemd로 구성
   vargrant로 손쉽게 cluster 구성

docker and cloud service
 - Iaas(openstack/softlayer/aws/azure...)
 - Paas(bluemix/openshift/cloudfoundry/elastic beantalk...)

docker and openstack
 - nova를 통한 provisioning
 - heat를 통한 provisioning
 - magnum project


  
# Docker 실습 ####



# VirtualBox ####

01. VirtualBox 다운로드 및 설치
 - https://www.virtualbox.org
 - VirtualBox 5.2.18 platform packages
 - Windows hosts
 - 관리자 권한으로 설치

02. VirtualBox 호스트키 조합 설정
 - 파일 -> 환경 설정 -> 입력 -> 가상 머신 -> 호스트 키 조합 -> Shift + Ctrl + Alt
 - 확인

03. NatNetwork 설정
 - 파일 -> 환경 설정 -> 네트워크 -> 새 NAT 네트워크 추가
 - 포트 포워딩 -> 새 포트 포워딩 규칙 추가
 - 호스트 IP(192.168.56.1) -> 호스트 포트(22) -> 게스트 IP(10.0.2.101) -> 게스트 포트(22)
 - 확인


# Ubuntu Desktop 설치 ####

01. Ubuntu 16.04.5 LTS 다운로드
 - http://releases.ubuntu.com/xenial
 - Desktop image -> 64-bit PC (AMD64) desktop image


02. Virtual Machine 생성
 - VirtualBox 실행
 - 새로 만들기 -> 이름:dbuntu -> 종류:Linux -> 메모리:4096 
   -> 지금 새 가상 하드 디스크 만들기 -> VDI(VirtualBox 디스크 이미지) 
   -> 동적 할당 -> 파일 위치 및 크기:40GB -> 만들기
 - dbuntu -> 설정 -> 시스템 -> 프로세서 -> 2
 - dbuntu -> 설정 -> 저장소 -> 컨트롤러:IDE -> 비어있음 
   -> 가상 광 디스크 파일 선택 -> ubuntu-16.04.5-desktop-amd64.iso -> 확인
 - dbuntu -> 설정 -> 네트워크 -> 다음에 연결됨 -> NAT 네트워크
 - 시작버튼(클릭) -> 가상머신 부팅


03. Ubuntu 설치
 - 한국어(선택) -> Ubuntu설치(클릭)
 - Ubuntu 설치 중 업데이트 다운로드(선택) -> 계속(클릭)
 - 디스크를 지우고 Ubuntu 설치(선택) -> 지금설치(클릭) -> 계속(클릭)
 - 어디 살고 계신가요?(Seoul) -> 계속(클릭)
 - 키보드 배치 -> 계속(클릭)
 - 당신은 누구십니까? -> 이름:dbuntu -> 컴퓨터이름:dbuntu-VM 
   -> 암호선택:dbuntu -> 암호확인:dbuntu -> 계속(클릭)
 - 설치완료 -> 지금다시시작(클릭)
 - Please remove installation ~ -> Enter


04. Ubuntu 환경설정
 - Terminal
 - 환경변수

 - $ ifconfig(10.0.2.15 -> 10.0.2.101)
   $ sudo su -

 - IP 변경
   # ip a
   # nmtui
    -> 연결편집 -> "유선 연결1" -> <삭제>
    -> <추가> -> 이더넷 -> <생성>
       -> 프로파일이름:enp0s3
       -> 장치:enp0s3
       -> IPv4설정:수동 -> <보기>
          -> 주소:10.0.2.101/24
          -> 게이트웨이:10.0.2.2
          -> DNS 서버:8.8.8.8
          -> <OK>
       -> <종료>
   # nmcli c up enp0s3
   # ping 9.9.9.9

 - ssh 설치
   # apt-get install ssh -y
   # systemctl start ssh

 - 가상머신 재시작
   # shutdown -r 0


05. PuTTY-ssh 연결설정
  - HostName -> 192.168.56.1
  - Port -> 22
  - Open

 

# Docker 설치 ####

$ sudo su -

# apt update
# apt install curl
# curl -fsSL https://get.docker.com | sudo sh

# docker version
# docker info


[Docker Image 생성]
# docker search oracle
# docker search --filter=stars=30 centos

# docker images
# docker pull ubuntu:14.04
# docker pull centos:7
# docker images
# docker inspect ubuntu:14.04


[Docker Image 편집]
# docker tag ubuntu:14.04 hoons/webserver:1.0
# docker pull httpd:2.4
# docker tag httpd:2.4 httpd_copy
# docker history ubuntu:14.04
# docker rmi httpd_copy
# docker rmi $(docker images -q)


[Docker Container 생성]
# docker run -it --name=test1 ubuntu	-> Ctrl + p + q or # exit
# docker run -d centos /bin/ping localhost
# docker ps -a
# docker logs Container_Name
# docker ps -a -f name=test1
# docker ps -a -f exited=0

# docker run -d -p 8080:80 httpd
# docker ps -a
# netstat ?nlp | grep 8080

# docker run -it --add-host=test.com:192.168.1.1 centos
(container) # cat /etc/hosts	-> Ctrl + p + q

?? # docker run --cpu-shares=512 --memory=512m centos
# docker inspect Container_Name

# docker run -it -e test=example centos
# (Container) # env	-> # exit 
# docker run -it -w=/tmp/work centos
# (Container) # env	-> # exit


[Docker Container 응용(test1: Container_Name)]
# docker ps -a

# docker stats test1	-> Ctrl + p + q

# docker start test1
# docker stop test1
# docker start test1
# docker kill test1
# docker restart test1

# docker pause test1
# docker ps
# docker unpause test1

# docker rm test1
# docker rm -f test1

# docker container prune
# docker rm $(docker ps -a -q) -f

# docker run -it --name=test1 Ubuntu -> Ctrl + p + q
# docker attach test1 		-> Ctrl + p + q
# docker exec -it test1 bash	-> # exit
# docker top test1
# docker port test1

# docker rename test1 test_new
# docker inspect Container_Name | grep IPAddress
# docker inspect Container_Name | grep Image


[파일복사]
# docker cp test_new:/etc/passwd ~
# docker cp ~/Dockerfile test_new:/tmp
# docker diff test_new

[Container > Image]
# docker commit -a hoons test_new test_image

[Container > File]
# docker export test_new > latest.tar

[File > Image]
# cat latest.tar | docker import - test2

[Image > File]
# docker save -o export.tar test2

[File > Image]
# docker load -i export.tar


[Docker Container 응용]
# docker events -> 다른 터미널에서 # docker pull
# docker system events -> #다른 터미널에서 docker pull
# docker stats
# docker stats --no-stream
# docker system df


[Dash Board로 컨테이너 모니터링]
# docker run --volume=/:/rootfs:ro \
--volume=/var/run:/var/run:rw \
--volume=/sys:/sys:ro \
--volume=/var/lib/docker/:/var/lib/docker:ro \
--publish=8080:8080 --detach=true \
--name=cadvisor google/cadvisor:latest

-> Firefox -> localhost:8080/containers


[Docker Network - bridge 방식]
# docker network inspect bridge
# docker network create --driver bridge mybridge
# docker run -it --name mynetwork_container --net mybridge ubuntu:14.04
(Container) # ifconfig
# docker network create --driver=bridge --subnet=172.72.0.0/16 --ip-range=172.72.0.0/24 --gateway=172.72.0.1 my_custom_network


[Docker Network - host 방식]
# docker run -it \
--name network_host \
--net host \
ubuntu:14.04
(Container) # ifconfig


[Docker Network - none 방식]
# docker run -it \
--name network_none \
--net none \
ubuntu:14.04
# docker inspect network_none | grep IPAddress


[Dockerfile 생성 및 실행]
# touch Dockerfile
# vi Dockerfile
FROM centos:latest
MAINTAINER hoons docker@korea.ac.kr

# docker build -t sample:1.0 ~
# docker images
# docker build -t sample:2.0 ~
# docker images


[Dockerfile로 Web-Server 생성 및 실행]
# vi Dockerfile
FROM centos:latest
MAINTAINER hoons docker@korea.ac.kr
RUN yum -y install httpd
CMD /usr/sbin/httpd -D FOREGROUND

# docker build -t sample2 ~
# docker run -d -p 80:80 sample2


[Dockerfile로 Volume Mount_Container to Container]
# vi Dockerfile
FROM centos:latest
MAINTAINER hoons docker@korea.ac.kr
RUN mkdir /var/log/httpd
VOLUME /var/log/httpd

# docker build -t log-image ~
# docker run -it --name=log-container log-image


[Dockerfile로 Volume Mount_Container to Container]
# vi Dockerfile
FROM centos:latest
MAINTAINER hoons docker@korea.ac.kr
RUN yum -y install httpd
CMD /usr/sbin/httpd -D FOREGROUND

# docker build -t web-image ~
# docker run -d --name=web-container -p 80:80 --volumes-from log-container web-image

# docker start -ia log-container
# ls -l /var/log/httpd
# cat /var/log/httpd/access_log


[Private Registry 생성]
# docker search registry
# docker pull registry
# docker images
# docker run -d -p 5000:5000 registry
# docker ps -a


[Private Registry에 업로드하기 위한 image생성]
# vi Dockerfile
FROM centos:latest
MAINTAINER hoons docker@korea.ac.kr
RUN yum -y install httpd
CMD /usr/sbin/httpd -D FOREGROUND

# docker build -t webserver ~
# docker tag webserver localhost:5000/httpd
# docker images


[Private Registry 업로드]
# docker push localhost:5000/httpd
# docker rmi webserver
# docker rmi localhost:5000/httpd
# docker images
# docker pull localhost:5000/httpd
# docker images


[Container간 링크]
# docker run -d --name dbserver postgres
# docker run -it --name appserver --link dbserver:pg centos /bin/bash
(Container) # env | grep PG
(Container) # cat /etc/hosts (dbserver가 pg로 있는지 확인)
(Container) # ping pg (# ping $PG_PORT_5432_TCP_ADDR)


[Docker Compose 설치]
# apt install docker-compose
# docker-compose --version


[docker-compose.yml 파일생성]
# vi docker-compose.yml   -> 스페이스로 파일 구조 결정(탭 사용 금지)
serverA:
 image: httpd
serverB:
 image: mysql
 environment:
   MYSQL_ROOT_PASSWORD: password


[docker-compose로 컨테이너 생성]
# docker-compose up
 -> 결과를 다른 터미널에서 확인
# docker-compose ps
# docker-compose ps -q
# docker-compose logs


[docker-compose 응용]
# docker-compose ps
# docker-compose scale serverA=3 serverB=5
# docker-compose ps
# docker-compose down
# docker-compose run serverA /bin/bash
# docker-compose up -d
# docker-compose up -d serverB

# docker-compose start
# docker-compose stop
# docker-compose restart
# docker-compose restart serverA
# docker-compose kill -s SIGINT
# docker-compose rm


[Docker Compose를 활용한 wordpress 시스템구성]
# vi Dockerfile
FROM busybox
MAINTAINER 0.1 hoons docker@korea.ac.kr
VOLUME /var/lib/mysql

# docker build -t dataonly ~
# docker images dataonly
# docker run -it -d --name dataonly dataonly
# docker ps -a


# vi docker-compose.yml
webserver:
 image: wordpress
 ports:
  - 80:80
 links:
  - dbserver:mysql
dbserver:
 image:mysql
 volumes_from:
  - dataonly
 environment:
  MYSQL_ROOT_PASSWORD:password


# docker-compose up -d
# docker-compose ps 
# docker start -ia dataonly
(Container) # ls /var/lib/mysql

# docker export dataonly -> backup.tar
# tar xvf backup.tar


[Docker Swarm으로 클러스터 관리]
(Manager node) # docker swarm init --advertise-addr 10.0.2.101

(Worker node) # docker swarm join --token SWMTKN ……. 10.0.2.101:2377
  -> 모든 worker node에서 실행

(Manager node) # docker swarm join-token worker
(Manager node) # docker swarm join-token manager

(Manager node) # docker swarm join-token --rotate manager

(Manager node) # docker node ls

(Worker node) # docker swarm leave

(Manager node) # docker node ls
(Manager node) # docker node rm ubuntu02 또는
(Manager node) # docker node rm (worker_node_ID)
(Manager node) # docker node ls

# docker node promote ‘노드 이름 or ID’          
# docker node demote ‘노드 이름 or ID’


[Nginx 웹서비스 생성]
# docker node ls
# docker service create --name myweb --replicas 3 -p 80:80 nginx
# docker service ps myweb

Nginx 컨테이너를 3개에서 4개로 늘이기
# docker service scale myweb=4
# docker service ps myweb

(Manager node)
# docker service create --name global_web --mode global nginx 
# docker service ls
# docker service ps global_web

(Worker node)
# service docker stop

(Manager node) 
# docker node ls
# docker service ps myweb -> 복구 컨테이너 생성확인

# docker service scale myweb=1
# docker service scale myweb=4

[서비스 롤링 업데이트]
(Manager node)
# docker service create --name myweb2 --replicas 3 nginx:1.10
# docker service ps myweb2
# docker service update --image nginx:1.11 myweb2
# docker service ps myweb2

(Manager node)
# docker node ls
# docker node update --availability active swarm-worker01

(Manager node)
# docker node update --availability drain swarm-worker01

(Manager node)
# docker node update --availability pause swarm-worker01


노드 라벨 추가
(Manager node)
# docker node update --label-add storage=ssd swarm-worker01
# docker node inspect --pretty swarm-worker01


(Manager node)
# docker service create --name label_test --constraint ‘node.label.storage == ssd’ --replicas=5 ubuntu:14.04 ping docker.com

(Manager node)
# docker node ls | grep swarm-worker02
# docker service create --name label_test2 --constraint ‘node.id == worker02 ID’ --replicas=5 ubuntu:14.04 ping docker.com

(Manager node)
# docker service create --name label_test3 --constraint ‘node.hostname == swarm-worker01’ ubuntu:14.04 ping docker.com

(Manager node)
# docker service create --name label_test4 --constraint ‘node.role != manager’ --replicas 2 ubuntu:14.04 ping docker.com




도커 이론
https://subicura.com/2017/01/19/docker-guide-for-beginners-1.html
https://subicura.com/2017/01/19/docker-guide-for-beginners-2.html
https://subicura.com/2017/02/10/docker-guide-for-beginners-create-image-and-deploy.html
https://subicura.com/2017/02/25/container-orchestration-with-docker-swarm.html
https://futurecreator.github.io/2018/11/16/docker-container-basics/
