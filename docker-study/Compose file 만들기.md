# Compose file 만들기

1. 이미지 준비

- 도커 허브에서 내려 받을 이미지 또는 Dockerfile 준비

1. compose.yaml 정의

```yaml
services:
  backend:
    image: username/repository_name:tag
    container_name: backend
    depends_on:
      - mysql
    environment:
			# 컨테이너 간에는 접속 가능, url을 서비스 이름으로 잡는다.
      SPRING_DATASOURCE_URL: jdbc:mysql://mysql:3306/my_database
    ports:
			# backend API 용 포트 연결
      - 8080:8080

  mysql:
    image: mysql:latest
    container_name: database
    environment:
      MYSQL_ROOT_PASSWORD: 1111
      MYSQL_DATABASE: my_database
    ports:
			# 호스트에서는 3307 포트로 접근 가능
			# 같은 호스트의 다른 컨테이너는 3306 포트로 접근 가능
      - 3307:3306
```

1. `docker compose up`
