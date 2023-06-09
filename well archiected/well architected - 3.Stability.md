# Well Architected - Stability

## 1. 안정성의 개념
 - 안정성 전제 : 모든 IT장치는 고장난다.
    * IT서비스는 언젠가는 고장 날 장비에서 가동된다는 전제를 가지고 설계해야 한다.
 - 가용성 vs 안정성
    * 가용성 : 장애에도 불구하고 서비스를 계속 유지 할 수 있는가?
    * 안정성 : 장애가 발생 했을 때, 얼마나 신속하게, 어느수준까지 복구할 수 있는가? (계획에 초첨)

### 안정성 목표 수준 - RTO Recovery Time Object (복구 시간 목표)
 - 서비스 관점
 - 장애, 재해 발생으로 서비스가 중단되었을 때, 얼마만에 서비스를 재개해야 하는가에 관한 복구 시간 목표
 - 설계 단계에서 RTO 목표 수준에 맞게 IT자원 투자 규모를 결정
 - RTO가 0 -> 장애가 발생하더라도 실시간으로 복구
 - 높은 RTO수준(RTO가 0에 근접) -> 높은 IT 투자 규모를 감수할 만한 중요한 시스템

### 안정성 목표 수준 - RPO Recovery Point Object (복구 지점 목표)
 - 데이터 관점, 복제와 백업
    * 복제 : 원본 저장소와 동일한 장비를 만들어 데이터가 생성,변경 되면 그대로 복제
    * 백업 : 일정 주기로 원본 데이터를 복사하여 저장
 - 복제 RPO 
    * 동기식 : 원본 데이터를 복제본으로 전송하여 저장하는 **시간차, 지연 시간**이 RPO
    * 비동기식 : 복제하도록 설정 해놓은 **시간 간격**이 RPO
 - cf) 백업 방식은 백업주기가 RPO
 ![](/img/stability001.png)
 - 데이터 손실 기간 결정 = 투자 규모 결정

## 2. 클라우드와 안정성
 - 온프레미스의 경우 일정 수준의 RTO, RPO를 구현하려면 그에 비례하는 IT 투자 결정이 필요
 - 클라우드는 비교적 안정성 향상에 유리
 - 다양한 RTO, RPO 수준을 쉽게 결정 할 수 있음
    * 신속한 자원 생성, 용량 조정, 자원 생성의 자동화 -> 유연성
    * 서비스 생존성 확보, 데이터 손실 최소화
 - 온프레미스는 하냐 마냐(do or not) / 클라우드는 얼만큼 하냐(how much)

### Lv1. 백업(비용 최소화)을 통한 데이터 보호 설계
![](/img/stability002.png)
 - 사내 데이터 센터의 Backup Server를 통해 DR에 백업
 - West Region의 Backup 서비스를 통해 Object Storage에 백업본 저장, 저장된 백업을 DR복제를 통해 백업
 - 백업주기가 1일이라면 RPO의 최대시간은 24시간
 - 투자비용 
    * 사내데이터 센터 : Backup Server, DR 서버의 Object Storage 저장용량 비용
    * West Region : Backup service, Object Storage 저장용량 비용

### Lv2. Cold DR 사이트 설계
![](/img/stability003.png)
 - Cold : 동일한 가상머신을 DR사이트에 구성하지만 실제로는 작동하지 않는 상태
 - Warm : 작동 상태로 대기
 - 사내 데이터 센터는 VM 마이그레이션으로 DR
 - West Region은 Virtual Server의 custom 이미지를 생성, 복사하여 DR
 - WEB WAS는 각 주사이트의 운영 서버의 용량과 유사한 서버타입을 선택한 후 정지상태로 대기
 - DataBase는 주사이트 용량보다 작은 서버 타입을 선택하고, 비동기식 복제 구성
 - GSLB를 사내데이터 센터 - East Region, West Resgion- East Resgion 간에 배치하고 Round Robin 알고리즘, Health Check 설정
 - RTO : Virtural Server 시작(기동) 시간, GSLB 전환 작업 시간(장애인지부터)
 - RPO : 데이터베이스 복제 지연 시간 -> 데이터 손실은 비교적 낮음
 - 온프레미스에는 구현이 힘듬. 적은 비용 대비 높은 RTO, RPO

### Lv3. Warm DR 사이트 설계
![](/img/stability004.png)
 - Virtural Server의 용량을 굳이 주 Site의 용량과 같을 필요 없음 (작은 타입으로 배포) -> 짧은 가동 시간 및 낮은 비용
 - DR 상황시, 원래 필요했던 서버 타입으로 확장
 - GSLB 요청 전환
 - RTO : 장애발생 감지 및 GSLB 전환 작업 시간
 - RPO : 데이터베이스 복제 지연 시간 -> 실시간에 가까운 복구
 - 높은 비용 만큼 높은 안정성 유지
 - 온프레미스 DR < 클라우드 DR
    * 초기 투자 비용 감축
    * 낮은 비용으로 RTO, RPO 안정성 확보


## 3. 안정성을 위한 Well-architected 설계원칙

### 원칙1. 기능을 분산하고 이중화하여 복원력, 가용성 증대
![](/img/stability005.png)

### 원칙2. 모든 자원을 백업
 - Virtual Server 보호 세부전략 1 : Custom Image를 최신상태로 유지
 ![](/img/stability006.png)
 - Virtual Server 장애시 Custom Image로 새로운 Virtual Server를 생성하고 Reserved IP를 새로운 서버로 옮기면 전환 끝
- Virtual Server 보호 세부전략 2 : Backup 서비스로 정기적 백업
- 저장소 데이터 보호 세부전략 1 : Database 서비스로 정기적 백업
- 저장소 데이터 보호 세부전략 2 : File Storage에서 Snatshot을 통해 백업
- 저장소 데이터 보호 세부전략 3 : Object Storage에서 버전관리 및 복제

### 원칙3. 템플릿으로 자원 관리 및 자동화
 - SCP에서는 CLI, Open API를 통해 자원 생성, 수정, 삭제를 스크립트화

### 원칙4. 가용 영역에 자원을 분산
 - 가용 영역 : Region 내에서 물리적으로 격리되어 있으면서 고속 프라이빗 네트워크로 연결되어 있는 데이터 센터 집합
    * 다른 가용 영역에서 호스트 수준의 장애, 계층/하이퍼바이저 수준의 유지보수 작업이 발생해도 영향을 받지 않고 서비스 유지가능
 - 가용성영역에서 다룬 2. 호스트 또는 기능 수준 장애 / 3. 정기 유지보수을 이 가용 영역을 활용할 수 있다.

### 원칙 5.  주기적으로 Game Day를 수행
 - 계획된 시나리오대로 진행되지 않을 경우?
    * 복구 절차에 변경 사항이 반영되지 않을 수 있다.
    * 수정 사항을 모두 체크 할 수 없다.
    * 하지만 복구는 신속해야 한다.
 - 모의 장애 상황에서 시나리오대로 진행해봄으로서 신속한 복구를 준비할 수 있다.