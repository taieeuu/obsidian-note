---
Date: 2024-06-08
tags:
  - docker
  - devops
---
>[!info]
>**重點**
>- CVE [https://cve.mitre.org/](https://cve.mitre.org/)
>- 程式碼掃描[https://snyk.io/](https://snyk.io/)
>- 鏡像掃描[https://github.com/aquasecurity/trivy#os-packages](https://github.com/aquasecurity/trivy#os-packages)

![[容器安全 1.png]]
## app
程式碼層面的漏洞
## 鏡像
- 選擇官方認證的基礎鏡像
- 鏡像的漏洞掃描
## 容器
- 容器的漏洞掃描
- 容器的即時監控
## 主機
- Linux Kernel
- Kernel namespaces
- Control groups
## Docker 配置掃描
Docker Bench for Security
[https://github.com/docker/docker-bench-security](https://github.com/docker/docker-bench-security)

我們可以透過上述的連接下載，運行就會自動幫你檢測，**如檢查出Warring並不一定代表有風險且也沒有一個軟體沒有風險，須看是否有修改必要，以及取捨**

## 代碼和鏡像掃描
CVE [https://cve.mitre.org/](https://cve.mitre.org/)

程式碼掃描[https://snyk.io/](https://snyk.io/)

鏡像掃描[https://github.com/aquasecurity/trivy#os-packages](https://github.com/aquasecurity/trivy#os-packages)

## 容器運行監控
- https://sysdig.com/
## 如何在Docker Image的構建階段保護敏感信息
範例1:
```dockerfile
FROM gcc:9.4

RUN git clone https://ghp_xxxxxxxxxxxxxxxxxxxxxxxxx@github.com/xiaopeng163/helloworld-c src

WORKDIR /src

RUN gcc --static -o hello hello.c

ENTRYPOINT [ "/src/hello" ]

CMD []
```
>[!info]
>可以看到這邊的 ghp_xxxxxxxxxxxxxxxxxxxxxxxxx 唯一個 token ，直接以明碼的形式存放在image是很危險的，我們應該要避免這樣子寫法。

範例2:
```dockerfile
FROM gcc:9.4

ARG TOKEN

RUN git clone https://$TOKEN@github.com/xiaopeng163/helloworld-c src

WORKDIR /src

RUN gcc --static -o hello hello.c

ENTRYPOINT [ "/src/hello" ]

CMD []
```
>[!info]
>雖然有進行改進，但使用ARG其實我們可以進入到 image 中，使用 `history` 的命令來查看構建時的歷史資訊。

範例3:
```dockerfile
FROM gcc:9.4 AS builder

ARG TOKEN

RUN git clone https://$TOKEN@github.com/xiaopeng163/helloworld-c src

WORKDIR /src

RUN gcc --static -o hello hello.c


FROM alpine:3.13.5

COPY --from=builder /src/hello /src/hello

ENTRYPOINT [ "/src/hello" ]

CMD []
```
>[!info]
>我們可以看到這邊使用兩個環境，先進行把帶有敏感訊息的事情做完，在傳遞給另一個環境使用，這樣歷史資料就無法查看了。

範例4
上述不確定因素很大，目前我沒有發現有甚麼指令能查看到token，而以下範例為最好的寫法。
```dockerfile
FROM gcc:9.4

RUN --mount=type=secret,id=my_secret git clone https://$(cat /run/secrets/my_secret)@github.com/xiaopeng163/helloworld-c src

WORKDIR /src

RUN gcc --static -o hello hello.c

ENTRYPOINT [ "/src/hello" ]

CMD []
```

我們使用以下指令傳遞給 dockerfile 進行構建
```shell
docker image build --secret id=my_secret,src=token -t docker-secret:4 -f Dockerfile4 .
```
>[!info]
>這條指令用來建構一個Docker映像檔，並且使用秘密變數來保護敏感信息。
>參數說明:
>- `docker image build`：這是用來建構Docker映像檔的指令。
>- `--secret id=my_secret,src=token`：這個選項用來傳遞秘密變數。`id=my_secret` 定義了秘密變數的ID為 `my_secret`，而 `src=token` 表示秘密變數的來源檔案是 `token`。
>- `-t docker-secret:4`：這個選項用來標籤（tag）生成的映像檔，標籤為 `docker-secret:4`。
>- `-f Dockerfile4`：這個選項用來指定要使用的Dockerfile檔案，這裡指定的是 `Dockerfile4`。
>- `.`：這個點號表示建構上下文（build context），通常是Dockerfile所在的目錄。

