# Dockerfile refer

## Dockerfile

- Dockerfile을 이용해서 Docker를 자동으로 빌드할 수 있다.
- `docker image build` 명령어를 사용해서 Dockerfile에 명시된 command line을 수행하도록 할 수 있다.

```bash
docker image build [Dockerfile 경로]
```

## .dockerignore

- Dockerfile과 같은 디렉토리에 있는 파일을 컨텍스트라 한다.
- 컨텍스트에서 제외하고 싶은 파일이나 디렉터리를 작성한다.

```bash
dir/*.txt # dir 디렉토리의 확장자가 txt인 파일 제외
.git # .git 제외
!README.md # README를 제외한 모든 md 파일 제외
```

## Format

- Dockerfile 포맷은 다음과 같다.

```docker
# Comment
INSTRUCTION arguments
```

- INSTRUCTION은 대/소문자를 구분하지 않지만, 대문자로 작성하는 것이 컨벤션이다.
- Dockerfile은 반드시 `FROM` INSTRUCTION으로 시작해야 한다.

## Environment replacement

- Dockerfile의 일부 명령어는 `ENV`를 통해 정의한 환경변수를 사용할 수 있다.
- 환경변수는 `$variable_name` 또는 `${variable_name}` 방식으로 사용할 수 있다.
- `${variable_name}` 는 다음과 같은 연산자를 지원한다.
  - `${variable_name:-word}` 는 `variable` 이 정의되어있지 않다면 `word` 로 대체된다.
  - `${variable_name:+word}` 는 `variable` 이 정의되어 있다면 `word` 가 그 값으로 대체되고 정의되어있지 않다면 빈 문자열로 대체된다.
- 환경 변수는 아래 명령어에 적용할 수 있다.
  - `ADD`
  - `COPY`
  - `ENV`
  - `EXPOSE`
  - `FROM`
  - `LABEL`
  - `STOPSIGNAL`
  - `USER`
  - `VOLUME`
  - `WORKDIR`
  - `ONBUILD` (위의 명령어들과 함께 사용되었을 때)

## FROM

```docker
FROM [--platform=<platform>] <image> [AS <name>]
FROM [--platform=<platform>] <image>[:<tag>] [AS <name>]
FROM [--platform=<platform>] <image>[@<digest>] [AS <name>]
```

- 생성할 이미지의 베이스 이미지를 설정한다.
- 멀티 플랫폼 이미지를 참조할 때 `--platform` 사용하여 플랫폼을 특정할 수 있다.
  - linux/amd64
  - linux/arm64
  - windows/amd64
  - …
- <image> 뒤에 `tag`와 `digest`는 선택적으로 사용한다. 둘 다 생략했다면 `tag`로 latest가 사용된다.
- AS를 사용해서 빌드 단계에 이름을 줄 수 있다.

## RUN

```docker
RUN <command> # shell 형식
RUN ["executable", "param1", "param2"] # exec 형식
```

- 현재 이미지의 새 레이어에서 실행되고 결과를 커밋되고, 커밋된 이미지는 Dockerfile의 다음 스텝에서 사용된다.
- RUN 명령어는 두 가지 방식을 따른다.
  - shell 형식
    - 내부적으로 shell 명령어를 호출하여 <command>를 호출한다.
  - exec 형식
    - 사용자가 executable(`/bin/sh`, `/bin/bash`…) 을 명시하여 명령어를 실행할 수 있다.

## CMD

```docker
CMD <command> param1 param2 # shell 형식
CMD ["executable", "param1", "param2"] # exec 형식
CMD ["param1", "param2"] # ENTRYPOINT 파라미터
```

- 컨테이너가 실행될 때 수행될 default 명령어를 설정하거나, `ENTRYPOINT`의 default 파라미터를 설정한다.
- 단 하나의 CMD 명령어가 존재할 수 있다.
- CMD는 세 가지 방식을 따른다.
  - shell 형식
    - 내부적으로 shell 명령어를 호출하여 <command>를 호출한다.
  - exec 형식
    - 사용자가 executable(`/bin/sh`, `/bin/bash`…) 을 명시하여 명령어를 실행할 수 있다.
  - ENTRYPOINT 파라미터
    - ENTIRYPOINT에 사용될 기본 파라미터를 설정한다.

## ENTIRYPOINT

```docker
ENTRYPOINT command param1 param2 # shell 형식
ENTRYPOINT ["executable", "param1", "param2"] #exec 형식
```

- 컨테이너가 실행될 때 가장 먼저 수행되는 명령어를 지정한다.
- ENTIRYPOINT 명령어는 두 가지 방식을 따른다.
  - shell 형식
    - 내부적으로 shell 명령어를 호출하여 <command>를 호출한다.
  - exec 형식
    - 사용자가 executable(`/bin/sh`, `/bin/bash`…) 을 명시하여 명령어를 실행할 수 있다.

## LABEL

```docker
LABEL <key>=<value> <key>=<value> <key>=<value> ... # 한 줄에 작성
LABEL <key>=<value> \ # 여러 줄에 작성
			<key>=<value> \
			<key>=<value> \
			...
```

- 키=밸류 방식으로 이미지에 메타데이터를 추가한다.
- LABEL은 기본 또는 상위 이미지의 LABEL을 현재 이미지에 상속 받는다.
- 이미지의 라벨은 `docker image inspect` 명령어로 확인할 수 있다.

## EXPOSE

```docker
EXPOSE <port> [<port>/<protocol>...]
```

- Docker에게 컨테이너가 런타임에서 어떤 네트워크 포트를 사용할 지 알려준다.
- TCP, UDP를 사용할 수 있고, 명시하지 않는다면 TCP가 사용된다.
- 실제로 포트를 공개하지는 않지만 `docker run -P` 명령어를 사용하면 호스트 운영체제의 랜덤 포트가 컨테이너의 EXPOSE로 명시한 포트에 매핑된다.

## ENV

```docker
ENV <key> <value>
ENV <key>=<value> ...
```

- 환경변수 <key>를 <value>로 설정한다.
- 컨테이너 실행 시 `docker container run —env` 명령어로 변경할 수 있다.

## ADD

```docker
ADD [--chown=<user>;<group>] <src>... <dest>
ADD [--chown=<user>;<group>] ["<src>",... "<dest>"]
```

- <src>의 파일, 디렉토리, 리모트 파일의 URL을 <dest>에 추가한다.
- `*`과 `?` 과 같은 패턴을 사용할 수도 있다.

```docker
# hom으로 시작하는 모든 파일 추가
ADD home* /dir/
# ?는 단일 문자 대체
ADD hom?.txt /dir/
```

## COPY

```docker
COPY [--chown=<user>:<group>] <src>... <dest>
COPY [--chown=<user>:<group>] ["<src>",... "<dest>"]
```

- 이미지에 호스트의 파일이나 디렉토리를 복사한다.
- `ADD`는 대상이 압축파일인 경우 해체하여 복사하는 등 기능을 제공하지만, COPY는 복사만 수행한다.

## VOLUME

```docker
VOLUME ["/data"]
```

- 이미지에 볼륨을 할당한다.
- 디렉토리의 내용을 호스트에 저장하도록 설정하지만, 호스트의 특정 디렉토리와 연결하지는 못한다.

## USER

```docker
USER <user>[:<group>]
USER <UID>[:<GID>]
```

- 명령을 실행할 유저 이름(UID)과 선택적으로 그룹(GID)를 지정한다.
- 아래와 같은 명령에 대해 적용된다.
  - `RUN`
  - `CMD`
  - `ENTRYPOINT`

## WORKDIR

```docker
WORKDIR /path/to/workdir
```

- Dockerfile에서 정의한 명령을 실행하기 위한 디렉토리를 지정하며, 경로가 존재하지 않으면 생성한다.
- Dockerfile에서 여러 번 사용 가능하며, 상대 경로를 지정한 경우 기존 WORKDIR에 대한 상대 경로가 된다.
  ```docker
  WORKDIR /a
  WORKDIR b
  WORKDIR c
  RUN pwd
  ```
  - result: /a/b/c
- 아래와 같은 명령에 대해 적용된다.
  - `RUN`
  - `CMD`
  - `ENTRYPOINT`
  - `COPY`
  - `ADD`

## ARG

```docker
ARG <name>[=<default value>]
```

- Dockerfile에서 사용할 변수를 정의한다.
- `ENV`와 달리 Dockerfile 내부에서만 사용 가능하다.

## ONBUILD

```docker
ONBUILD <INSTRUCTION>
```

- 생성하는 이미지를 베이스로한 다른 Dockerfile을 빌드할 때 실행하고 싶은 명령을 지정한다.
  ```docker
  ONBUILD ADD . /app/src
  ONBUILD RUN /usr/local/bin/python-build --dir /app/src
  ```

## STOPSIGNAL

```docker
STOPSIGNAL signal
```

- 컨테이너가 종료되기 위한 시그널 번호 또는 시그널명을 지정한다.

## \***\*HEALTHCHECK\*\***

```docker
HEALTHCHECK [OPTIONS] CMD command # 사용할 명령을 지정 (curl 등)
HEALTHCHECK NONE #기본 이미지에서 상속된 healthcheck 사용 안함
```

- 컨테이너가 잘 동작하는지 확인한다.
- 옵션은 다음과 같다.

| Option           | Default | Description                |
| ---------------- | ------- | -------------------------- |
| --interval=n     | 30s     | 헬스 체크 간격             |
| --timeout=n      | 30s     | 헬스 체크 타임아웃 기준    |
| --retries=n      | 3       | 타임아웃 횟수              |
| --start_period=n | 0s      | 컨테이너 실행 후 대기 시간 |

## SHELL

```docker
SHELL ["executable", "parameters"]
```

- shell 형식으로 명령을 실행할 때 기본 shell을 설정한다.
- RUN, CMD, ENTYPOINT 등 shell 형식을 사용할 때 SHELL에서 지정한 기본 shell이 사용된다.
  - default
    - linux: `["/bin/sh", "-c"]`
    - window: `["cmd", "/S", "/C"]`
