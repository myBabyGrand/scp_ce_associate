# Well Architected - Performance Efficiency


## 1. 성능효율성의 개념
 ![](/img/performanceEfficiency001.png)
 - 서버 가상화 : 서버 여러대의 자원을 묶어 필요한 수요에 맞게 자원 할당
 - 가상화 SW로 성능의 일부 손해를 보더라도 서버자원을 가상화 해서 대규모 부하를 감당할 용량의 자원을 가상의 머신에 할당하는 전략
 - 가상화 기반 데이터 센터 -> 클라우드 서비스
 - 물리계층 - ***가상화 계층*** - 응용계층
    * 성능손실, 가시성 제한이 불가피
 - 클라우드 성능효율성 전략은 **부하 증감에 맞춰 전체 성능을 조절** 하는 것을 초점으로 함
 ![](/img/performanceEfficiency002.png)
 
## 2. 성능 개선 영역

### 성능 개선 1 : 지연시간(latency) 단축
 - 각 지역 사용자에게 동일한 경험 제공 <> 모든 지역 사용자에게 동일한 지연 시간 제공
 - 각 지역 마다 인터넷, cellular 통신 속도가 다르기 때문
 - CDN, 글로벌 데이터센터 인프라 : 지연시간 개선에 기여
 - Virtual Server 메모리 용량 검토 
 - 데이터베이스 지연 시간 개선 : Read Replica 적용(SCP MySQL, PostgreSQL) 등
 
### 성능 개선 2: 처리량(Throughput, 지정된 시간에 송/수신되는 데이터양) 개선 
 ![](/img/performanceEfficiency003.png)
 - 네트워크 대역폭
    * 온프레이미스 - 클라우드 연결시 안정적 대역폭 확보
    * 소켓방식 통신이 필요하다면, 양쪽 연결이 대역폭이 안정적이어야 하며, 송수신하는 데이터양에 따른 대역폭이 확보되어야 함 -> Direct Connect 
 - 애플리케이션 설계 시 API (http 기반)등으로 애플리케이션 연결을 고려
 - 스토리지의 디스크 처리량
    * IOPS : 10,000 IOPS 스토리지
    * I/O당 데이터 크기 : 4KB
    * -> 초당 41MB/S 전송
    * SCP는 기본 SSD 제공, 최대 160000 IOPS 제공
 - 가상머신으로 처리량 확보가 어려운 경우
    * Bare Metal Sever : 애플리케이션이 하이퍼바이저를 거치지 않고 물리서버에 직접 접속 할 수 있다.
 
 ### 성능 개선 3 : 캐싱을 적용(아키텍처 설계CDN)
 - 파일을 제공하는 서버의 부하를 줄여줌 : 파일을 
 - 네트워크 경로 측면에서, CDN은 최종 사용자와 서버에 중간에 위치함. CDN-서버간의 거리만큼 지연속도를 줄일 수 있다. 
  - 서버를 전세계 Region에 배포하지 않는다면 CDN이 좋은 대안이 됨
  ![](/img/performanceEfficiency004.png)  

### 성능 개선 3 : 캐싱을 적용(데이터베이스 Redis)
 - 읽기 전용 Redis 도입

### 캐싱
 - 성능 향상을 기대 -> 잘 구현하지 못할 경우 성능 향상은 하지 못하고 비용만 증가할 수 있음
 ![](/img/performanceEfficiency005.png)  

## 3. 성능효율성을 위한 Well-Architected 설계원칙

### 원칙 1. 컴퓨팅 선택 : 서버 타입 선택
![](/img/performanceEfficiency006.png)   

### 원칙 1. 컴퓨팅 선택 : 컨테이너 선택
 ![](/img/performanceEfficiency007.png)   
 - 컨테이너 구현시 검토사항
    * 컨테이너는 애플리케이션 인프라를 추상화
    * 컨테이너 운영 도구 및 운영 역량이 필요함
    * 주로 부분개선이 아닌 빅뱅 형식의 구축에 많이 활용
 - SCP는 Kubernetes 컨테이너 오케스트레이션, DevOps 제공

 - 그밖에
 - **Bare Metal Server**
 ![](/img/performanceEfficiency008.png)   
 - **HPC Cluster**
 ![](/img/performanceEfficiency009.png)   
 - Bare Metal Server와 차이점은 여러서버를 클러스터로 묶고 애플리케이션 작업은 각각의 노드에서 병렬로 처리할 수 있다.
 - **GPU Multi-node**
 ![](/img/performanceEfficiency010.png)   
 
 ### 원칙 2. 스토리지 선택
 ![](/img/performanceEfficiency011.png)   
  - 각 데이터 사용 유형에 따라 유리한 패턴을 선택하면 됨
  - ex) 엑세스 패턴
    * 무작위 : Block가 유리
    * 순차적 : File, Object가 유리
   
### 원칙 3. 데이터베이스 선택 : 사용자 구축형, 관리형
 - 사용자 구축형(직접설치)
 - SCP  관리형
 ![](/img/performanceEfficiency012.png)   
    * PostgreSQL, MySQL은 읽기복제(Read-replica) 제공, 주 DB의 읽기 부하 감소, 성능 향상
    * **현재 기준 EPAS, PostgreSQL, MariaDB, MySQL, MS SQL 읽기 복제 지원**

### 원칙 4. 네트워킹 선택 : 인터넷 전송, 프라이빗 네트워크 연결, 클라우드 내부 네트워크
 ![](/img/performanceEfficiency013.png)   
 - 클라우드와 온프레미스를 바로 연결시 **프라이빗 네트워크 연결** 고려
 - Load balancer : 비용효용 측면에서 유리
 - VPN Peering : VPC간 통신을 외부 경유하지 않고 내부간 연결하여 불필요한 우회를 절약


### 원칙 5. 성능 모니터링
![](/img/performanceEfficiency014.png)   
 - 성능지표에 임계치 기반에 이벤트를 생성 -> 성능 변화에 신속한 대응
 - 성능에 대한 누적추이를 관찰하여 자원을 조정