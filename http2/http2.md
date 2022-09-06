### HTTP 1.1 

* Connection당 하나의 요청을 처리 하도록 설계 되어있다.
* 그래서 동시전송이 불가능하고 요청,응답이 순차적으로 진행된다.
* 이것이 문제가된다. HTTP안에 포함된 다수의 리소스를 처리하려면 요청한 리소스 개수에 비례해서 Latency가 길어진다
    Latency가 길어지면 client들은 😫😫😫 
    
### HTTP HOL Blocking

* HTTP 1.1 에서 connection당 하나의 요청을 처리하는 게 아닌 pipelining을 이용한 방법이 존재한다
* 이는 하나의 Connection을 이용해 다수의 파일을 요청 응답할 수 있는 기법이다.
* 이 방식에는 문제가 존재한다.
```shell
|             a.jpg             |                                     
                                |   b.js    |
                                            | c.html |
```
* 첫번째 응답이 처리되기 전까지 다른 자료를 받을 수 없다. 따라서 더 빨리 받을 수 있는 자료도 순서에 따라 더 기다려야 하는 상황이 발생한다
* 이러한 문제가 HOL 문제이다 

### Header

* http 의 header에는 요청시 마다 중복된 헤더값을 전송하는 경우가 많다(cookie).
* 따라서 동일한 정보를 매번 다시 요청,응답 해야 하는 문제가 발생한다.


### 문제 해결 

* Domain Sharding
    * http/1.1의 단점인 단일 connection 형성을 극복하기 위해 다수의 도메인에 connection을 생성해서 병렬로 요청을 보내기도 한다.
    * 하지만 브라우저 별로 Domain당 connection 개수의 제한이 존재한다.(Chrome의 경우 최대 6개)

* [SPDY](https://ko.wikipedia.org/wiki/SPDY) : http를 통한 전송을 재정의한다.
> SPDY는 웹 페이지 부하 레이턴시를 줄이고 웹 보안을 개선하는 목표 면에서 HTTP와 비슷하다. SPDY는 압축, 다중화, 우선 순위 설정을 통한 레이턴시 감소를 달성한다.[1] "SPDY"는 구글의 상표이며 두문자어는 아니다.
  * 구글은 HTTP 2.0 프로토콜의 이점이 더 크기 때문에 크롬 브라우저에서 SPDY 지원을 제거

## [HTTP/2](https://ko.wikipedia.org/wiki/HTTP/2)  
  * HTTP 프로토콜의 두 번째 버전이다. SPDY에 기반한다
  * 목표
    * 클라이언트와 서버가 HTTP 1.1, 2.0 혹은 다른 비 HTTP 프로토콜 사용을 협상할 수 있는 메커니즘 구현
    * HTTP 1.1과 호환성 유지
    * 다음과 같은 방법들을 이용하여 Latency를 감소시켜 웹 브라우저의 페이지 로드 속도 개선
      * HTTP 헤더 데이터 압축 
      * 서버 푸시 기술
      * 요청을 HTTP 파이프라인으로 처리
      * HTTP 1.x의 HOL blocking 문제 해결 => 파이프 라이닝을 사용하면 발생하던 문제를 해결 
      * TCP 연결 하나로 여러 요청을 다중화 처리 => 매 요청마다 connection을 형성하는 문제를 해결 
      * 데스크탑 브라우저, 모바일 웹 브라우저, 웹 API, 웹 서버, 프록시 서버, 리버스 프록시 서버, 방화벽, 콘텐츠 전송 네트워크 등 자주 쓰이는 것들을 지원

### Multiplexed Streams
  하나의 커넥션으로 여러개의 데이터를 송수신할 수 있는 stream 기반의 방식
  각각의 데이터에 식별자를 두어서 순서에 종속되지 않게 데이터를 전송해서 HOL 문제를 해결했다.
### Server Push
  서버는 클라이언트의 요청에 대해 요청하지 않은 리소스를 보내줄 수 있다.
  미리 리소스를 전송해서 클라이언트의 요청을 최소화함으로서 성능 최적화를 달성한다
  단 웹 브라우저가 연결 주기 동안 푸시된 리소스를 캐싱하기 때문에 무작정 푸시하면 비효율적이게된다
### Header Compression
  Header 정보를 압축하기 위해 HeaderTable과 Huffman Encoding 기법을 이용하여 데이터를 처리한다.
  만약 Header에 중복값이 존재하는 경우 Static/Dynamic HeaderTable 를 사용하여 중복을 검출하고 중복된 Header는 index만 전송하고
  중복되지 않은 Header는 허프만 인코딩으로 전송한다
### Stream Prioritization
  클라이언트가 요청한 자료에 우선순위를 설정하여 더 우선순위가 높은 자료를 먼저 전송하도록 요청할 수 있다.
  
 
