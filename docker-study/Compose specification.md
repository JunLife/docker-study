# Compose specification

- 아래 문서를 참조하여 작성했습니다.

[Compose specification](https://docs.docker.com/compose/compose-file/)

[Compose specification](https://runebook.dev/ko/docs/docker/compose/compose-file/index)

## Compose file versions

| Reference file                                        | What changed in this version |
| ----------------------------------------------------- | ---------------------------- |
| Compose Specification (most current, and recommended) | Versioning                   |
| Version 3                                             | Version 3 updates            |
| Version 2                                             | Version 2 updates            |
| Version 1 (Deprecated)                                | Version 1 updates            |

## Compose specification

- Compose specification은 도커가 다중 컨테이너 애플리케이션을 정의하기 위해 만든 **새로운 표준 규격**이다.
- **YAML(YML)**을 이용해서 다음과 같은 항목을 정의한다.
  - service(필수), network, volume, config, secret
  - version은 deprecated 이다.
- Compose file의 이름은 compose.yaml 또는 docker-compose.yaml을 사용한다.
- 만약 둘 다 존재하는 경우 Compose spec의 컨벤션인 **compose.yaml을 권장**한다.

## Compose 애플리케이션 모델

- Compose spec은 플랫폼에 의존하지 않는 컨테이너 집합 기반 애플리케이션을 정의한다.
- **서비스(service)**
  - 애플리케이션 컴포넌트를 구성한다.
  - 컨테이너를 실행해서 플랫폼에 구현되는 추상 개념이다.
  - 어떤 서비스는 런타임 또는 플랫폼에 의존적인 **설정(config)**을 필요로 한다.
- **네트워크(network)**
  - 서비스간 통신을 구성한다.
  - 서로 연결된 서비스 컨테이너 간에 IP 라우팅을 위한 플랫폼 기능 추상체이다.
- **볼륨(volume)**
  - 서비스는 볼륨에 데이터를 저장하고 공유한다.
  - Compose spec은 전역 옵션으로 파일 시스템 마운트를 기술한다.
- config와 secret을 이용해서 컨테이너에 필요한 정책과 보안을 설정할 수 있다.

## Profile

- 프로필을 사용해서 환경에 맞게 Compose 애플리케이션 모델을 조정할 수 있다.
- `services`는 요소로 서비스 `name`을 제공하고 그 하위에 `profiles` 속성을 제공한다.
  - profiles 속성으로 프로필 목록을 정의한다.
  - profiles 속성이 설정되지 않은 서비스는 항상 활성화 된다.
  - 특정 서비스를 실행하는 경우 지정한 프로필이 활성화 된다.

```yaml
services:
	# 모든 프로필에서 활성화 된다.
	foo:
		image: foo

	# test 프로필에서 활성화 된다.
	bar:
		image: bar
		profiles:
			- test

	# test 및 debug 프로필에서 활성화 된다.
	baz:
		image: baz
		depends_on:
			- bar
		profiles:
			- test
			- debug
```

## service의 구성 요소

- service의 주요 하위
  - `build` `image` `command` `container_name` `depends_on` `environment` `expose` `ports` `healthcheck` `volumes`
- 다른 구성 요소는 문서를 참고

### build

- 컨테이너 이미지를 생성하기 위한 빌드 구성을 지정한다.
- `build` 요소는 문자열 값을 가지거나 하위 요소를 가질 수 있다.
- 아래와 같이 build에 문자열 값을 가지면 Dockerfile의 context만 가질 수 있다.
  ```yaml
  services:
  	webapp:
  		build: ./dir
  ```
- `build` 요소의 하위 요소는 다음과 같다.
  - context: Dockerfile의 context를 지정한다.
  - dockerfile: 사용할 Dockerfile의 이름을 지정한다.
  - args: Dockerfile ARG 값을 정의한다.
  - …
  ```yaml
  services:
  	webapp:
  		build:
  			context: ./dir
  			dockerfile: webapp.Dockerfile
  			args:
  				- GET_COMMIT=cdc3b19
  ```

### image

- 컨테이너를 시작할 이미지를 지정한다.
- `[<registry>/][<project>/]<image>[:<tag>|@<digest>]` 방식으로 기술해야 한다.

```yaml
image: redis
image: redis:5
image: redis@sha256:0ed5d5928d4737458944eb604cc8509e245c3e19d02ad83935398bc4b991aac7
image: library/redis
image: docker.io/library/redis
image: my_private.registry:5000/redis
```

### command

- 컨테이너 이미지(CMD)에 선언된 기본 명령을 재정의 한다.

```yaml
services:
	webapp:
		command: ["bundle", "exec", "thin", "-p", "3000"] # exec 형식
		command: bundle exec thin -p 3000  # shell 형식
```

### **container_name**

- container_name은 컨테이너의 이름을 지정한다.

```yaml
services:
	webapp:
		container_name: my-web-container
```

### \***\*depends_on\*\***

- 서비스 간의 시작 및 종료 종속성을 기술한다.
- 두 가지 방법으로 기술할 수 있다.
- Short syntax
  - 종속성 서비스 이름만 지정한다.
  ```yaml
  services:
    web:
      build: .
      depends_on:
        - db
        - redis
    redis:
      image: redis
    db:
      image: postgres
  ```
  - 위 예제는 아래와 같은 동작을 의미한다.
    - `web` 보다 `db` 및 `redis`가 빨리 생성된다.
    - `web` 이 `db` 및 `redis`보다 빨리 제거된다.
- Long syntax
  - 이 방법을 사용하면 추가 필드를 사용할 수 있다.
  - `condition`: 종속성이 충족된 것으로 간주되는 조건
    - `service_started`: (default) 의존하는 서비스가 먼저 시작됨
    - `service_healthy`: 의존하는 서비스가 먼저 시작되고, healthy 상태임
    - `service_completed_successfully`: 의존하는 서비스가 성공적으로 종료됨
  ```yaml
  services:
    web:
      build: .
      depends_on:
        db:
          condition: service_healthy
        redis:
          condition: service_started
    redis:
      image: redis
    db:
      image: postgres
  ```
  - 위 예제는 아래와 같은 동작을 의미한다.
    - `web` 이 실행되기 전에 `db` 가 healthy 상태이고 `redis`가 시작된 상태이다.

### environment

- 컨테이너에 설정된 환경변수를 정의한다.
- 두 가지 방법으로 환경변수를 정의할 수 있다.
- Map syntax
  ```yaml
  environment:
    RACK_ENV: development
    SHOW: 'true'
    USER_INPUT:
  ```
- Array syntax
  ```yaml
  environment:
    - RACK_ENV=development
    - SHOW=true
    - USER_INPUT
  ```

### expose

- 컨테이너에서 노출해야 하는 포트를 정의한다.
- 호스트 내부의 다른 컨테이너들만 엑세스가 가능하다.

```yaml
expose:
  - '3000'
  - '8000'
```

### ports

- 컨테이너 포트를 노출한다.
- `[HOST:]CONTAINER[/PROTOCOL]`

```yaml
ports:
  - '3000' # 호스트의 랜덤 포트, 컨테이너의 3000번 포트
  - '3000-3005' # 컨테이너의 포트 번호 범위내에서 할당
  - '8000:8000'
  - '9090-9091:8080-8081'
  - '49100:22'
  - '127.0.0.1:8001:8001'
  - '127.0.0.1:5000-5010:5000-5010'
  - '6060:6060/udp'
```

```
expose vs ports
- ports는 호스트와 컨테이너의 포트를 바인딩 시킨다.
- ports는 호스트 포트와 컨테이너 포트를 모두 노출시키기 때문에
	호스트 내부 컨테이너 간에는 노출된 포트로 접근할 수 있지만,
	호스트 외부에서는 컨테이너와 바인딩된 포트로 접근해야 한다.

- expose는 호스트 포트를 공개하지 않고 컨테이너의 포트만 공개한다.
- 따라서 호스트 외부에서는 컨테이너에 접근할 수 없고 컨테이너 끼리만 접근이 가능하다.
```

### healthcheck

- 서비스 컨테이너가 healthy 상태인지 확인한다.
- healthcheck의 하위 구성 요소는 아래와 같다.

| Element      | Description                                                                    |
| ------------ | ------------------------------------------------------------------------------ |
| disable      | true 또는 false로 healthcheck 여부를 설정                                      |
| test         | 컨테이너 상태를 확인하기 위한 명령 정의. exec 방식과 shell 방식 모두 사용 가능 |
| interval     | 헬스 체크 간격                                                                 |
| timeout      | 헬스 체크 타임아웃 기준                                                        |
| retries      | 타임아웃 횟수                                                                  |
| start_period | 컨테이너 시작 후 대기 시간                                                     |

```yaml
healthcheck:
  test: ['CMD', 'curl', '-f', 'http://localhost']
  interval: 1m30s
  timeout: 10s
  retries: 3
  start_period: 40s
```

### volumes

- 서비스 컨테이너에서 엑세스하는 마운트 호스트 경로 또는 정의한 볼륨을 기술한다.
- 마운트가 단일 서비스에서만 사용되는 경우 최상위 volumes 요소 대신 services 하위 요소로 선언할 수 있다.
- 여러 서비스가 볼륨을 재사용하려면 최상위 volumes 요소에서 정의된 볼륨을 기술해야 한다.
- `VOLUME:CONTAINER_PATH`
- `VOLUME:CONTAINER_PATH:ACCESS_MODE`
  - `VOLUME`: 호스트 경로 또는 최상위 volumes 요소에서 정의한 볼륨 이름
  - `CONTAINER_PATH`: 컨테이너의 경로
  - `ACCESS_MODE`: 목록은 `,`으로 구분된다.
    - `rw` : 읽기 및 쓰기(기본값)
    - `ro` : 읽기 전용
    - `z` : 호스트 마운트 경로가 여러 컨테이너 간에 공유됨 (SELinux 옵션)
    - `Z` : 호스트 마운트 경로가 다른 컨테이너 간에 공유되 지 않음 (SELinux 옵션)

```yaml
services:
  backend:
    image: awesome/database
    volumes:
      - db-data:/etc/data

  backup:
    image: backup-service
    volumes:
      - db-data:/var/lib/backup/data

volumes:
  db-data:
    driver_opts:
      device: /host/path/to/volume
```

```yaml
services:
  backend:
    image: awesome/database
    volumes:
      - /dir1:/etc/data

  backup:
    image: backup-service
    volumes:
      - /dir2:/var/lib/backup/data:ro
```
