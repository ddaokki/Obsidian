## Chapter 4: done!

### 요약: Network Layer의 주요 구성 요소
- **Network layer: overview**  
  네트워크 계층의 역할과 주요 기능 (주소 지정, 경로 설정, 포워딩 등)을 개괄적으로 다룸

- **What’s inside a router**  
  라우터 내부 구성: input port, switching fabric, output port, control plane 등

- **IP: the Internet Protocol**  
  IP 데이터그램 형식, 주소 구조, 라우팅 등 IP 관련 핵심 개념 설명

- **Generalized Forwarding, SDN**  
  전통적 포워딩(목적지 기반)에서 확장된 방식으로, 다양한 필드 기반의 처리(log/drop/forward 등)와 SDN의 개념 소개

- **Middleboxes**  
  NAT, 방화벽, 로드밸런서 등 전통적 라우터의 기능을 넘는 네트워크 장비에 대한 개념

### 질문 및 다음 장 예고
- **Question**: Forwarding Table (또는 Flow Table)은 어떻게 계산되는가?
- **Answer**: Control Plane에서 계산됨 (다음 장에서 설명)

## IP fragmentation/reassembly

MTU(Maximum Transmission Unit)는 네트워크 링크에서 전송 가능한 최대 프레임 크기를 의미함. 네트워크마다 MTU 크기가 다르며, 하나의 큰 IP 데이터그램이 MTU보다 클 경우 조각(fragment)으로 나누어져 전송됨.

- **Fragmentation**: 하나의 큰 IP 데이터그램이 여러 개의 작은 데이터그램으로 나뉘어 네트워크를 통해 전송됨. 
- **Reassembly**: 분할된 데이터그램은 목적지 호스트에서만 재조립됨.
- **IP 헤더의 필드**: fragment offset, ID, flags 등을 이용하여 어떤 조각이 어디에 위치하는지를 파악함.

예시:
- 4000바이트의 데이터그램이 있고 MTU가 1500바이트일 경우:
  - IP 헤더가 20바이트이므로 한 데이터그램에 담을 수 있는 데이터는 1480바이트임
  - 총 3개 조각: 1480, 1480, 1040바이트
  - offset은 8바이트 단위로 지정됨 → 각각 offset: 0, 185, 370
  - 마지막 조각만 `MF`(More Fragment) 플래그를 0으로 설정함

## DHCP: Wireshark output (home LAN)

DHCP(Dynamic Host Configuration Protocol)는 IP 주소를 포함한 네트워크 설정을 자동으로 할당해주는 프로토콜임. 클라이언트가 DHCP 서버에 요청을 보내고, 서버가 응답을 통해 설정을 제공함.

- **Boot Request (1)**: 클라이언트가 IP 주소 요청을 위해 보내는 메시지
- **Boot Reply (2)**: 서버가 IP 주소 및 기타 설정을 클라이언트에 전달하는 메시지

요소 해석:
- `Client MAC address`: 요청자의 물리 주소
- `Transaction ID`: 요청과 응답을 매칭하기 위한 식별자
- `Requested IP address`: 클라이언트가 원하는 주소 (DHCP Request 단계에서 포함됨)
- `DHCP Message Type`: 요청(DHCP Request), 응답(DHCP ACK) 등 명시
- `Subnet Mask`, `Router`, `DNS`, `Domain Name` 등 다양한 설정이 포함됨

실제 예시:
- 클라이언트 MAC 주소: `23:68:8a:0d:16:63:23`
- 서버가 제공한 IP 주소: `192.168.1.101`
- 기본 게이트웨이(Router): `192.168.1.1`
- 서브넷 마스크: `255.255.255.0`
- DNS: `68.87.74.226`, `68.87.64.146`
- Domain Name: `hsd1.ma.comcast.net`

이러한 과정을 통해 클라이언트는 네트워크 설정 없이도 자동으로 통신할 수 있게 됨.
