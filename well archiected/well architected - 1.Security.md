# Well Architected - Security
 - 정보보안 : 정보 취급 중 발생하는 정보의 훼손, 변조, 유출을 막기 위한 활동

## 1. 클라우드 보안의 특징
 -  CIA Triad

   ![security001](/img/security001.png)
 
 - On-premises

  ![security002](/img/security002.png)
  ![security005](/img/security005.png) 

 - Cloud

  ![security003](/img/security003.png)
 
 - 신뢰구간

  ![security004](/img/security004.png)
  ![security006](/img/security006.png)

### 클라우드의 기밀성
 - 클라우드를 위한 접근 통제 필요
### 클라우드의 무결성
 - On-premises - 클라우드 간 연결 보안 필요
 - 클라우드의 가용성
   - 클라우드 환경에 특화된 생존성 보장 방안 필요

## 2. 클라우드 보안의 영역
 - 클라우드 공동책임모델과 보안의 책임

![security007](/img/security007.png)
 - 클라우드에서 필요한 보안 활동들

![security008](/img/security008.png)

## 3. 보안을 위한 Well-Architected 설계원칙
### 원칙1. 역할기반의 권한 정책 구현
 - 관리자권한, 사용자권한, 조회자권한
  - 사내 여러팀이 있거나 대형 프로젝트인 경우 : 역할별 그룹 권한 관리
     ![security009](/img/security009.png)
 - 세부원칙 1 : 최소한의 권한부여
 - 세부원칙 2 : 그룹기반 사용자 권한 관리
 - 세부원칙 3 : 인증키 만료 기간 설정 및 접속 IP 제한 설정
 - 세부원칙 4 : 사용자 활동 로깅 활성화
   - 주기적으로 대조하여 활동이 없는 권한은 회수

### 원칙2. 저장 중 데이터 보호, 전송 중 데이터 보호
 - 중요정보의 암호화
   - 저장중 데이터 보호 전략 : 백업/스냅샷
   - 중요도에 따라 백업 주기를 설정
 - 전송 중 데이터 보호 전략 : VPN, Direct Connect, SSL
   - SSL/TLS 인증서 통합관리 

### 원칙3. 가능한 모든자원의에 보안 적용
 - 세부원칙 1 : Virtual Server는 최대한 프라이빗 서브넷에 배치하여 보호
   - Private Subnet은 외부에서 요청할 때 사용하는 NAT IP를 할당하지 않음
   ![security010](/img/security010.png)
   - 로드밸런서를 통한 인바운드 단일 엔드포인트로 접근       
 - 세부원칙 2 : 관리 서버(Bastion Host)로 프라이빗 자원을 관리(지정된 외부 IP접근)
   ![security011](/img/security011.png)
 - 세부원칙 3 : 보안서비스, Security Group, Firewall 정책을 서버 및 네트워크 기능에 적용
   ![security012](/img/security012.png)
   ![security013](/img/security013.png)

 - 세부원칙 4 : 다층 방어를 통한 계층별 트래픽 제어

   ![security014](/img/security014.png)

### 원칙4. 지속적인 보안 관제와 추적 수행
  - 세부원칙 1 : 워크로드의 특성에 따른 보안 서비스 적용
  
      ![security015](/img/security015.png)
  - 세부원칙 2 : Security Monitoring을 통한 통합 보안 관제
  - 세부원칙 3 : 추적 기능 활성화
    - 서비스 로깅 활성화 -> 이상 접근 징후 분석

### 원칙5. 지속적으로 취약점 개선 및 운영
  - 세부원칙 1 : Virtual Server Image Hardening
	  - 골든 이미지 생성 및 유지 관리
  - 세부원칙 2: 웹취약점 점검을 통한 웹 애플리케이션 취약점 개선
    ![security016](/img/security016.png)

### 원칙6. 보안이벤트에 신속대응
  - DDos 공격의 예
  
    ![security017](/img/security017.png)