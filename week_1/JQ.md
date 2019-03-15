# 도커

- 빠르고 가벼운 가상화 솔루션

- 애플리케이션과 그 실행환경 / os를 모두 포함한 소프트웨어 패키지 -> Docker image

- 플랫폼과 상관없이 실행될 수 있는 애플리케이션 컨테이너를 만드는 기술

- Docker image는 컨테이너 형태로 docker engine이 있는 어디에서나 실행가능

  - 대상 : 로컬머신, azure, aws 등
  - 하나의 도커이미지를 통해 다수의 컨테이너를 생성할 수 있습니다.

- 생성된 Docker Container는 바로 쓰고 버리는 것(Immutable Infrastructure 패러다임)

  - 업데이트할때마다 새로운버전의 컨테이너를 띄우고 이전 것을 날림

- 도커 컨테이는 격리되어있어서 해킹되더라도 도커엔진이 구동되는 운래의 서버에는 영향을 끼치지 않음


## 도커만의 특징/유의사항

- 도커내에서 어떤 프로세스가 도는지 명확히 하기 위해서
  - 하나의 도커내에서 다양한 프로세스가 구동되는 것을 지양
  - 한 종류의 프로세스만 구동 지향
- 하나의 도커내에서 프로세르를 백그라운드 (Daemon 형태)로 구동하는 것을 지향
  - 프로세스를 Foreground로 구동하는 것을 지향 - nginx -g daemon off;
  - 실행 로그도 표준 출력(stdout)으로 출력

## Container Orchestration

- 컨테이너 관리툴의 필요성
  - 컨테이너 자동배치 및 복제, 컨테이너 그룹에 대한 로드밸런싱, 컨테이너 장애복구, 클러스터 외부에 서비스 노출, 컨테이너 추가 또는 제거로 확장 및 축소, 컨테이너 서비스간의 인터페이스를 통한 연결 및 네트워크 포트 노출제어
- 주요도구
  - 구글의 kubernetes 
  - Docker swram
  - aws ecs

## Docker Registry

- 도커 이미지 저장소
- 공식저장소 : Docker hub
- ecr( aws elastic container registry)

## Docker 설치

DOCKER 실행시

Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?

와 같은 에러가 날 시에는 docker 데몬 파일이 있는 폴더 유무를 우선 확인하고 없으면 설치가 제대로 안된 것이고, 제대로 설치되어 있다면

sudo service docker restart
sudo service docker status
sudo docker run hello-world // docker 실행되는지 테스트



## ## Dockerfile

- 도커이미지를 만들때, 수행할 명령과 설정들을 시간순으로 기술한 파일

- docker build .... : 빌드를 할때 할 작업을 시간순으로 기술

- ```dockerfile
  FROM ubuntu:16.04
  RUN apt-get update && apt-get install -y python3-pip python3-dev && apt-get clean #성공임을 보장받아야할때는 &&을 써야함
  
  RUN mkdir /code
  WORKDIR /code
  ADD requirements.txt /code/
  RUN pip3 install -r requirements.txt
  ADD . /code/
  
  EXPOSE 8000
  #docker run 할때 CMD가 실행됨
  CMD ["python3", "/code/manage.py", "runserver", "0.0.0.0:8000"]
  
  ```

- 도커 포트포워딩할때 docker un -p(-publish) 80:8000

  - 80번포트를 8000번과연결

![image-20190305225231670](./img/image-20190305225231670.png)

docker run --rm -it python:3.7-stretch

--rm 옵션 -> 이미지는 제거가됨
exit()하는 순간 제거됨
필요할때 쓰고 바로 버리는 패턴

docker container ls
-> 현재 띄워져있는 컨테이너

all과 ls 의 차이점 -> stop 상태의 컨테이너까지 보여줌

 docker rm $(docker ps -a -q)
-> 현재 떠있는 모든 컨테이너 제거
