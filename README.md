# Websockets with Ratchet Presentation

This file is a mess. It's just my notes.

http://www.html5rocks.com/en/tutorials/websockets/basics/

RFC6455

## Outline

1. What are websockets
    1. Created to standardize the hacks built on http to achieve low-latency communication.
        1. Why not the hacks? No time to talk about that. 
    2. Features:
        1. WebSocket is like TCP protocol of messages instead of bytes, the standardization of Comet techniques.
        1. "the intent of WebSockets is to provide a relatively simple protocol that can coexist with HTTP and deployed HTTP infrastructure "
        1. The protocol is intended to be extensible; future versions will likely introduce additional concepts such as multiplexing.
        1. Full duplex, frame based not stream. Means the client and server can both send data at any time.
        1. Distinction between binary and unicode.
        1. done over port 80, 443 - or any port. but stick to 80/443 for best compatibility
        1. "Upgraded" from a usual http 1.1 connection
        1. ws and wss schemes
        1. Proxy servers: Sum up http://www.infoq.com/articles/Web-Sockets-Proxy-Servers
    1. Supported browsers
    1. What does it look like?
        1. Request with upgraded: Picture: computer on left service on right. 
            GET /chat HTTP/1.1
            Host: server.example.com
            Upgrade: websocket
            Connection: Upgrade
            Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
            Origin: http://example.com
            Sec-WebSocket-Protocol: chat, superchat
            Sec-WebSocket-Version: 13
            
            HTTP/1.1 101 Switching Protocols
            Upgrade: websocket
            Connection: Upgrade
            Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
            Sec-WebSocket-Protocol: chat
             # sha1 hash of Sec-WebSocket-Key nonce and special guid - to show we agree on ws.
            <frames now pass>

        1. Frames.
            1. There are control frames: close, ping, pong. pong frame may be used as a heartbeat.
            1. There are text and binary "data" frames.
            1. Client frames are masked, server frames are not. This is to avoid proxies misinterpretting the data. New each frame.
        3. But you don't need to know that: JS Libraries: Socket.io, sockjs, autobahnjs
            <script>
            var sock = new SockJS('http://mydomain.com/my_prefix');
            sock.onopen = function() {
                console.log('open');
            };
            sock.onmessage = function(e) {
                console.log('message', e.data);
            };
            sock.onclose = function() {
                console.log('close');
            };
            </script>

            <?php
            use Ratchet\MessageComponentInterface;
            use Ratchet\ConnectionInterface;

            class Chat implements MessageComponentInterface {
                public function onOpen(ConnectionInterface $conn) {
                }

                public function onMessage(ConnectionInterface $from, $msg) {
                }

                public function onClose(ConnectionInterface $conn) {
                }

                public function onError(ConnectionInterface $conn, \Exception $e) {
                }
            }
                
    1. When to use it
        Use Cases:
            Multiplayer Games / Interactive experiments
            Presence
            Chat
            Realtime Data Tickers / charts
            User interactions (lock this resource, it's being edited)
    1. But PHP just isn't like that!
        1. Neither is Heroku and other auto-scaling cloud systems. Hello, Redis? Is is time for PubSub. (I have no idea what i'm doing)

2. What is Ratchet
    1. What is react? Event-driven, non-blocking I/O with PHP, like EventMachine (Ruby), Twisted (Python) and Node.js (V8)
    1. Components

3. How to do something simple
    1. Tail a log

4. Security
    1. Suseptible to XSS
    1. Validate client input as normal (untrusted)
    1. Validate server responses as if dangerous: Send data not code and JSON.parse().
    1. Connections can easily be initiated non-browsers.
        1. Auth on a page doesnt mean websocket is authed
        1. You cannot customize the headers from JS, so use cookies and Authorization: headers sent with the http request
        1. Common pattern is to bake a token system into the protocol: request token from server via http, include token in first message.
        1. CORS is baked in, but that just protects the client. Be careful on the server.
    1. fields starting with |Sec-| cannot be set by an
   attacker from a web browser using only HTML and JavaScript APIs such
   as XMLHttpRequest

5. Problems
    1. Testing
    1. Deployment
        1. Long running, not stateless like traditional php
        1. And We are not sysadmins (I can count to potato)
        1. When code changes server needs be restarted
        1. Solutions:
            1. restart the socket server and lose connections (?) via capistrano, rocketeer
            1. Make websocket server thin state store. php-wsthinstate
    
4. How to do something useful
    1. WAMP
    2. Autobahnjs
    3. Theory of operation
    4. Server
        1. Environment: libevent, ulimit, -xdebug
        1. Be on port 80, 443 for best compatibility
            1. Run on subdomain: ws.example.com
            1. Reverse proxy (HAProxy or Varnish)
        1. nginx: http://nginx.org/en/docs/http/websocket.html
            1. version > 1.3.13
        1. Apache 2.4 mod_proxy_wstunnel: http://httpd.apache.org/docs/2.4/mod/mod_proxy_wstunnel.html
