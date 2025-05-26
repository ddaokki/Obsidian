# Principles of Reliable Data Transfer

## 1. Reliable Service Abstraction
- **Sending process**와 **receiving process** 간의 데이터 전송이 신뢰성 있게 이루어지는 추상적 모델
- 중간에 **reliable channel**을 통해 데이터가 손실 없이 전송된다고 가정함
- 전송 계층(transport layer)은 이러한 **reliable service abstraction**을 상위 계층(application layer)에 제공함
    

---

## 2. Reliable Service Implementation
- 실제 네트워크는 **unreliable channel**을 사용함
- **신뢰 가능한 데이터 전송 프로토콜**을 송신 측과 수신 측에 각각 구현하여 신뢰성 보장
    

### 송신 측
- 송신 애플리케이션으로부터 데이터를 수신하여 전송 준비
- **Sender-side of reliable data transfer protocol**이 데이터를 처리하여 전송함
    

### 수신 측
- 네트워크로부터 데이터를 수신
- **Receiver-side of reliable data transfer protocol**이 데이터를 처리하여 애플리케이션 계층으로 전달함
- 이러한 구조는 **reliable service의 실제 구현**을 나타냄
    

---

## 3. Protocol Complexity and Channel Characteristics
- 신뢰성 있는 데이터 전송 프로토콜의 복잡도는 사용되는 채널의 특성에 **강하게 의존**함

### 예시 특성
- 데이터 손실 (lose)
- 데이터 손상 (corrupt)
- 데이터 순서 변경 (reorder data)
- 이러한 특성들이 존재할수록 신뢰성 있는 전송 프로토콜은 더욱 복잡한 메커니즘을 필요로 함
    
> → 따라서, 신뢰성 있는 데이터 전송을 구현하기 위해서는 **송신 측과 수신 측 모두에 프로토콜이 필요**하며, 채널의 특성에 따라 프로토콜 설계가 달라짐

## 4. Sender and Receiver State Unawareness
- 송신자와 수신자는 서로의 상태(state)를 모름
- 예: 수신자가 메시지를 받았는지 송신자는 알 수 없음
- 단, 메시지를 통해 상태 정보를 전달받은 경우는 예외임 (unless communicated via a message)
    

## 5. rdt 인터페이스 구조
- **rdt_send()**: 상위 계층(app 등)에서 호출되어 데이터를 수신자 쪽으로 전달
- **udt_send()**: rdt가 호출하며, 신뢰성 없는 채널을 통해 패킷을 전송함
- **rdt_rcv()**: 수신 측에서 패킷이 도착했을 때 호출됨
- **deliver_data()**: 수신 측에서 데이터를 상위 계층으로 전달할 때 호출됨
- 송수신 양방향 통신은 unreliable channel 위에서 수행됨
    

## 6. rdt 설계 개요
- 송신 측과 수신 측의 reliable data transfer protocol(rdt)을 점진적으로 개발할 것
- 단방향 데이터 전송만 고려하되, 제어 정보는 양방향으로 흐름
- 송신자와 수신자의 동작을 **유한 상태 기계(Finite State Machines, FSM)**를 통해 명시함
- 상태(state): 특정 상태에서 다음 상태는 발생 이벤트에 따라 결정됨
- 이벤트 발생 시 상태 전이와 그에 따른 동작(actions)이 수행됨

## 7. rdt1.0: reliable transfer over a reliable channel
- underlying channel은 완전히 신뢰 가능함
    - 비트 오류 없음 (no bit errors)
    - 패킷 손실 없음 (no loss of packets)
- 송신자와 수신자 각각에 대해 별도의 FSM 사용
    - 송신자: 상위 계층으로부터 데이터 수신 → 패킷 생성 → 전송
    - 수신자: 패킷 수신 → 데이터 추출 및 상위 계층으로 전달
        

## 8. rdt2.0: channel with bit errors
- underlying channel이 패킷의 비트를 변형시킬 수 있음 (bit errors 가능)
    - 체크섬(checksum, 예: 인터넷 체크섬)을 통해 오류 탐지
- 핵심 질문: 오류로부터 어떻게 회복할 것인가?
    - **ACK**(Acknowledgement): 수신자가 패킷을 제대로 받았음을 명시적으로 송신자에게 알림
    - **NAK**(Negative Acknowledgement): 수신자가 패킷에 오류가 있었음을 명시적으로 알림
    - **Retransmission**: 송신자는 NAK를 받으면 해당 패킷을 재전송함
- **Stop and Wait** 방식 적용: 한 번에 하나의 패킷만 보내고, 수신자의 응답을 기다림
## 9. rdt2.0: FSM 동작 원리 (송신자)
- 송신자는 두 가지 상태 중 하나에 있음:
    - 상위 계층으로부터 데이터 수신 대기 (Wait for call from above)
    - 수신자로부터 ACK 또는 NAK 수신 대기 (Wait for ACK or NAK)
- 상태 전이 조건:
    - **rdt_send(data)** 호출 시: 패킷 생성 후 전송 상태로 이동
    - **rdt_rcv(rcvpkt) && isNAK(rcvpkt)**: 패킷 재전송
    - **rdt_rcv(rcvpkt) && isACK(rcvpkt)**: 초기 상태로 복귀
        

## 10. 수신자의 상태 비가시성 문제
- 송신자는 수신자의 상태(정확히 수신했는지)를 알 수 없음
- 이 상태는 오직 메시지를 통해서만 전달될 수 있음
- 이러한 이유로 **신뢰성 있는 데이터 전송 프로토콜이 필요**함
    

## 11. rdt2.0: 오류 없는 동작 시의 전체 FSM 흐름
### 송신자 측
- **rdt_send(data)** → 패킷 생성 및 전송
- **rdt_rcv(rcvpkt)** 이후:
    - isACK이면 다음 데이터 수신 대기 상태로 전이
    - isNAK이면 동일 패킷 재전송
        
### 수신자 측
- **rdt_rcv(rcvpkt)** 호출 시:
    - 패킷이 손상되었으면 **NAK 전송**
    - 패킷이 올바르면:
        - 데이터 추출 및 상위 계층에 전달
        - ACK 전송

## 12. rdt2.0: 손상된 패킷 처리 시나리오
- 수신자가 손상된 패킷을 받으면:
    - 오류를 탐지하고 NAK 전송
    - 송신자는 NAK 수신 시 동일 패킷 재전송
- 수신자가 정상 패킷을 받으면:
    - 데이터 추출 및 상위 계층 전달 후 ACK 전송
        

## 13. rdt2.0의 치명적 결함
### 문제: ACK/NAK가 손상되면?
- 송신자는 수신자의 상태를 모르게 됨 → 무엇이 일어났는지 알 수 없음
- 무작정 재전송하면 중복 발생 가능
### 중복 처리 방법
- 송신자는 패킷에 **시퀀스 번호(sequence number)** 추가
- 수신자는 중복 여부 판단하여 이미 처리된 패킷은 폐기함
- 이 방식은 여전히 **stop-and-wait** 기반: 송신자는 응답을 기다릴 때까지 다음 패킷 전송 안함
    

## 14. rdt2.1: 송신자 FSM (ACK/NAK 손상 대응)
- 각 전송에 대해 시퀀스 번호(0 또는 1)를 부여하여 구분
- 상태는 이전에 보낸 패킷이 0인지 1인지 기억해야 함
- ACK/NAK 또는 손상된 응답에 대해 반복 전송 수행
- 전이 조건에 따라 0 또는 1 시퀀스 상태에서 다음 단계로 전환
## 15. rdt2.1: 수신자 FSM (ACK/NAK 손상 대응)
- 수신자는 수신 패킷의 시퀀스 번호를 기반으로 중복 여부 판단
- 유효하고 새로운 패킷이면:
    - 데이터 전달 후 ACK 생성 및 전송
- 중복이거나 손상된 경우:
    - 기존 ACK 재전송 또는 NAK 전송으로 대응

## 16. rdt2.1: 정리
### 송신자
- 패킷에 시퀀스 번호 추가 (0 또는 1이면 충분)
- 수신한 ACK/NAK의 손상 여부 확인 필요
- 상태가 시퀀스 번호를 기억해야 하므로 FSM 상태 수가 증가함

### 수신자
- 수신 패킷이 중복인지 검사해야 함
    - 상태에 따라 기대하는 시퀀스 번호가 다름
- 마지막으로 보낸 ACK/NAK가 송신자에게 전달되었는지는 수신자가 알 수 없음

## 17. rdt2.2: NAK 없는 프로토콜
- rdt2.1과 동일한 기능을 ACK만을 이용해 구현
- 수신자는 NAK 대신 마지막으로 **정상 수신한 패킷에 대한 ACK**를 다시 보냄
    - 이때, **ACK에는 반드시 해당되는 시퀀스 번호를 명시해야 함**
- 송신자는 중복된 ACK을 수신하면 NAK와 동일하게 처리함: 현재 패킷 재전송
- TCP도 이 방식을 사용하여 NAK 없이 신뢰성을 확보함
    

## 18. rdt2.2: 송신자와 수신자 FSM 단편

### 송신자 FSM fragment

- 손상되었거나 시퀀스 번호가 기대와 다르면 현재 패킷 재전송
    
- 기대한 ACK(예: isACK(rcvpkt, 1))이 도착해야 다음 상태로 진행
    

### 수신자 FSM fragment

- 정상 수신 및 시퀀스 번호 일치 시: 데이터 전달 후 해당 번호의 ACK 생성 및 전송
    
- 중복되거나 손상된 패킷 수신 시: 이전 ACK 반복 전송
    

## 19. rdt3.0: 오류와 손실이 있는 채널

- 새로운 전제: underlying channel이 **패킷 또는 ACK를 잃을 수 있음**
    
- 체크섬, 시퀀스 번호, ACK, 재전송만으로는 부족할 수 있음
    
- 질문: 인간은 대화 중 단어를 놓쳤을 때 어떻게 반응하는가?
    

## 20. rdt3.0: 해결 접근법 (timeout 기반)

- 송신자는 ACK 수신을 일정 시간 기다림
    
- 시간 내 ACK를 못 받으면 재전송 수행
    
- 단순 지연일 경우: 시퀀스 번호 덕분에 중복으로 처리되어도 문제 없음
    
    - 수신자는 ACK에 해당 패킷의 시퀀스 번호를 명시해야 함
        
- **타이머를 사용하여 합리적인 시간 후 인터럽트 발생** (timeout)