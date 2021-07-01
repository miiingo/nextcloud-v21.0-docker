# [Ubuntu] Ubuntu 18.04에 Nextcloud 21.0 버전을 Docker로 실행

## ■ 버전 설명

현재 최신버전인 latest 버전을 사용한다. (2021.06.28 기준)
- Nextcloud: 21.0.2.1
- PostgreSQL: 13.3-1.pgdg100+1


## ■ 기본 설정

데이터 보존을 위해 볼륨을 설정하는데, 여기에서는 호스트 경로와 볼륨 처리한다.

Docker Volume으로 처리도 가능하다.

```bash
# 호스트 경로 볼륨 저장소 생성
mkdir -p /docker/nextcloud/db

# Nextcloud 프로젝트 복제
cd /opt/gopath/src/github.com/honeybee
git clone https://github.com/miiingo/nextcloud-v21.0-docker nextcloud

```


## ■ Nextcloud 실행

```bash
# Nextcloud 실행
cd /opt/gopath/src/github.com/honeybee/nextcloud
docker-compose up -d

```


### ● trusted_domain 설정

localhost가 아닌 특정 IP나 도메인으로 접속하기 위해서는 trusted_domain 설정을 추가해주어야 한다.
(Nextcloud의 Docs에서는 NEXTCLOUD_TRUSTED_DOMAINS 환경변수를 설정하면 자동으로 적용된다고 하는데, 테스트 결과 적용이 제대로 되지 않음. 따라서 별도로 설정 필요)

로컬 환경의 docker/nextcloud/config/config.php 파일에 trusted_domain 설정이 되어있는지 확인한다.
(볼륨 처리가 되어있기 때문에 Nextcloud 컨테이너 내의 /var/www/html/config/config.php 파일과 동일함)

```bash
# trusted_domain 설정 확인
sudo cat /docker/nextcloud/config/config.php

```

원하는 trusted_domain이 설정에 추가되어있지 않은 경우, 다음 내용을 추가해주어야 한다.
여기서 IP 주소는 Nextcloud 컨테이너를 실행한 호스트의 IP 주소를 입력해야한다.
```bash
sudo vi /docker/nextcloud/config/config.php


# config/config.php 파일에 다음 내용 추가
  'trusted_domains' => 
  array (
    0 => '<Nextcloud 컨테이너를 실행한 호스트의 IP 주소 ex:127.0.0.1>:8080',
  ),

```

이제 <해당 IP 주소>:8080 경로로 Nextcloud 웹 페이지 접속이 가능하다.



## ■ Nextcloud 종료

```bash
# Nextcloud 종료
cd /opt/gopath/src/github.com/honeybee/nextcloud
docker-compose down

# 볼륨 처리한 이전 데이터 삭제 필요 시 다음 실행 
## 호스트 경로 볼륨으로 설정한 경우 (이후 볼륨 저장소를 다시 생성해주어야함)
sudo rm -rf /docker/nextcloud
## Docker Volume으로 설정한 경우
docker volume rm db nextcloud

```