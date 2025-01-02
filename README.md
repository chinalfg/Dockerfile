FROM alpine

WORKDIR /opt/app

RUN apk add --no-cache nodejs curl tzdata

ENV TIME_ZONE=Asia/Shanghai 

RUN cp /usr/share/zoneinfo/$TIME_ZONE /etc/localtime && echo $TIME_ZONE > /etc/timezone

# RUN apk del tzdata

ADD https://github.com/sub-store-org/Sub-Store/releases/latest/download/sub-store.bundle.js /opt/app/sub-store.bundle.js

ADD https://github.com/sub-store-org/Sub-Store-Front-End/releases/latest/download/dist.zip /opt/app/dist.zip

RUN unzip dist.zip; mv dist frontend; rm dist.zip

ADD https://github.com/xream/http-meta/releases/latest/download/http-meta.bundle.js /opt/app/http-meta.bundle.js

ADD https://github.com/xream/http-meta/releases/latest/download/tpl.yaml /opt/app/http-meta/tpl.yaml

RUN version=$(curl -s -L --connect-timeout 5 --max-time 10 --retry 2 --retry-delay 0 --retry-max-time 20 'https://github.com/MetaCubeX/mihomo/releases/download/Prerelease-Alpha/version.txt') && \
  arch=$(arch | sed s/aarch64/arm64/ | sed s/x86_64/amd64-compatible/) && \
  url="https://github.com/MetaCubeX/mihomo/releases/download/Prerelease-Alpha/mihomo-linux-$arch-$version.gz" && \
  curl -s -L --connect-timeout 5 --max-time 10 --retry 2 --retry-delay 0 --retry-max-time 20 "$url" -o /opt/app/http-meta/http-meta.gz && \
  gunzip /opt/app/http-meta/http
