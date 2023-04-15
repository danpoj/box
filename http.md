# HTTP

- Hypertext Transfer Protocol
- application layer protocol for transmitting data like HTML. sent over TCP. (text, layout description, images, videos, scripts, ...)
- protocol for fetching resouces such as HTML documents
- request <-> response
- designed for communication between web browser <-> web server
- stateless protocol (server does not keep any data between two requests. **But while the core of HTTP itself is stateless, HTTP cookies allow the use of stateful sessions.**)
- `http://` incicates which protocol the browser must use. secure version is `https://`
- there are other protocols such as `mailto:` to open a mail client, `ftp://` to handle a file transfer. `data:` `file:` `ssh:` `tel:` `urn:` `view-source:` ...

<hr/>

- `www.example.com`: domain name or authority that governs the namespace.
- `:80 .. :3000 .. : 3095 .. :443`: port. it indicates the gate to access the resources on the web server. (usually omitted if the web server uses the standard ports or HTTP protocol. `80 for HTTP and 443 for HTTPS`) otherwise its mandatory.
- `?key1=vale1&key2=value2`: Query
- `#somewhere`: Fragment, anchor to another part of the resource itself. never sent to the server with the request.

<hr/>

- Increasingly, browsers are removing support for using FTP to load subresources, for security reasons.
- HTTP requests의 주체는 보통 web browser이다. 다른 주체로는 크롤링을 위한 robot, proxy서버, 서버 그자체 일 수도 있다.

## Proxy server

- intermediate program or computer used when navigation through diffrent networks of the Internet.
- interceps requests and serves back responses.
- 캐싱, 필터링, 로드밸런싱, 인증, 로깅, 보안, 우회등 다영햔 역할을 한다. 나중에 공부하자...
