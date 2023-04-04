# Well Architected - Availabilty
  - 사용자가 일정한 기대를 가지고 IT자원 또는 서비스를 이용하려고 할 때, 그 기대에 부합하는 기능과 성능을 제공할 수 있는 능력
  - 가용성이 높은 서비스의 조건
    - 생존, 성능, 기능
  - 클라우드 가용성 지표 : 가용률
  - 가용률 : 1달 간 클라우드 서비스에 접근해서 사용할 수 있다고 예상하는 확률

## 1. 가용성의 개념
 ![](/img/availability001.png)
  - 99.95%의 가용률
  - 1달 730시간 중 22분 동안 중단 될 수도 있다.
  - 클라우드 가용성의 고려 요소
    - 1. 클라우드 사업자 영역(내부요인, 안정성)
    ![](/img/availability002.png)
    - 2. 클라우드 사용자 영역(주로 외부요인, 가용성)
    ![](/img/availability003.png)

## 2. 클라우드의 고가용성
  - HA : High Availability
  - 1대의 가용률이 99.95% 일 때 2대인 경우
  - 0,05 * 0.05 = 0.0025% -> 가용률은 99.9975%
  - Load balancer<br>
  ![](/img/availability004.png)
  - Load balancer의 문제점 - 데이터 동기화 -> storage의 공유
  ![](/img/availability005.png)  
  - 수평확장 Scale out
    - 물리적 자원을 소프트웨어로 사용
    - 이미지를 복사하여 수평확장 가능

## 3. 가용성을 위한 Well-Architected 설계원칙
  - Iaas : 사용자가 가용성 관리 
  - Paas : 사업자가 가용성 관리
### 원칙 1 : 관리형 서비스로 고가용성 아키텍처를 구현

### 원칙 2 : Auto Scaling으로 유연하게 클라우드 자원을 제공
  - Auto scaling 활용 시 고려사항1 : 클라우드 제한을 고려해 확정을 설계
  ![](/img/availability006.png)
    - SCP에서는 auto scaling으로 늘리 수 있는 서버 대 수 : 10대
    - 10대 이상의 부하처리 -> 높은 사양의 서버 타입으로 변경
  - Auto scaling 활용 시 고려사항2 : 장애를 고려해 서버 수량을 결정
    - 큰 서버타입 보다는 작은 서버타입을 여러개 사용
  - Auto scaling 활용 시 고려사항3 : 어플리케이션을 결합해제(decoupling)
    ![](/img/availability007.png)
    - 3계층 웹 어플리케이션에서 확장을 고려하여 설계를 해야함
    - web - was - DB  (web - was 가 scale out 대상)
    - web - was 가 tight coupling이라면 scale out이 어려움
  - Auto scaling 활용 시 고려사항4 : 상태 비저장 서버를 구현
    - application 메모리에서 상태를 관리한다면 
    - application을 개선하지 않으면 서버를 늘려도 요청 부하가 처리 가능성 저하
    - application 메모리를 사용하지 않고(상태 비저장) 상태관리 데이터베이스를 활용
    - NoSQL DB, Redis
 
### 원칙 3 : 마이크로 서비스 아키텍처를 고려
 - Cloud native architecture
   - 클라우드 인프라를 이용하여 변화하는 수요에 신속하게 대응 할 수 있는 아키텍처
   - MSA 
 - Monolithic vs MSA
  ![](/img/availability008.png)

### 원칙 4: 서버리스 아키텍처를 고려
  - Object Storage
    - Rest API로 접근 : API로 데이터를 접근하는 웹서버
  - K-pop 서비스의 예
    - 1. 전통적인 구성<br>
    ![](/img/availability009.png)
    - 2. Object Storage 활용<br>
    ![](/img/availability009.png)
      - html, css등 정적 컨텐츠를 Object Storage로 관리
      - Fn으로 동적 컨텐츠 처리
      - 서버리스로 구현됨



### 원칙 5 : 온프레미스에서 클라우드로 확장
  - 온프레미스 서버가 포화 될 때 온프레미스의 자원을 클라우드에 복제하여 사용
  ![](/img/availability011.png)
    - GSLB : 평소에는 온프레미스로만 연결. 온프레미스 임계치 초과시 임계치 이하로 떨어질 때까지 클라우드로 연결
    - 클라우드는 scale out 하여 대응   
  - 하이브리드 클라우드
  - 온프레미스의 가용성을 높이는 효과