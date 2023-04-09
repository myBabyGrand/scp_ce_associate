# Well Architected - Cost Optimization

## 1. 클라우드의 가치 : 경제성
![](/img/costOptimization001.png)

## 2. 비용최적화란?
 - 비용최적화 <> 비용절감
 ![](/img/costOptimization002.png)
 - 예) 전세계 지사가 있는 게임회사에서 임직원이 사용할 서비스를 클라우드에 올릴지 고민
 ![](/img/costOptimization003.png)
 - 온프레미스 IT 투자 비용 구조
 ![](/img/costOptimization004.png)
    * 매몰비용 : 데이터센터에 이미 들어간 백본 네트워크, 냉공조 비용, 시설비용 등
 - 클라우드 IT 투자 비용 구조
 ![](/img/costOptimization005.png)
    * 특정시점이 되면 누적비용이 클라우드가 더 많은 시점에 도달
 - CTLP(Clout TCO Limit Point, 클라우드 경제성 한계점)
 ![](/img/costOptimization006.png)
    * 사용자의 정보시스템 사용연수가 CTLP보다 빠르면 클라우드가 유리
 - 클라우드의 가격 경쟁력을 가지는 방법
 ![](/img/costOptimization007.png)
    * PaaS를 통해 개발기간 단축, 인건비 절감, 서비스 조기 오픈
 ![](/img/costOptimization008.png)
    * 원 기울기 직선와 낮춰진 기울기 차이 : 비용최적화
 ![](/img/costOptimization009.png)

## 3. 클라우드 비용최적화 영역

### 낭비되는 클라우드비용
 - 1. 사용되지 않는 클라우드 자원
    * 서버가 늘어나면 일부 서버들에 관리가 부주의해짐
 - 2. Virtual Server 타입 과대 프로비저닝
    * 몇 년치 사용량 증가 감안하여 서버타입을 정했기 때문(온프레미스때의 관례)
 - 3. 부적합한 스토리지 유형 사용
    * 용량이 적어 추가비용이 나가거나, 용량이 너무 커 노는 용량이 많은 경우

## 4. 비용최적화를 위한 Well-Architected 설계원칙
### 원칙 1. 비즈니스 요구 사항과 예산의 절충점(Trade-off)을 결정하여 설계
 - 가용성
    * Virtual Server 2대가 1대 보다 가용성이 훨씬 좋다.
    * 이 경우 Virtual Server 비용 뿐만 아니라 Load Balancer 비용도 추가된다.
 - 안정성
    * 장애, 재해 발생시 RTO, RPO 수준 결정
    ![](/img/costOptimization011.png)
    ![](/img/costOptimization012.png)
    * 백업 전략 활용시 고려할 점
        + 백업 주기 사이 데이터 손실
        + 복구 시간
        + 트래픽 비용과 스토리지 비용
    * 예산과 중요도를 고려하여 RTO, RPO를 적정 수준으로 결정
 - 성능    
    * 처리 성능 보장 수준
    ![](/img/costOptimization013.png)
    * 하이브리드 클라우그 설계시 애플리케이션 연결을 할 경우
        + 소켓통신과 같이 안정적 속도가 필요할 경우 : Direct Connetcion
        + API연동등을 이용하여 안정적 속도가 불필요 할 경우 : VPN
 - 운영
    * 서비스 운영을 위한 전반적인 연략 진단이 필요
    * 비용 절감 전략
        + 교육 훈련 등으로 내재화
        + 외부 자원을 활용하여 지원 수준 결정
    * 클라우드 규모 및 중요도에 따라 결정 : 내부인력 vs 아웃소싱(MSP)
    * 아웃소싱(MSP)할 경우 : 사업장의 SLA에 따라 운영 수준 계약
    * SCP의 MSP : NONE < LITE < BASIC < PREMIUM 등급으로 운영

### 원칙 2. 적합한 컴퓨팅 자원을 선택
 - 클라우드 비용이 가장 많이 발생
 ![](/img/costOptimization014.png)
 - 비용 절감 전략
    * PaaS는 SW 운영 비용을 절감 할 수 있어 내부 인력이 부족할 경우 활용
    * 향후 조정이 가능하므로 (현재)수요에 적합한 서버 선택
    * 워크로드 성격에 맞는 요금제 선택
        + 연간 일정하게 서비스 -> 약정 요금제가 유리
        + 비정기적 사용 -> 무약정 요금제가 유리
        + SCP는 무약정,1년약정,3년약정 운영
    * 소프트웨어 라이선스 검토
        + 코어당 과금하는 SW 라이선스는 라이선스 요금이 증가 할 수 있음
 - 라이선시 비용절감 전략
    * 최대한 소형 virtual server로 분산 및 배치
    * 온프레미스에 라이선스를 보유하고, 퍼블릭 사용 권리가 있다면 BYOL 활용
 - 모범사례
    * 소형 서버 타입으로 시작
    * 모니터링
    * 크기 조정
 - 요금계산기
 ![](/img/costOptimization015.png)  
 - Virtual Server 타입
 ![](/img/costOptimization016.png)  

### 원칙 3. 확장성을 감안한 컴퓨팅 아키텍처를 설계
 - 사업자가 내재적으로 보장하는 확장성 : PaaS
 - 비용 절감 전략 
    * PaaS를 활용한 컴퓨팅 아키텍처 설계
        + 사용자의 확장성 부담 감소
        + 사용자가 사용한 만큼 비용 지불 가능
    * 사용량 증가를 예측할 수 없는 경우 Auto Scaling 사용
 - 모범사례
 ![](/img/costOptimization017.png)
 * 하이브리드 버스팅 : 온프레미스 사용량 급증일 때, 클라우드로 부하를 전환해서 처리
 - Auto Scaling
    * SCP는 약정외도 Auto Scaling이 가능하다.
    ![](/img/costOptimization018.png)
 - Auto Scaling 유의 사항
    * Virtual Server의 시작 속도가 늦으면 대응이 늦으므로 미리 확장해서 부하 대비
    * 천천히 축소(Scale-in)
        + 과금 단위 시간은 1시간
        + 부하 변동 주기가 1시간에 5번이면 scale in이 이미 되버리면 5번 Auto scaling이 발생하여 5시간의 과금이 발생
    * Auto Scaling의 CoolDown Time 조절
        + 기본 설정은 300초
 - Job Scheduling
 ![](/img/costOptimization019.png)
    * 예) 월간 매출 보고서 취합 서버
        + 각 애플리케이션에 통합 : 한달내내 취합
        + 소형 Virtual Server를 별도로 사용 : 월마감 이후 2~3일동안 집중해서 취합

### 원칙 4. 데이터 유형 및 요청 패턴에 따라 스토리지를 선택
 - 비용 절감 전략
    * 데이터 저장 및 요구 사항에 따른 스토리지 선택
        + Object Storage는 요금이 File, Block에 비해 1/4 수준
        + 용량이 큰 문서, 미디어 파일 저장에 효율적
        + 저장소 비용 및 요청 부하를 처리하는 컴퓨팅 자원 오프로드
    * 데이터 수명 주기 관리를 통해 File, Block에서 사용하지 않는 데이터는 저렴한 Object Storage로 옮겨 관리

### 원칙 5. 지속적인 비용 추적을 구현
 - 운영의 관점
 - 비용 절감 전략
    * 클라우드 자워에 대한 태그 연결 규칙 수립 
        + 규칙적으로 태그가 연결된 자원은 그룹에서 관리
    * Cloud Monitoring 지표에 대한 관리 정책 수립
 - 모범사례
 ![](/img/costOptimization020.png)
 - SCP에서 제공하는 비용 추적을 위한 기능
 ![](/img/costOptimization021.png)
 ![](/img/costOptimization022.png)
 ![](/img/costOptimization023.png)

