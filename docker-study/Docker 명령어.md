# Docker 명령어

## 참고

- 자세한 옵션과 설명은 아래 문서를 참조

[docker](https://docs.docker.com/engine/reference/commandline/docker/)

## `docker COMMAND`

### search

```bash
docker search [OPTIONS] TERM
```

- Docker Hub에 있는 이미지를 검색한다.

| Option      | Default | Description                                                                                                                                               |
| ----------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| --filter -f |         | key=value 포맷으로 검색을 필터링 한다.</br>- stars: star의 개수 (int)</br>- is-automated: 자동 빌드 여부 (boolean)</br>- is-official: 공식 여부 (boolean) |
| --limit     | 25      | 검색 결과의 최대 개수                                                                                                                                     |
| --no-trunc  |         | 검색 결과 텍스트를 생략하지 않고 전부 보여준다.                                                                                                           |

### pull

```bash
docker pull [OPTIONS] NAME[:TAG|DIGEST]
```

- Docker Hub에서 이미지를 내려 받는다.
- NAME 앞에 사용자 명을 지정하지 않으면 공식 이미지를 내려 받는다.

### push

```bash
docker push [OPTIONS] NAME[:TAG]
```

- 이미지를 Docker Hub 또는 개인 저장소에 업로드 한다.

### rm

```bash
docker rm [OPTIONS] CONTAINER [CONTAINER...]
```

- 하나 또는 하나 이상 컨테이너를 제거한다.

| Option       | Default | Description                                             |
| ------------ | ------- | ------------------------------------------------------- |
| --force -f   |         | 동작 중인 컨테이너를 강제로 제거한다.                   |
| --volumes -v |         | 컨테이너와 관련된 이름을 지정하지 않은 볼륨을 제거한다. |

### rmi

```bash
docker rmi [OPTIONS] IMAGE [IMAGE...]
```

- 하나 또는 하나 이상 이미지를 제거한다.

### start

```bash
docker start [OPTIONS] CONTAINER [CONTAINTER...]
```

- 하나 또는 하나 이상의 컨테이너를 시작한다.
- 이미 실행 중인 컨테이너를 다시 시작하려면 `restart`를 사용한다.

### stop

```bash
docker stop [OPTIONS] CONTAINER [CONTAINER...]
```

- 하나 또는 하나 이상의 컨테이너를 중지시킨다.

### tag

```bash
docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
```

- 이미지에 태그를 설정한다.
- 숫자 및 `_` `-` `.` 으로 이름을 시작할 수 없다.

### logs

```bash
docker logs [OPTIONS] CONTAINER
```

- 컨테이너의 로그를 보여준다.

| Option          | Default | Description                                          |
| --------------- | ------- | ---------------------------------------------------- |
| --follow -f     |         | 로그를 계속 추적하면서 출력한다.                     |
| --tail -n       | all     | 가장 최근 로그부터 출력할 로그의 라인 수를 설정한다. |
| --timestamps -t |         | 시간 데이터를 보여준다.                              |

### exec

```bash
docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
```

- 실행 중인 컨테이너에 명령어를 실행한다.

| Option           | Default | Description                                          |
| ---------------- | ------- | ---------------------------------------------------- |
| --detach -d      |         | 명령어를 백그라운드로 실행한다.                      |
| --env -e         |         | 환경변수를 설정한다.                                 |
| --interactive -i |         | 표준입력을 유지한다.                                 |
| --tty -t         |         | 터미널(pseudo-TTY)을 할당한다.                       |
| --workdir -w     | /       | 컨테이너 내부에서 명령이 실행될 디렉토리를 설정한다. |

### run

```bash
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

- 이미지를 컨테이너로 생성하고 실행한다.

| Option           | Default | Description                                                    |
| ---------------- | ------- | -------------------------------------------------------------- |
| —detach -d       |         | 컨테이너의 ID를 출력하고 백그라운드로 실행한다.                |
| --interactive -i |         | 표준입력을 유지한다.                                           |
| --tty -t         |         | 터미널(pseudo-TTY)을 할당한다.                                 |
| --name           | random  | 컨테이너에 이름을 지정한다.                                    |
| --env -e         |         | 환경변수를 설정한다.                                           |
| --publish -p     |         | host(port):container(port) 포맷으로 publish와 bind를 설정한다. |
| --volume -v      |         | 볼륨을 마운트 시킨다.                                          |
| --workdir -w     | /       | 컨테이너 내부에서 명령이 실행될 디렉토리를 설정한다.           |
| --rm             |         | 종료되면 해당 컨테이너를 삭제한다.                             |

- `-i` 옵션과 `-t` 옵션은 일반적으로 같이 사용한다.

  - `-t` 옵션만 사용한 경우 `docker run -t ubuntu:latest`
    ```bash
    > root@1480068fa25b:/# ls
    > (no result)
    ```
  - `-i` 옵션만 사용한 경우 `docker run -i ubuntu:latest`
    ```bash
    > tty
    > not a tty (result)
    ```
  - `-it` 옵션을 사용한 경우 `docker run -it ubuntu:latest`

    ```bash
    > root@9d108d34222d:/# tty
    > /dev/pts/0

    (`docker exec -it CONTAINER /bin/sh` 로 추가 연결)
    > # tty
    > /dev/pts/1 (result)
    > echo "hello, 0 from 1" > /dev/pts/0

    (/dev/pts/0)
    > root@9d108d34222d:/# hello, 0 from 1
    ```

```
TTY란?
- 원격 타자기(TeleTYpewriter)
- Linux에서 터미널을 의미한다.
```

### build

```bash
docker build [OPTIONS] [PATH or URL]
```

- Dockerfile을 이용해서 이미지를 생성한다.
- Dockerfile의 경로는 PATH 또는 URL을 사용할 수 있다.
  - `docker build .`
  - `docker build github.com/creack/docker-firefox`

| Option     | Default            | Description                                       |
| ---------- | ------------------ | ------------------------------------------------- |
| --tag -t   | 테스트 해보고 추가 | name:tag 포맷으로 이미지의 태그를 설정한다.       |
| —-file -f  | PATH/Dockerfile    | 사용할 Dockerfile의 이름을 지정한다.              |
| --no-cache |                    | Dockerfile 명령어 실행 시 캐시를 사용하지 않는다. |

## `docker image COMMAND`

### build

```bash
docker image build [OPTIONS] [Dockerfile PATH | URL]
```

- `docker build` 명령어와 같다.

### history

```bash
docker image history [OPTIONS] IMAGE
```

- 이미지 히스토리를 보여준다.

| Option     | Default | Description                                |
| ---------- | ------- | ------------------------------------------ |
| --no-trunc |         | 결과 텍스트를 생략하지 않고 전부 보여준다. |
| --quiet -q |         | ID만 보여준다.                             |

### inspect

```bash
docker image inspect [OPTIONS] IMAGE [IMAGE...]
```

- 하나 또는 하나 이상 이미지의 상세 내역을 보여준다.

### ls

```bash
docker image ls [OPTIONS] [REPOSITORY[:TAG]]
```

- 이미지 목록를 보여준다.

### prune

```bash
docker image prune [OPTIONS]
```

- 사용하지 않는 이미지를 삭제한다.

### pull

```bash
docker image pull [OPTIONS] NAME[:TAG|DIGEST]
```

- `docker pull` 명령어와 같다.

### push

```bash
docker push [OPTIONS] NAME[:TAG]
```

- `docker push` 명령어와 같다.

### rm

```bash
docker image rm [OPTIONS] IMAGE [IMAGE...]
```

- `docker rmi` 명령어와 같다.

### tag

```bash
docker image tag SOURCE_IMAGE[:TAG] TARGET[:TAG]
```

- `docker tag` 명령어와 같다.

## `docker container COMMAND`

### attach

```bash
docker container attach [OPTIONS] CONTAINER
```

- 표준 입출력을 컨테이너에 연결한다.

### commit

```bash
docker container commit [OPTIONS] CONTAINER [REPOSITORY:[TAG]]
```

- 컨테이너의 변경사항을 이미지로 생성한다.

| Option       | Default | Description                        |
| ------------ | ------- | ---------------------------------- |
| --author -a  |         | 커밋한 사용자를 작성한다.          |
| --message -m |         | 커밋 메시지를 작성한다.            |
| --pause -p   | true    | 커밋하는 동안 컨테이너를 중단한다. |

### create

```bash
docker container create [OPTIONS] IMAGE [COMMAND] [ARG...]
```

- 새로운 컨테이너를 생성한다.
- run과는 달리 컨테이너를 생성하고 실행하지는 않는다.

### diff

```bash
docker container diff CONTAINERr
```

- 컨테이너의 변경사항을 확인한다.
- A: 추가, C: 변경, D: 삭제

### exec

```bash
docker container exec [OPTIONS] CONTAINER COMMAND [ARG...]
```

- `docker exec` 명령어와 같다.

### logs

```bash
docker container logs
```

- `docker logs` 명령어와 같다.

### ls

```bash
docker container ls [OPTIONS]
```

- 컨테이너 목록을 보여준다.

| Option     | Default           | Description                                |
| ---------- | ----------------- | ------------------------------------------ |
| --all -a   | running container | 모든 컨테이너를 보여준다.                  |
| --size -s  |                   | 사이즈를 같이 보여준다.                    |
| --no-trunc |                   | 출력 결과 텍스트를 축약하지 않고 보여준다. |

### prune

```bash
docker container prune [OPTIONS]
```

- stop 상태인 모든 컨테이너를 제거한다.

### rename

```bash
docker container rename CONTAINER NEW_NAME
```

- 컨테이너 이름을 변경한다.

### rm

```bash
docker container rm [OPTIONS] CONTAINER [CONTAINER...]
```

- `docker rmi` 명령어와 같다.

### run

```bash
docker container run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

- `docker run` 명령어와 같다.

### start

```bash
docker container start [OPTIONS] CONTAINER [CONTAINER...]
```

- `docker start` 명령어와 같다.

### top

```bash
docker container top CONTAINER [ps OPTIONS]
```

- 실행 중인 컨테이너 목록을 본다.

## `docker compose COMMAND`

### build

```bash
docker compose build [SERVICE...]
```

- 서비스를 빌드한다.

| Option     | Default | Description                                   |
| ---------- | ------- | --------------------------------------------- |
| --no-cache |         | 이미지를 빌드할 때 캐시를 사용하지 않는다.    |
| --pull     |         | 항상 새로운 이미지 버전 내려 받기를 시도한다. |

### create

```bash
docker compose create [SERVICE...]
```

- 서비스의 컨테이너를 생성한다.

| Option     | Default | Description                                 |
| ---------- | ------- | ------------------------------------------- |
| --build    |         | 컨테이너를 시작하기 전에 이미지를 빌드한다. |
| --no-build |         | 이미지를 빌드하지 않는다.                   |

### down

```bash
docker compose down
```

- 컨테이너를 중단하고 제거한다.

| Option          | Default | Description                                      |
| --------------- | ------- | ------------------------------------------------ |
| --remove-orphan |         | 컴포즈 파일에 정의되지 않은 컨테이너를 제거한다. |
| --rmi           |         | 서비스에 사용된 이미지를 제거한다.               |
| --volumnes -v   |         | 이름이 지정된 volume을 제거한다.                 |

### events

```bash
docker compose events [OPTIONS] [--] [SERVICE...]
```

- 컨테이너로부터 실시간 이벤트를 받는다.

| Option | Default | Description        |
| ------ | ------- | ------------------ |
| --json |         | json으로 출력한다. |

### logs

```bash
docker compose logs [SERVICE...]
```

- 컨테이너들의 로그를 출력한다.

| Option          | Default | Description                                          |
| --------------- | ------- | ---------------------------------------------------- |
| --follow -f     |         | 로그를 계속 추적하면서 출력한다.                     |
| --tail -n       | all     | 가장 최근 로그부터 출력할 로그의 라인 수를 설정한다. |
| --timestamps -t |         | 시간 데이터를 보여준다.                              |

### ls

```bash
docker compose ls
```

- 컴포즈로 실행 중인 컨테이너 목록을 조회한다.

| Option   | Default | Description                      |
| -------- | ------- | -------------------------------- |
| --all -a |         | 중지 상태인 컨테이너를 보여준다. |

### pull

```bash
docker compose pull [SERVICE...]
```

- 컴포즈 파일에 정의된 이미지를 내려 받는다.

| Option                 | Default | Description                                                |
| ---------------------- | ------- | ---------------------------------------------------------- |
| --ignore-pull-failures |         | 내려받기에 실패하는 이미지를 무시한다.                     |
| --include-deps         |         | 컴포즈 파일에 의존성으로 선언된 이미지를 같이 내려 받는다. |

### push

```bash
docker compose push [SERVICE...]
```

- 컴포즈 파일의 이미지를 푸쉬한다.

| Option                 | Default | Description                        |
| ---------------------- | ------- | ---------------------------------- |
| --ignore-push-failures |         | 푸쉬에 실패하는 이미지를 무시한다. |

### restart

```bash
docker compose restart
```

- 컴포즈 파일의 컨테이너들을 재시작한다.

### rm

```bash
docker compose rm [SERVICE...]
```

- 중단된 서비스의 컨테이너들을 제거한다.

| Option       | Default | Description                           |
| ------------ | ------- | ------------------------------------- |
| --volumes -v |         | 이름이 지정되지 않은 볼륨을 제거한다. |

### run

```bash
docker compose run [OPTIONS] [-v VOLUME...] [-p PORT...] [-e KEY=VAL...] [-l KEY=VALUE...] SERVICE [COMMAND] [ARGS...]
```

- 컨테이너들에 명령어를 일회성으로 사용한다.

### start

```bash
docker compose start [SERVICE]
```

- 서비스를 시작한다.

### stop

```bash
docker compose stop [SERVICE...]
```

- 서비스를 중지한다.

### up

```bash
docker compose up [SERVICE...]
```

- 서비스의 컨테이너를 생성하고 시작한다.

| Option      | Default | Description                                 |
| ----------- | ------- | ------------------------------------------- |
| --detach -d |         | 백그라운드로 수행한다.                      |
| --build     |         | 컨테이너를 시작하기 전에 이미지를 빌드한다. |
