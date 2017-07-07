# 네트워크 시험문제
### 객관식(17문제)
##### 1, 2. 소켓프로그래밍의 서버 클라이언트 구조(그림) 2문제
![클라이언트의 서버와 연결 구조](https://raw.githubusercontent.com/KGSH/KGSH12_Network_2/fc0a81fe5632a72c85559c8909226a146d33c549/2.PNG)
##### 3. 백로그 크기
* int listen(int sockfd, int backlog);
* UNIX, LINUX  최대 8 / WINDOW 최대 5
>연결 요청 대기 상태에서 클리이언트의 연결 요청을 받아들이는 큐의 크기를 나타냄 인자로 5가 들어오면 큐의 크기가 5가 되어 클라이언트의 연결 요청을 5개까지 대기시킬 수 있다.

##### 4. 네트워크 정수 저장 함수
* htonl() : (Host to network long )

      호스트 -> 네트워크 long 형 데이터의 바이트 오더를 바꾸어주는 함수

* ntohl() : (Network to host long)

      네트워크 -> 호스트 long 형 데이터의 바이트 오더를 바꾸어주는 함수

##### 5. 소켓의 접속 종료 규칙
* TCP 소켓은 1:1스트림 연결이기 때문에 상대편이 소켓 연결을 종료하면 이쪽도 같이 종료해야 한다. 자동으로 닫히지 않는다.

* recv()를 호출했을 때 0이 리턴되거나, send를 호출했을 때 -1로 에러가 리턴하면서 errno가 EPIPE이면 상대편(peer) 연결이 종료된 것이다.

* close()로 접속 종료

##### 6. 소켓의 기본 인터페이스 함수
* 소켓 생성 : socket()
* 접속 수락 : connect() -> 클라이언트
* 접속 허용 : accept() -> 서버
* 자료 송신 : send()
* 자료 수신 : recv()
* 접속 종료 : close()

##### 7. TCP 소켓의 형태와 프로토콜
* TCP 프로토콜 : SOCK_STREAM
* UDP 프로토콜 : SOCK_DGRAM
##### 8. 동기 방식 소켓
* CSocket
* (비동기) CAsyncSocket
##### 9. 소켓 초기화 함수
>프로젝트가 생성될 때 자동생성되서 모르는 경우가 많음

* AfxSocketInit : MFC소켓 사용위해 초기화
##### 10. 이벤트 처리 함수
* OnReceive() : 자료를 수신했을 때
##### 11. 소켓의 특징
##### TCP(Transmission Control Protocol)
 >연결형 서비스를 지원하는 전송계층 프로토콜 인터넷 환경에서 기본으로 사용한다. 호스트간 신뢰성 있는 데이터 전달과 흐름제어 및 혼잡제어 등을 제공하는 전송계층

* 특징
>가상 회선 연결 방식, 연결형 서비스를 제공
높은 신뢰성(Sequence Number, Ack Number를 통한 신뢰성 보장)
>연결의 설정(3-way handshaking)과 해제(4-way handshaking)
데이터 흐름 제어(수신자 버퍼 오버플로우 방지) 및 혼잡 제어(네트워크 내 패킷 수가 과도하게 증가하는 현상 방지)
전이중(Full-Duplex), 점대점(Point to Point) 서비스

* 소켓 통신 과정
>서버 : 소켓을 생성, 주소 할당, 연결 요청 기다림, 요청에 대한 응답
클라이언트 : 소켓을 생성, 주소 할당, 연결 요청

##### UDP(User Datagram Protocol)

>비연결형 서비스를 지원하는 전송계층 프로토콜 사용자 데이터그램형 프로토콜 인터넷상에서 서로 정보를 주고받을 때 정보를 보낸다는 신호나 받는다는 신호 절차를 거치지 않고, 보내는 쪽에서 일방적으로 데이터를 전달하는 통신 프로토콜 보내는 쪽에서는 받는 쪽이 데이터를 받았는지 받지 않았는지 확인할 수 없고, 또 확인할 필요도 없도록 만들어진 프로토콜

* 특징

>비연결형(port만 확인하여 소켓을 식별하고 송수신)
패킷 오버헤드가 적어 네트워크 부하 감소
비신뢰성
오류검출(헤더에 오류 검출 필드를 포함하여 무결성 검사)
TCP의 handshaking 같은 연결 설정이 없다
DNS, NFS, SNMP, RIP 등 사용

* 소켓 통신 과정
>서버 : 소켓을 생성, 주소 할당, 데이터를 송수신
클라이언트 : 소켓 생성 후 데이터 수신

##### 12. 소켓 프로그램 코드 자료 수신 부분
( 120p )
<pre><code>// OnReceive 함수 재정의
void CMySocket::OnReceive(int nErrorCode)
{
  if(nErrorCode) return;
  if(m_ReceivePtr > m_NextReceive) {

        // 1. 중간 버퍼를 사용하고 있는 경우
        if(m_ReceivePtr - m_NextReceive <= RECEIVESIZE + 2)
          return;
        // 빈 공간이 없음
        }

        else if(m_NextReceive <= RECEIVEBUFSIZE) {
          // 2. 뒤쪽의 공간을 사용할 수 있는 경우
        }

        else if(m_ReceivePtr >= RECEIVESIZE + 2) {
          // 3. 앞쪽의 공간을 사용할 수 있는 경우
          *(short *)(m_ReceiveBuf + m_NextReceive) = -1;
          m_NextReceive = 0;
        }
        else
          return; // 빈 공간이 없음

        int n;
        n = CAsyncSocket::Receive(m_ReceiveBuf + m_NextReceive + 2, RECEIVESIZE);

        if (n < 0) return; // 소켓 오류
        if (n == 0) { // 접속 끊김
          OnClose(0);
          return;
        }

        *(short *)(m_ReceiveBuf + m_NextReceive) = n;
        m_NextReceive += n + 2;

        // 응용 프로그램에 자료를 수신했음을 알림
        if(m_hWnd) ::SendMessage(m_hWnd, m_Message,
                                 MYSOCK_RECEIVE, (LPARAM)this);
        else if(m_CallBackFunc)(\*m_CallBackFunc)
                               (MYSOCK_RECEIVE, this);
  }</code></pre>

##### 13, 14. UDP 서버 코드 일부분
    ReceiveFrom(buf, 512, addr, port);
    ...
    SendTo(str.GetBuffer(), str.GetLength() + 1, port, addr);

##### 15. UDP 프로토콜 특징
* 신뢰성은 보장되지 않더라도 빠른 전송을 요하는 소켓 응용 프로그램에 적합하다.
* 소켓을 생성한 후 접속 과정 없이 소켓을 바로 사용한다.
* 자료를 송신하는 함수는 sendto()함수이다. 이 함수에서는 자료를 수신하는 호스트의 주소와 포트를 직접 지정한다.
* UDP 소켓 프로그램에서 자료를 수신하는 함수는 recvfrom() 함수이다. 이 함수는 자료를 송신한 호스트의 주소와 포트를 얻어올 수 있다.
### 주관식(8문제)
##### 1, 2. *보너스 문제*
##### 3. 클라이언트와 서버 간의 연결 통로
* 세션 소켓 ( 84p )
또는
* 세션 ( 89p )
##### 4. PF와 AF의 차이점
* PF_INET은 프로토콜 체계(프로토콜 패밀리)중 하나.
* AF_INET은 주소 체계(주소 패밀리)중 하나.
##### 5. 소켓 함수 이름
##### 6. UNIX 시스템과 WINDOW시스템에 있는 같은 함수지만 매개변수가 다른 함수의 매개변수(함수를 완성시켜라)
* int listen(int sockfd, int backlog);
* 매개변수 : backlog ( UNIX, LINUX  최대 8 /  WINDOW 최대 5 )

( 88p )
##### 7. 소켓 프로그래밍을 할때 상속받는 클래스 이름
* CSocket
##### 8. *보너스 문제* 1차고사에서 다른 과목에서 주관식 답이었던 것
