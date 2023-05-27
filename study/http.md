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

## TCP / IP / UDP / 공인 IP / 사설 IP

- 정보를 전송 및 수신하기 위한 규칙
- 모든 데이터의 교환은 2대의 기기 사이에서만 가능하다.
- UDP는 TCP보다 단순하며 다른 데이터에 비해 안전하게 보호되어야 할 필요가 없는 실시간 응용 프로그램에서 흔히 사용된다. 단순하기때문에 신뢰도가 낮지만 빠른 정보 교환이 가능하다.
- 공인 IP 주소는 인터넷이 사용자를 찾을 수 있도록 사용자를 식별하는 역할을 한다. 인터넷 통신 목적으로 사용된다.
- 사설 IP 주소는 사설 네트워크에서 다른 장치와 안전하게 연결하기 위해 사용되며, 동일한 네트워크의 각 장치에는 고유한 사설 IP 주소가 할당된다. 사설 IP는 각 사설 네트워크에서만 사용되기에 여러 네트워크에서 IP 주소가 중복되더라도 문제가 없다. 로컬 네트워크 통신 목적으로 사용된다. (10... 172... 192.168...)
- 장치수에 비해 IP 주소가 충분하지 않기때문에 인터넷에 연결된 장치에는 공인 IP와 사설 IP 가 모두 할당된다.

## 주소창에 ~~가 입력됐을 때

- 사람이 기억하기 쉬운 google.com과 같은 도메인을 검색한다.
- 도메인에 맵핑된 서버의 ip 주소를 찾기 위해 dns가 동작한다. domain name server에 데이터베이스로 관리된다.
- 이전 방문에 의한 캐시가 남아있는지 확인한다. 브라우저캐시, OS캐시, 라우터캐시, ISP캐시 등을 차례대호 확인한다.
- 캐시가 없다면 ip 주소를 DNS 서버로부터 가져온다.
- 리소스를 가져올 서버 ip 주소를 알게됐으면, 브라우저와 서버는 TCP 연결을 맺는다. http 프로토콜은 기본적으로 tcp connection이며 리소스가 이동하기 전에 브라우저와 서버 사이의 다리를 놓는 3-way-handshake 과정을 거친다.
- 서버에 GET Request를 보낸다.
- 리소스들을 response로 받아와 파싱하고 DOM, CSSOM 등과 같은 구조를 만들며 렌더링한다. [자세한 동작 구조 - naver D2](https://d2.naver.com/helloworld/59361)

## Reference

- [mdn web docs / http](https://developer.mozilla.org/ko/docs/Web/HTTP)
