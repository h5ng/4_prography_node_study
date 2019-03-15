# Docker

#### Docker란 무엇인가?

* 2013년에 등장한 새로운 컨테이너 기반 가상화 도구
* 응용 프로그램들을 소프트웨어 컨테이너 안에 배치시키는 일을 자동화하는 오픈소스 프로젝트
* <u>도커 컨테이너</u>는 소프트웨어 실행에 필요한 모든 것(코드, 런타임, 시스템 도구, 시스템 라이브러리 등 서버에 설치되는 무엇이든)을 포함하여 파일 시스템 안에 감싼다.
* 한 서버에 여러 개의 컨테이너 실행 가능, 여러 대의 서버에도 문제 없음!
* 컨테이너의 특정 상태를 항상 보존해두고, Docker가 설치되어 있다면 필요할 때 언제, 어디서나 이를 실행할 수 있도록 도와주는 도구이다.

----

#### 도커와 가상머신의 차이 : OS 가상화 여부

![img](http://blogfiles.naver.net/MjAxODEyMTVfNzUg/MDAxNTQ0ODA2MjAyNTU1.b6CGHVjb1b2_JibDY2Cwyukuug0__wwG2HuomTROJKkg.CCSGUwIISQSJtGFeGDueHPZWapx9Jl3tU-3O9hlpxncg.PNG.jevida/121418_1650_Docker2.png)

* 가상머신
  * 서버 하드웨어를 가상화
  * 운영체제 위에서 또 다른 운영체제를 통째로 돌리므로, 유용하지만 많은 리소스 필요
  * 배포용으로 쓰기에는 성능 면에서 매우 불리함
* 컨테이너
  * 서버의 OS를 가상화
  * 가상 머신의 단점은 극복, 장점만 극대화
  * 어느 플랫폼에서나 재현 가능한 어플리케이션 컨테이너!

---

#### 이미지

![img](https://rampart81.github.io/img/docker_img_layers.jpeg)

* 이미지란? - **컨테이너 실행에 필요한 파일과 설정값 등을 포함하고 있는 것**으로, 상대값을 가지지 않고 변하지 않는다.
* 파일 시스템들의 layer로 이루어져 있다. (파일 시스템 하나하나가 이미지)
* image들을 서로 위에 layer 시키는 구조로 되어있다.
* Base가 되는 이미지: 부모 이미지
* 위부터 아래로 횡단하는 구조
* read-only 파일 시스템들이 mount가 되고, 마지막으로 read-write 파일 시스템을 파일 시스템 맨 위에 mount 한다.
* 컨테이너를 실행하기 위한 <u>모든</u> 정보를 가지고 있기 때문에 더 이상 이것저것 설치하는 수고를 할 필요가 없다!

---

#### 레이어 저장 방식

![Docker Layer](https://subicura.com/assets/article_images/2017-01-19-docker-guide-for-beginners-1/image-layer.png)

* 도커 이미지는 용량이 수백 MB에 육박함

* 이를 해결하기 위해 <font color="red">**레이어**</font>를 사용

* 이미지는 여러 개의 읽기 전용 레이어로 구성되고, 파일이 추가되거나 수정되면 새로운 레이어 생성

  ~~무슨 말인지 잘 모르겠다~~

----

#### 도커를 사용해야 하는 이유

- 더 많은 소프트웨어를 더 빨리 제공

  : 평균적으로 Docker를 사용하지 않는 사용자보다 7배 더 많은 SW를 제공함

- 운영 표준화 : 손쉽게 배포, 문제 파악, 롤백 가능

- 원활하게 이전 가능

- 비용 절감: 사용률은 up, 비용은 down

---

#### 도커 시작하기

* Ubuntu에서 실행 중

* ```
  curl -fsSL https://get.docker.com/ | sudo sh  // 시작
  ```

* ```
  sudo usermod -aG docker $USER # 현재 접속중인 사용자에게 권한주기
  ```

* ```
  docker version 
  ```

* ```txt
  docker run [OPTIONS] IMAGE[:TAG|@DIGEST] [COMMAND] [ARG...]
  ```

* 













---

#### 참고자료

- http://sqlmvp.kr/221419557326

* https://aws.amazon.com/ko/docker/
* https://subicura.com/2017/01/19/docker-guide-for-beginners-1.html
* https://blog.nacyot.com/articles/2014-01-27-easy-deploy-with-docker/
* https://rampart81.github.io/post/docker_image/ 도커 이미지
* https://subicura.com/2017/01/19/docker-guide-for-beginners-2.html 다운로드 1