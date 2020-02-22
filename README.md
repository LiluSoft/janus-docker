# janus-docker

This is a test composition for janus-admin project.

It starts [Janus WebRTC Server](https://github.com/meetecho/janus-gateway), Janus HTML test site, [Mosquitto MQTT](https://mosquitto.org/) and [RabbitMQ](https://www.rabbitmq.com/) for the integration tests.

I've used the [checkinstall](https://asic-linux.com.mx/~izto/checkinstall/) utility to shrink the final image as much as possible and the installation follows the build procedures as closely as possible.

```
Compiler:                  gcc
libsrtp version:           2.x
SSL/crypto library:        BoringSSL
DTLS set-timeout:          yes
Mutex implementation:      GMutex (native futex on Linux)
DataChannels support:      yes
Recordings post-processor: no
TURN REST API client:      yes
Doxygen documentation:     no
Transports:
    REST (HTTP/HTTPS):     yes
    WebSockets:            yes
    RabbitMQ:              yes
    MQTT:                  yes
    Unix Sockets:          yes
    Nanomsg:               yes
Plugins:
    Echo Test:             yes
    Streaming:             yes
    Video Call:            yes
    SIP Gateway:           yes
    NoSIP (RTP Bridge):    yes
    Audio Bridge:          yes
    Video Room:            yes
    Voice Mail:            yes
    Record&Play:           yes
    Text Room:             yes
    Lua Interpreter:       no
    Duktape Interpreter:   no
Event handlers:
    Sample event handler:  yes
    WebSocket ev. handler: yes
    RabbitMQ event handler:yes
    MQTT event handler:    yes
    Nanomsg event handler: yes
External loggers:
    JSON file logger:      no
JavaScript modules:        no
```

# Getting Started

```
git clone 
docker-compose up
```

# Thank you

Special thanks to [Brendan Jocson](https://github.com/krull/docker-janus) for opening his Dockerfile, I've learned from it.

