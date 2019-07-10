### 190710 도커

#### Get Started, Part 1: Orientation and setup

도커란 (https://www.youtube.com/watch?v=tPjpcsgxgWc)

- ppt하기 위해 폰트깔고, ppt깔고, 포토샵도 깔아야하듯이 서버 운영할때도 언어, 웹서버, db, 자동배포툴 같이 여러가지 버전 신경 맞춰서 깔아줘야함. 만약 서버를 옮기거나 추가할 때 그 곳에도 다 똑같이 설치해줘야함. 이때 만약 예전~~~~에 구축된 환경이라면 곤란. 혹은 같은 서버에 다른 서비스를 돌리는 경우, 서비스마다 프로그램 버젼이 달라 까다로울 수 있음 (ex. 기존 사이트 Java 7 -> 새로운 서비스는 Java 8)

- 도커는 각 요소들이 설치된 모습을 이미지란 형태로 박제해서 저장. 각 제품마다 공식적으로 제공되는 이미지도 있고, 임의로 사용자가 만들수도 있음

- github처럼 이 이미지들은 DockerHub이란 곳에 업로드, 공유 가능 

- 이미지로 저장된 항목들이 함께 연결되서 동작하도록 설정된 상태를 정리해놓은 명력어 텍스트나 문서형태로도 저장 가능

- 설치하는 과정을 어디서든 어떤 컴퓨터에서 자동으로 재현할 수 있도록 녹화해두는 느낌

- 도커가 이것들을 바로 컴퓨터에 설치하는 것이 아니라, 각각을 '컨테이너'라고 불리는 독립된 가상 공간을 만들어서 복원

- 그렇기 때문에 서로 다른 버젼도 한 컴퓨터에서 잘 돌아갈 수 있음 

  ![image](https://user-images.githubusercontent.com/40848630/60977744-f68ff080-a36a-11e9-8279-9d6b8a68284a.png)

- 가상 컴퓨팅과 다른점

  ![image-20190710233332005](/Users/alpineviolet/Library/Application Support/typora-user-images/image-20190710233332005.png)

  - 가상 컴퓨팅은 한 물리적 컴퓨터안에 각각 OS를 가동하는 가상 컴퓨터들이 물리적 자원을 분할해서 쓰기 때문에 성능에 한계가 생김
  - 도커는 OS단까지 내려가는 게 아니라 실행환경만 독립적으로 돌리기 때문에 가상 컴퓨팅보다 빠름 (실제 설치한 것과 거의 비슷)

- 결론: Containerization을 사용하연 CI/CD가 편리하다.
  - 어플리케이션은 시스템 의존성이 없다
  - 언제든지 업데이트 가능하고
  - 자원 밀도(?) 최적화

- CI/CD? 
  - CI = 개발자를 위한 자동화 프로세스인 지속적인 통합(Continuous Integration)
  - CD = 지속적인 서비스 제공 (Continuous Delivery) 및/또는 지속적인 배포 (Continuous Deployment)
  - CI/CD는 애플리케이션 개발 단계를 [자동화](https://www.redhat.com/ko/topics/automation/whats-it-automation)하여 애플리케이션을 보다 짧은 주기로 고객에게 제공하는 방법입니다. CI/CD의 기본 개념은 지속적인 통합, 지속적인 서비스 제공, 지속적인 배포입니다. CI/CD는 새로운 코드 통합으로 인해 개발 및 운영팀에 발생하는 문제(일명 "[통합 지옥(integration hell)](https://www.solutionsiq.com/agile-glossary/integration-hell/)")를 해결하기 위한 솔루션입니다.
  - https://www.redhat.com/ko/topics/devops/what-is-ci-cd
- 예전 Demon Tools랑 비슷한건감? 



#### Get Started, Part 2: Containers

- Dockerfile, requirements.txt, app.py

  - `pip install -r requirements.txt` 안되서… `sudo easy_install pip` 함

  - 아래 에러 떠서 

  - ```
    ERROR: Could not install packages due to an EnvironmentError: [Errno 13] Permission denied: '/Library/Python/2.7/site-packages/Click-7.0.dist-info'
    Consider using the `--user` option or check the permissions.
    ```

    `pip install -r requirements.txt —user`함..

  - 아래 워닝 떴는뎁… 일단 냅둠 

  - ```
    WARNING: The script flask is installed in '/Users/alpineviolet/Library/Python/2.7/bin' which is not on PATH.
      Consider adding this directory to PATH or, if you prefer to suppress this warning, use --no-warn-script-location.
    ```

- hello-world랑 friendlyhello 차이가 뭐지? 

  ![image](https://user-images.githubusercontent.com/40848630/60981294-06aace80-a371-11e9-94a2-cbcb187e8f2a.png)

- 실습 성공!

  ![image](https://user-images.githubusercontent.com/40848630/60981396-378b0380-a371-11e9-809e-d3016d147e22.png)

- http://0.0.0.0:80 하면 아래처럼 뜸.. 왜뜸? 

  ![image-20190711001841127](/Users/alpineviolet/Library/Application Support/typora-user-images/image-20190711001841127.png)

- 뭔말인지 모르겠음,,,,

- ```
  This port remapping of 4000:80 demonstrates the difference between EXPOSE within the Dockerfile and what the publish value is set to when running docker run -p. In later steps, map port 4000 on the host to port 80 in the container and use http://localhost.
  ```

- `docker run -d -p 4000:80 friendlyhello`

  background 모드?

- `tagging`

  ![image](https://user-images.githubusercontent.com/40848630/60982415-0c091880-a373-11e9-8b38-3e093f1c4233.png)

  생겼닭! 

- push 할때 deny 오류 생김..

  `denied: requested access to the resource is denied`

  - user name을 도커 name이랑 똑같이 안해서 그런거였음ㅎ… 

    ![image](https://user-images.githubusercontent.com/40848630/60983018-33acb080-a374-11e9-9733-4e2dd6965b99.png)

    ![image](https://user-images.githubusercontent.com/40848630/60983035-39a29180-a374-11e9-9678-dea3b9b85474.png)

    성-공

- basic Docker commands

  ```
  docker build -t friendlyhello .  # Create image using this directory's Dockerfile
  docker run -p 4000:80 friendlyhello  # Run "friendlyhello" mapping port 4000 to 80
  docker run -d -p 4000:80 friendlyhello         # Same thing, but in detached mode
  docker container ls                                # List all running containers
  docker container ls -a             # List all containers, even those not running
  docker container stop <hash>           # Gracefully stop the specified container
  docker container kill <hash>         # Force shutdown of the specified container
  docker container rm <hash>        # Remove specified container from this machine
  docker container rm $(docker container ls -a -q)         # Remove all containers
  docker image ls -a                             # List all images on this machine
  docker image rm <image id>            # Remove specified image from this machine
  docker image rm $(docker image ls -a -q)   # Remove all images from this machine
  docker login             # Log in this CLI session using your Docker credentials
  docker tag <image> username/repository:tag  # Tag <image> for upload to registry
  docker push username/repository:tag            # Upload tagged image to registry
  docker run username/repository:tag                   # Run image from a registry
  ```



### Get Started, Part 3: Services

- `docker-compose.yml`

  - yml = YAML = "YAML Ain't Markup Language" = 마크업 언어가 아니다앗!!! 이 확장자명이라니..

  - `Be sure you have pushed the image you created in Part 2 to a registry, and update this .yml`

    - …? 어떻게 update하는 지 알려줘...
    - ![image](https://user-images.githubusercontent.com/40848630/60983990-e7627000-a375-11e9-8c28-631ee3bcc88b.png)
    - 된건가 안된건가.. 
    - This `docker-compose.yml` file tells Docker to do the following:
      - Pull [the image we uploaded in step 2](https://docs.docker.com/get-started/part2/) from the registry.
      - Run 5 instances of that image as a service called `web`, limiting each one to use, at most, 10% of a single core of CPU time (this could also be e.g. “1.5” to mean 1 and half core for each), and 50MB of RAM.
      - Immediately restart containers if one fails.
      - Map port 4000 on the host to `web`’s port 80.
      - Instruct `web`’s containers to share port 80 via a load-balanced network called `webnet`. (Internally, the containers themselves publish to `web`’s port 80 at an ephemeral port.)
      - Define the `webnet` network with the default settings (which is a load-balanced overlay network).
    - 1. 5개 아닌데?
      2. 저 중에 선택되는 거 기준이 뭔지?

    ![image](https://user-images.githubusercontent.com/40848630/60984456-b6cf0600-a376-11e9-92e3-d4a3390edc3e.png)

![Jul-11-2019 00-58-51](https://user-images.githubusercontent.com/40848630/60984677-188f7000-a377-11e9-829c-9b12b728421f.gif)

- scale app? => It simply means making the application serve more users. ([quora](https://www.quora.com/What-does-it-mean-to-scale-an-application))
  - 아, 걍 replica 늘려보라는 뜻?
