<img src="https://cdn-images-1.medium.com/max/2600/1*JAJ910fg52ODIRZjHXASBQ.png" width="50%"></img>

# 도커[http://docker.io]
---

### [전자책](http://www.realhanbit.co.kr/books/226)

### 도커란?
* 가상머신의 단점(가상머신의 불리한 성능?)을 보완한 가상화 어플리케이션, 가상화 어플리케이션 컨테이너
* 개발자와 시스템어드민을 위한 분산 애플리케이션용 오픈 플랫폼
* 아주 쉽게 가상화 머신을 생성하고 제거하고 관리를 할 수 있어 다양한 OS를 마음것 사용할 수도 있다
* 자신의 원본 PC를 아주 깨끗하게 유지 하면서도 다양한 시도와 테스트를 해볼 수 있는 장점이 있다.
* 팀원과 공유도 단순히 파일(Dockerfile) 하나만 공유하면 상대방이 바로 같은 셋팅의 머신을 생성할 수 있어 아주 좋습니다.

### 기본 명령어
```
# 실행중인 도커 머신 정보 보기
$ docker ps

# 실행중이 않은 머신을 포함한 정보 보기
$ docker ps -a

# 도커 머신 삭제 하기
$ docker rm -f [container name]

# 도커 이미지 삭제 하기
$ docker rmi [image name]

# 도커 머신 중지하기
$ docker stop [container name]

# 도커 머신 시작하기
$docker start [container name]

# 도커 머신으로 파일 복사하기
$ docker cp [소스파일] [머신이름:디렉토리]

# 도커 머신으로 부터 파일 가져오기
$ docker cp [머신이름:소스파일] [디렉토리]

# 도커 머신으로 재접속하기
$ docker attach [머신이름]

# 도커 머신에서의 로그 확인
# docker logs ${CONTAINER_ID}

```
## Install
```
도커 사이트
- https://docs.docker.com/docker-for-mac/
공식 사이트에서 아주 쉽게 설치부터 사용법까지 소개를 잘 해주고 있습니다.

- 도커 이미지 허브
https://hub.docker.com/explore/

다양한 도커 이미지들을 찾아 볼 수 있어 직접 가상 머신이 환경셋팅들을 설치할 필요가 없습니다.
```
### 1. For Mac
1) [도커 다운로드 For Mac](https://docs.docker.com/docker-for-mac/install/)
2) docker.dmg 파일 실행
3) 설치 완료 및 버전 확인
  `$ docker --version`
4) Dockerfile 로 빌드하기


## Run
### 1. 도커에 Mysql DB 설치하여 접속
1. mysql docker 이미지 조회 
 ```
 $ docker search mysql
 ```

2. mysql docker 이미지 Pull
 ```
 $ docker pull mysql
 ```

3. mysql docker 이미지 확인
```
$ docker images
REPOSITORY     TAG       IMAGE ID          CREATED             SIZE
mysql       latest     d72169616e20        23 hours ago        443MB
```

4. docker 이미지를 통해 mysql 컨테이너 생성

```
//root에 패스워드
$ docker run -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=toor --name mysql_test mysql

//만약 root에 패스워드를 넣지 않는다면
$ docker run -d -p 3306:3306 -e MYSQL_ALLOW_EMPTY_PASSWORD=true --name mysql_test mysql
```

- root 계정의 패스워드 설정
- -p 3306:3306 : 호스트의 3306포트와 컨테이너의 3306포트를 연결한다. 즉 호스트에 3306포트 접근이 발행하면 해당 컨테이너에 접속이 된다.
- -e MYSQL_ROOT_PASSWORD=password : 컨테이너를 생성하면서 환경변수를 지정한다. root계정의 비밀번호를 설정한다.
- -name mysql_test : 컨테이너의 이름은 mysql_test로 지정한다.
- 만약 현재 로컬에서 사용중인 3306 mysql 이 있다면 머신이 동작하지 않기 때문에 포트를 달리 해준다.
```
// 다음과 같이 하면 도커에 있는 mysql은 3307 포트로 접속하면 된다.
$ docker run -d -p 3307:3306 -e MYSQL_ROOT_PASSWORD=toor --name mysql_test mysql
```

5. docker 머신 확인
```
// 모든 머신 확인 Status 가 날짜가 아니면 동작하지 않음. or Up 34 minutes 로 나오면 동작중
$ docker ps -a
// 실행중인  머신 확인
$ docker ps
```

6. shell 접속
```
$ docker exec -i -t [컨테이너 이름] bash
$ root@7392aef1dd89:/# mysql -u root -p
[Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.0.16 MySQL Community Server - GPL

Copyright (c) 2000, 2019, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| project1           |
| sys                |
+--------------------+
5 rows in set (0.00 sec)

```

7. Data Grip 또는 MYSQLWorkbench 를 통해 접속
```
> host: 127.0.0.1
> port: 3306 //사용하고 있는 포트여서 3307과 같이 변경하였다면 3307
> user id: docker run -d 시 만든 root
> password: docker run -d [MYSQL_ROOT_PASSWORD] 로 지정한 toor
```


### 2. 도커에 Node Application 이미지 만들어 실행
1. Node App 준비
```
# localhost:3000 접속 시 기본 페이지 출력
$ mkdir app
$ npm i --g express-generator
$ express express-example
$ npm i
$ npm start
```

2. Dockerfile 만들기
> Dockerfile은 이미지 생성을 위한 배치파일과 같은 개념이다. 특정 이미지를 기준으로 새로운 이미지 구성에 필요한 명령어를 작성한 파일.
> 파일 및 디렉토리 추가 / 환경 변수 설정 / 대상 이미지를 사용하여 컨테이너를 시작할 프로세스를 실행

```
$ cd /app
$ touch Dockerfile
$ vi Dockerfile
```

```
# Dockerfile
어떤 이미지로부터 새로운 이미지를 생성할지를 지정
FROM node:10.15.3

#Dockerfile 을 생성/관리하는 사람
MAINTAINER Steven <steven@wepla.net>

# /app 디렉토리 생성
RUN mkdir -p /app
# /app 디렉토리를 WORKDIR 로 설정
WORKDIR /app
# 현재 Dockerfile 있는 경로의 모든 파일을 /app 에 복사
ADD . /app
# npm install 을 실행
RUN npm install

#환경변수 NODE_ENV 의 값을 development 로 설정
ENV NODE_ENV development

#가상 머신에 오픈할 포트
EXPOSE 3000 80

#컨테이너에서 실행될 명령을 지정
CMD ["npm", "start"]
```

3. 도커 이미지 생성 하여 실행
```
# docker build --tag [이미지명:태그명] [빌드대상 디렉토리]
$ docker build --tag express:0.0.1 .

# 이미지 생성 확인
$ docker images

# 모든 실행 이미지 확인
$ docker ps -a

# 이미지 생성
$ docker create --name express_0 -p 3000:3000 express:0.0.1

# 생성한 이미지를 docker에서 실행
$ docker start express:0.0.1

# docker 이미지를 지금 콘솔에서 실행 Ctrl+C 시 종료
$ docker run --name express_1 -p 3000:3000 express:0.0.1

# docker 이미지 실행  docker stop [container id] 할때까지
$ docker run -d --name express_2 -p 3000:3000 express:0.0.1

# 생성한 이미지 삭제
$ docker rm -f [NAMES]
```

