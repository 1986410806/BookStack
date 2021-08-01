FROM golang:1.13-alpine as builder

ENV GOPROXY=https://goproxy.cn,direct

WORKDIR /builder

ADD . .

RUN go mod tidy

RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -v -o dist/app && cp -r conf dist/

FROM ubuntu:16.04

MAINTAINER "TruthHun <TruthHun@QQ.COM>"

RUN sed -i s@/archive.ubuntu.com/@/mirrors.aliyun.com/@g /etc/apt/sources.list \
    && apt clean \
    && apt update -y

# 安装依赖
RUN apt instal apt install -y xdg-utils wget xz-utils python chromium-browser \
    && wget -nv -O- https://download.calibre-ebook.com/linux-installer.sh | sh /dev/stdin

ENV LANG en_US.utf8
ENV chrome=chromium-browser

WORKDIR /app
# 将程序拷贝进去
COPY --from=builder /builder/dist .
COPY --from=builder /builder/static static
COPY --from=builder /builder/views views

# 将程序拷贝进去
#COPY lib/time/zoneinfo.zip /usr/local/go/lib/time/

RUN mv conf/app.prod.conf conf/app.conf

CMD [ "./app" ]