# 환경 구성
## Docker 설치
- [multipass](https://multipass.run/) 로 ubuntu VM 생성
- docker 설치
```shell
ubuntu@ubuntu:~$ sudo apt update
ubuntu@ubuntu:~$ sudo apt install -y docker.io
ubuntu@ubuntu:~$ sudo groupadd docker
ubuntu@ubuntu:~$ sudo usermod -aG docker $USER
ubuntu@ubuntu:~$ newgrp docker 
ubuntu@ubuntu:~$ exit
```

## MongoDB Container 실행
- 번역중인 v4.4 이미지 다운로드
  - `docker pull mongo:4.4.10`
- 컨테이너 실행
  - `docker run -d -p 27017:27017 test-mongo mongo:4.4.10`
- container 상태 확인
  - `docker ps`

[Index 로 이동](https://github.com/jx2lee/getting-started-with-mongodb#index)
