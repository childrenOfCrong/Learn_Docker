# Step2

## 1. Images and containers

- Images: 이미지를 실행하여 컨테이너를 실행한다. 이미지는 어플리케이션이 필요한 모든 것을 포함하는 실행 가능한 패키지이다.
- container: 이미지의 런타임 인스턴스이다. 즉, 이미지가 실행될 때 메모리에 저장된다. 'docker ps' 명령어를 통해 실행되는 컨터이너를 확인할 수 있다.

### 1-1. DockerFile

    # official Python runtime 이미지를 parent 이미지로 사용한다.
    FROM python:2.7-slim
    
    # working directory를 /app으로 설정한다.
    WORKDIR /app
    
    # 현재 디렉토리의 컨텐츠를 /app에 카피한다.
    COPY . /app
    
    # requirements.txt에 정의된 필요한 패키지들을 설치한다.
    RUN pip install --trusted-host pypi.python.org -r requirements.txt
    
    # 외부에서 접속할 수 있는 포트 설정
    EXPOSE 80
    
    # 환경 변수 정의
    ENV NAME World
    
    # Run app.py when the container launches
    CMD ["python", "app.py"]

1. **pip install -r requirements.txt : 의존 패키지 설치**
2. **docker build --tag=friendlyhello . : docker build(이미지 생성)**
3. **docker run -p 4000:80 friendlyhello : run**

- **docker run -d -p 4000:80 friendlyhello : detached mode**
- **docker container stop <container id>: remove container**

### 1-2. Share Your Image

1. [hub.docker.com](http://hub.docker.com/) 의 계정 필요
2. **docker login**
3. **docker tag image username/repository:tag : 깃으로 치면 commit**
4. **docker push username/repository:tag : push**

## 2. About Service

Service는 실제로 "production container"입니다. 서비스는 하나의 이미지만 실행하지만 이미지를 실행하는 방법을 체계화합니다. 즉, 어떤 포트를 사용해야 하는지, 몇 개의 컨테이너가 필요한지, 컨테이너 복제본을 실행하는 만큼 서비스에 필요한 용량이 있는지, 곧, 실행할 Container 인스턴스를 정하는 것, 서비스를 확장하면 해당 소프트웨어를 실행하는 컨테이너 인스턴스 수가 변경되어 프로세스의 서비스에 더 많은 컴퓨팅 리소스가 할당됩니다. 

Docker 플랫폼으로 서비스를 전의, 실행 및 확장하는 것은 매우 쉽습니다. **docker-compose.yml** 파일을 작성하면 됩니다. 

docker-compose.yml로 서비스 환경을 정의한다. 

### 2-1. docker-compose.yml

    version: "3"
    services:
      web:
        # replace username/repo:tag with your name and image details
        image: username/repo:tag
        deploy:
    			# 인스턴스 개수
          replicas: 5
          resources:
            limits:
    					# 각 컨테이너는 cpu의 10%를 사용한다.
              cpus: "0.1"
    					# 50M RAM을 할당
              memory: 50M
          restart_policy:
    				# 실패할때마다 다시 띄우는 설정
            condition: on-failure
        ports:
          - "4000:80"
        networks:
    			# load-balanced overlay network
          - webnet
    networks:
      webnet:

### 2-2 Run your new load-balanced app

1. **docker swarm init : swarm manager 지정하는 과정**
2. **docker stack deploy -c docker-compose.yml getstartedlab : getstartedlab이라는 이름으로 docker-compose.yml의 설정대로 deploy**

- docker service ps getstartedlab_web: container들 확인
- 리프레쉬 할 때마다 사이트의 Hostname 정보가 변경된다 → container들의 순환
- docker stack rm getstartedlab: container 삭제
- docker swarm leave --force : swarm manager 권한 취소

## 3. About Swarm

- swarm은 docker를 실행하고 클러스터에 합류한 machine들의 그룹이다.
- 하나의 swarm manager가 있는 클러스터가 존재한다.
- 이 클러스터에 machine이 합류하면 node가 된다.
- 몇가지 전략이 있다.
    - emptiest node: 최소한의 node만을 사용한다.
    - global: 모든 node에 적어도 1개의 컨테이너를 부여한다.
- swarm 메커니즘에 따라 swarm 메니저가 도커를 컨드롤 할 수 있다.

1. **docker swarm init: swarm manager를 설정한다.**
2. **docker swarm join: 클러스터에 합류한다.**

### 3-1. Set up your Swarm

1. virtualBox driver을 통해 docker-machine을 사용한 VM을 생성한다.

        docker-machine create --driver virtualbox myvm1
        docker-machine create --driver virtualbox myvm

2. swarm manager를 설정한다.

        docker-machine ssh myvm1 "docker swarm init --advertise-addr <myvm1 ip>"

3. cluster에 machine을 합류시킨다.

        $ docker-machine ssh myvm2 "docker swarm join \
        --token <token> \
        <ip>:2377"
