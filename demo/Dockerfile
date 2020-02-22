FROM alpine as builder

WORKDIR /demo

RUN apk add git

RUN git clone --depth=1 https://github.com/meetecho/janus-gateway.git

FROM nginx:alpine
COPY --from=builder /demo/janus-gateway/html/ /usr/share/nginx/html/

HEALTHCHECK --interval=5m --timeout=3s \
  CMD curl -f http://localhost:80 || exit 1