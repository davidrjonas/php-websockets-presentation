# Websockets with Ratchet Presentation

## Outline

1. What are websockets
    1. Limits of http 1.0, 1.1
        1. 1.1 allowed only 2 connections to server
    2. Features:
        1. WebSocket is like TCP protocol of messages instead of bytes, the standardization of Comet techniques.
        1. Full duplex
        1. done over port 80, 443
        1. "Upgraded" from a usual http 1.1 connection
        1. ws and wss schemes
        1. Proxy servers: Sum up http://www.infoq.com/articles/Web-Sockets-Proxy-Servers
    2. Supported browsers
    3. Socket.io
2. What is ratchet
    1. Components
3. How to do something simple
    1. Tail a log
4. How to do something useful
    1. WAMP
    2. Autobahnjs
    3. Theory of operation
    4. Server
        1. nginx: http://nginx.org/en/docs/http/websocket.html
            1. version > 1.3.13
        1. Apache 2.4 mod_proxy_wstunnel: http://httpd.apache.org/docs/2.4/mod/mod_proxy_wstunnel.html
