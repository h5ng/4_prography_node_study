# Docker study 1탄


도커는 설명이 필요없는 필수 품이 되었다.

[도커에 대한 설명](https://subicura.com/2017/01/19/docker-guide-for-beginners-1.html)

레이어로 구성된 이미지를 받아서 가상환경을 구축하는 컨테이너 기반 가상화 플랫폼이다. 기존의 VM(가상머신)은 OS가상화라서 용량과 리소스 잡아먹는게 심했는데, CPU 가상화(?)를 써서 좀 더 빠른 속도로 구축이 가능하다. 쉽게 생각하면, 프로그램이랑 똑같은 모양을 본뜨고, 필요할 때에 실행만 하면 프로그램이 복제된다.

사용하려면 무엇을 해야할까?

도커를 설치 해야한다. ~~(당연한 사실)~~

- [Docker for Mac](https://docs.docker.com/docker-for-mac) 
- [Docker for windows](https://docs.docker.com/docker-for-windows), 
- Docker for Linux ` curl -fsSL https://get.docker.com/ | sudo sh `


빠르게 다음의 경로로 실행 `이미지` --> `컨테이너 실행` --> `프로그램 실행`


사용법
1. CLI: 커맨드 라인으로 작동 가능
    1. [Docker Hub](https://hub.docker.com/) 에서 이미지 `pull`
    2. 실행 `docker run image:tag`
2. Dockerfile
    - 이미지 하나에 대한 도커 파일 작성
    - `FROM` 가상환경의 이미지 지정(실행할 프로그램과 언어 등등 알맞게 사용)
    - `ENV` 환경변수 지정
    - `COPY`(외부 접근 X), `ADD`(외부 접근 O) 파일 복사(빌드 디렉토리에서 실행 디렉토리로, 앞에 써져있는 외부 접근 가능 여부로 골라서 사용한다. 똑같은 복사이지만, 외부에서 접근 가능하게 할 것이냐 차이가있음)
    - `WORKDIR` 작업디렉토리 지정
    - `RUN` 실행 명령 지정
    - `EXPOSE` 컨테이너가 외부에 노출할 포트번호 지정
    - `CMD` 커맨드 명령 지정
3. docker-compose
    - 컨테이너를 여러개 연결할 수 있다. 기본적으로는 도커 허브에서 이미지를 받지만, 도커파일을 경로지정해서 커스텀 컨테이너를 연결할 수 있다. 가끔 편하다.



도커 실행 예제

- [Nginx로 로컬에서 https 리다이렉트 에제](https://github.com/geusan/nginx-proxy)
- [postgreSQL(준비중)]()
- [MySQL(준비중)]()
- [MariaDB(준비중)]()
- [MongoDB(준비중)]()

created by geusan