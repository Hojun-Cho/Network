# 프로토콜 스택에 메세지 송신 의뢰

    resolver를 이용해서 웹서버의 ip을 알았다면 이제 이 ip로 메세제를 보내달라고 os 프로토콜 스택에게 
    의뢰한다.

## 소켓을 이용한 통신

    의뢰한 요청은 socket을 통해 전송된다.
    소켓을 이용한 통신을 간단하게설명하자면  두 컴퓨터 사이에 파이프가 생긴다.
    이 파이프로 데이터를 넣으면 반대편에서 데이터를 받는다.
    여기서 소켓은 이 파이프의 양 끝에 있는 데이터의 출입구 역활을 한다.
    파이프는 만든는 순서가 존재한다
    1.클라이언트에서 소켓을 작성한다.
    2.서버측의 소켓에 파이프를 연결한다
    3.데이터를 송수신한다.
    4.파이프를 분리하고 소켓을 말소한다.

    수도코드를 작성해보자 
    
    ip = gethostbyname("www.google.com");
    
    descriptor = socket(ip,stream);

    connect(descriptor,ip,port); // (a) 
    length = write(descriptor,data,length(data)); //(b) 
    length = read(descriptor,buffer);  

    close(descriptor);  //(c)

    (a) 부분을 살펴보자.ip로 컴퓨터를 식별하는게 아닌가 ?? 왜 포트번호도 보내는거지??
    ip주소는 네트워크에 존재하는 각 컴퓨터를 식별하는 값이다.즉 어떤게 어느 컴퓨터인지만 식별 가능하다.
    컴퓨터안에는 매우 많은 프로그램들이 실행되고 이 프로그램중 어느 프로그램에 connect할지 알려주는게 port이다.
    
    (b)부분에서 실질적으로 http request가 나간다.
    
    실제로 위의 과정들은 설명처럼 단순하지 않다...
    연결 및 종료에도 규칙이 존재한다.(다음에 작성해야지~)

```java

import java.net.*;
import java.io.*;

public class DateServer {
    
    public static void main(String[] args) throws Exception {
        ServerSocket server = new ServerSocket(6013); 
        //now listen for connections 
        while (true) {
            Socket client = server.accept(); 
            PrintWriter pout = new PrintWriter(client.getOutputStream(), true);
            pout.println(new java.util.Date().toString());
            client.close();
        }
    }
}


```
위의 코드에서는 6013포트가 server.accept()로 요청이 되기를 기다린다.
여기서 어디선가 6013 포트에 접속을 시도한다면 DataServer는 Date를 파이프에 흘려 보낼거싱다.  

hojun@DESKTOP-8C1UKSP:~/test_java$ telnet localhost 6013   
Trying 127.0.0.1...    
Connected to localhost.   
Escape character is '^]'.    
Fri Apr 08 20:13:37 KST 2022    
Connection closed by foreign host.  

위에는 내가 local에서  telnet으로 테스트했다.물론 이는 local에서만 작동하는 테스트이지만 어떤식으로
소켓을 만들고 데이터를 전송하는지 알아보는 테스트였다.  

