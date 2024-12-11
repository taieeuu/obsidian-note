---
Date: 2024-06-01
tags:
  - docker
  - devops
---
### 映像的多階段建置
##### 我們使用 C 來舉例
假設有一個C的程序，我們想用Docker進行編譯，然後執行可執行檔。
```c
#include <stdio.h>

void main(int argc, char *argv[])
{
    printf("hello %s\n", argv[argc - 1]);
}
```

建構一個Docker映像，因為選擇C的環境，所以我們gcc這個映像。
```dockerfile
FROM gcc:9.4

COPY hello.c /src/hello.c

WORKDIR /src

RUN gcc --static -o hello hello.c

ENTRYPOINT [ "/src/hello" ]

CMD []
```

建置和測試
```shell
$ docker build -t hello .
Sending build context to Docker daemon   5.12kB
Step 1/6 : FROM gcc:9.4
---> be1d0d9ce039
Step 2/6 : COPY hello.c /src/hello.c
---> Using cache
---> 70a624e3749b
Step 3/6 : WORKDIR /src
---> Using cache
---> 24e248c6b27c
Step 4/6 : RUN gcc --static -o hello hello.c
---> Using cache
---> db8ae7b42aff
Step 5/6 : ENTRYPOINT [ "/src/hello" ]
---> Using cache
---> 7f307354ee45
Step 6/6 : CMD []
---> Using cache
---> 7cfa0cbe4e2a
Successfully built 7cfa0cbe4e2a
Successfully tagged hello:latest
$ docker image ls
REPOSITORY     TAG          IMAGE ID       CREATED       SIZE
hello          latest       7cfa0cbe4e2a   2 hours ago   1.14GB
gcc            9.4          be1d0d9ce039   9 days ago    1.14GB
$ docker run --rm -it hello docker
hello docker
$ docker run --rm -it hello world
hello world
$ docker run --rm -it hello friends
hello friends
$
```
可以看到鏡像非常大，1.14GB

注意當我們把hello.c編譯完成以後，並不需要這麼大的GCC環境，一個小小的alpine鏡像就可以了。

這時候我們就可以使用多階段建置了。

```dockerfile
FROM gcc:9.4 AS builder

COPY hello.c /src/hello.c

WORKDIR /src

RUN gcc --static -o hello hello.c


FROM alpine:3.13.5

COPY --from=builder /src/hello /src/hello

ENTRYPOINT [ "/src/hello" ]

CMD []
```

測試
```shell
$ docker build -t hello-alpine -f Dockerfile-new .
Sending build context to Docker daemon   5.12kB
Step 1/8 : FROM gcc:9.4 AS builder
---> be1d0d9ce039
Step 2/8 : COPY hello.c /src/hello.c
---> Using cache
---> 70a624e3749b
Step 3/8 : WORKDIR /src
---> Using cache
---> 24e248c6b27c
Step 4/8 : RUN gcc --static -o hello hello.c
---> Using cache
---> db8ae7b42aff
Step 5/8 : FROM alpine:3.13.5
---> 6dbb9cc54074
Step 6/8 : COPY --from=builder /src/hello /src/hello
---> Using cache
---> 18c2bce629fb
Step 7/8 : ENTRYPOINT [ "/src/hello" ]
---> Using cache
---> 8dfb9d9d6010
Step 8/8 : CMD []
---> Using cache
---> 446baf852214
Successfully built 446baf852214
Successfully tagged hello-alpine:latest
$ docker image ls
REPOSITORY     TAG          IMAGE ID       CREATED       SIZE
hello-alpine   latest       446baf852214   2 hours ago   6.55MB
hello          latest       7cfa0cbe4e2a   2 hours ago   1.14GB
demo           latest       079bae887a47   2 hours ago   125MB
gcc            9.4          be1d0d9ce039   9 days ago    1.14GB
$ docker run --rm -it hello-alpine docker
hello docker
$ docker run --rm -it hello-alpine world
hello world
$ docker run --rm -it hello-alpine friends
hello friends
$
```

可以看這個鏡像非常小，只有6.55MB
##### Go來舉例
```go
package main

import (
    "log"
    "net/http"
)

func test(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte("Hello golang"))
}

func main() {
    log.SetFlags(log.LstdFlags | log.Lshortfile)
    log.Println("start server on [localhost:8080] ...")
    http.HandleFunc("/", test)
    err := http.ListenAndServe(":8080", nil)
    if err != nil {
        log.Fatal(err)
    }
}
```

建構一個 Docker映像，因為選擇了 Go 的環境，所以我們用 golang 這個映像。
```dockerfile
FROM golang:alpine3.17 AS builder

COPY main.go /src/app.go

WORKDIR /src

RUN go build app.go

EXPOSE 8080

ENTRYPOINT [ "/src/app" ]
```

建置和測試
```shell
$ docker build -t hello-go .
Sending build context to Docker daemon  6.512MB
Step 1/6 : FROM golang:alpine3.17 AS builder
---> 3257bc8ee9f7
Step 2/6 : COPY main.go /src/app.go
---> b0156e003e2d
Step 3/6 : WORKDIR /src
---> Running in 7976422fe214
Removing intermediate container 7976422fe214
---> 122042396c76
Step 4/6 : RUN go build app.go
---> Running in f321f6a73147
Removing intermediate container f321f6a73147
---> 21236778ceee
Step 5/6 : EXPOSE 8080
---> Running in d47b6e2fb836
Removing intermediate container d47b6e2fb836
---> 133988261356
Step 6/6 : ENTRYPOINT [ "/src/app" ]
---> Running in 7f19bd8952b4
Removing intermediate container 7f19bd8952b4
---> 2ccb4f220a22
Successfully built 2ccb4f220a22
Successfully tagged hello-go:latest
$ docker image ls
REPOSITORY                                                           TAG                 IMAGE ID            CREATED             SIZE
hello-go                                                             latest              2ccb4f220a22        19 minutes ago      321MB
golang                                                               alpine3.17          3257bc8ee9f7        3 days ago          254MB
$ docker run -p 8080:8080 -it hello-go
2023/02/14 18:16:11 app.go:14: start server on [localhost:8080] ...
```

```shell
$ curl localhost:8080
Hello golang
```

可以看到鏡像也很大，321MB，同樣的，我們採用多階段構建。
```dockerfile
FROM golang:alpine3.17 AS builder

COPY main.go /src/app.go

WORKDIR /src

RUN go build app.go

FROM alpine:3.17.0

COPY --from=builder /src/app /src/app

EXPOSE 8080

ENTRYPOINT [ "/src/app" ]
```

測試
```shell
$ docker build -t hello-go-alpine -f ./df .
Sending build context to Docker daemon  6.512MB
Step 1/8 : FROM golang:alpine3.17 AS builder
---> 3257bc8ee9f7
Step 2/8 : COPY main.go /src/app.go
---> 167672dc57ce
Step 3/8 : WORKDIR /src
---> Running in a53f0f84c92d
Removing intermediate container a53f0f84c92d
---> cc8ee771cdbd
Step 4/8 : RUN go build app.go
---> Running in 9e8e575af675
Removing intermediate container 9e8e575af675
---> e8e7c7219cd5
Step 5/8 : FROM alpine:3.17.0
3.17.0: Pulling from library/alpine
c158987b0551: Pull complete
Digest: sha256:8914eb54f968791faf6a8638949e480fef81e697984fba772b3976835194c6d4
Status: Downloaded newer image for alpine:3.17.0
---> 49176f190c7e
Step 6/8 : COPY --from=builder /src/app /src/app
---> 8121bedd9a21
Step 7/8 : EXPOSE 8080
---> Running in 93a02551712d
Removing intermediate container 93a02551712d
---> e91f0c467511
Step 8/8 : ENTRYPOINT [ "/src/app" ]
---> Running in aef94175c85d
Removing intermediate container aef94175c85d
---> f3ee197cba4f
Successfully built f3ee197cba4f
Successfully tagged hello-go-alpine:latest
$ docker image ls
REPOSITORY                                                           TAG                 IMAGE ID            CREATED             SIZE
hello-go-alpine                                                      latest              f3ee197cba4f        31 seconds ago      13.6MB
<none>                                                               <none>              e8e7c7219cd5        46 seconds ago      321MB
hello-go                                                             latest              2ccb4f220a22        24 minutes ago      321MB
golang                                                               alpine3.17          3257bc8ee9f7        3 days ago          254MB
$ docker run --rm -p 8080:8080 -it hello-go-alpine
2023/02/14 18:42:29 app.go:14: start server on [localhost:8080] ...
```

現在鏡像只有13.6MB

>[!info]
>由此可知，我們先使用一個環鏡編譯檔案(像是C、C#、GO2都會需要編譯)，再將其傳遞給下一個環境，這樣可以有效降低 image的大小。**注意** 最後只會包含最後 `From` 指令的環境。
