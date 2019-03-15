# 도커 

https://miiingo.tistory.com/category/%EA%B0%9C%EB%B0%9C%EB%8F%84%EA%B5%AC/Docker ( 도커 설명 블로그 )

http://pyrasis.com/docker.html ( 가장 빨리 만나는 Docker )

## 도커 설치하기

* 서버 환경 : 우분투 18.04

```shell
sudo apt update
sudo apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
sudo apt update
sudo apt install docker-ce
```

##  도커 명령어

* docker pull <image> - 이미지를 다운로드
* docker ps - 실행중인 도커 리스팅
* docker image - 도커 이미지를 관리할 수 있음 
  * option : ls - 이미지 리스팅!
* docker search <option> <keyword> - 이미지 검색
* docker build <option> <directory> - 이미지를 빌드
  * option : -t ( 이름/태그 식으로 네이밍 가능 )
* docker run <option> <image> <name>- 이미지를 실행
  * option : —link - 다른 컨테이너 연동

## Dockerfile로 도커 관리하기

* FROM ubuntu:18.04 ( 우분투 이미지를 받아옴 )
* RUN apt-get update ( RUN으로 커맨드 실행이 가능합니다! )
* RUN apt-get upgrade -y
* RUN apt-get install -y git