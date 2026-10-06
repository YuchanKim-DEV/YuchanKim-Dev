<p align="left"><img src="https://komarev.com/ghpvc/?username=YuchanKim-DEV&style=flat-square&color=8B949E&label=Profile+Views"/></p>

<div align="center">

# 김유찬 · Yuchan Kim

<img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=500&size=19&pause=1600&color=8B949E&center=true&vCenter=true&width=520&lines=Backend+Developer;Java+%C2%B7+Spring+Boot+%C2%B7+Kafka+%C2%B7+PostgreSQL;Don't+Stop%2C+Keep+Going" alt="Backend Developer"/>

<br/>

<a href="https://yuchankim-dev.github.io/"><img src="https://img.shields.io/badge/Blog-181717?style=for-the-badge&logo=github&logoColor=white" alt="Blog"/></a>
<a href="mailto:sksk7799@gmail.com"><img src="https://img.shields.io/badge/Email-EA4335?style=for-the-badge&logo=gmail&logoColor=white" alt="Email"/></a>

</div>

<br/>

## 👤 About

<table width="100%">
<tr>
<td width="130"><b>학력</b></td>
<td width="900"><img src="https://upload.wikimedia.org/wikipedia/commons/thumb/f/f5/Boston_University_seal.svg/40px-Boston_University_seal.svg.png" height="16" valign="middle"/>&nbsp; Boston University — Computer Science (B.A.)</td>
</tr>
<tr>
<td><b>경력</b></td>
<td>백엔드 개발자 · 2025-04-24 ~ 현재 (<!--YM_START-->1년 5개월차<!--YM_END--> · <!--DAYS_START-->529<!--DAYS_END-->일째)</td>
</tr>
<tr>
<td><b>주요 경험</b></td>
<td>Kafka 기반 실시간 데이터 파이프라인 · 음성인식(STT) 연동 백엔드 · TCP 소켓 서버</td>
</tr>
<tr>
<td><b>자격증</b></td>
<td>SQLD · 네트워크관리사 2급 · 리눅스마스터 2급</td>
</tr>
<tr>
<td><b>관심 분야</b></td>
<td>대용량 트래픽을 견디는 백엔드 설계, 이벤트 스트리밍</td>
</tr>
<tr>
<td><b>기록</b></td>
<td><a href="https://yuchankim-dev.github.io/">Don't Stop Keep Going</a> — 배운 것을 글로 남기며 매일 쌓아가는 중</td>
</tr>
</table>

## 💼 Projects

> 고객사 보안상 고객사명은 표기하지 않았습니다. 모두 콜센터 STT(음성인식) 솔루션 납품 프로젝트이며, 그중 제가 맡은 백엔드 영역만 정리했습니다.

### STT 솔루션 납품

`2026.09 ~ 진행 중` &nbsp; 해외(대만어) 확장, STT 결과 컨슈머 재설계

**역할** &nbsp;국내 버전 Kafka 컨슈머를 해외 환경에 맞게 재설계 · 대만어 전용 마스킹 API 연동 · 테스트 환경 배포 및 실데이터 검증<br/>
**기술** &nbsp;Java · Spring Boot · Kafka · MySQL · Docker

**문제 해결**
- **STT 이벤트 스펙 변경 대응** — 통화당 실시간 이벤트 4종(Open/Partial/Final/Close)이 통화 종료 후 1건으로 바뀐 스펙에 맞춰 컨슈머를 재작성. 이벤트 사이의 세션 상태 관리를 통째로 걷어내 구조를 단순화
- **마스킹 전 원문이 DB에 남던 구간 제거** — 기존 구조는 원문 저장 → 통화 종료 시 마스킹 UPDATE라 잠시 원문이 남았다. 통화 전체 텍스트를 미리 알 수 있다는 점을 이용해 마스킹 후 저장으로 순서를 바꿈
- **마스킹 결과가 엉뚱한 문장에 붙을 위험 발견** — 실제 API가 화자별로 문장 번호를 0부터 다시 매기는 것을 확인하고, 응답 매칭 키를 (화자, 문장 번호) 쌍으로 변경
- **재처리 시 통화 전체 중복 저장 방지** — 저장 후 커밋 전에 프로세스가 죽으면 통화가 통째로 다시 들어오는 문제를, 저장 전 해당 통화·화자 행을 지우는 방식으로 멱등하게 처리
- **동작하지 않던 설정 정리** — 하드코딩돼 설정값이 무시되던 토픽, 국내 에러 토픽을 같이 구독해 에러 로그가 중복 적재될 수 있던 구조를 분리

**결과** &nbsp;테스트 환경 실데이터 217건 저장, 금칙어·암호화 실패 0건, graceful shutdown 정상 동작 확인


### 운영 및 유지보수

`2026.07 ~ 2026.08` &nbsp; 납품한 시스템의 운영·장애 대응·운영 도구 개발에 집중

- **대량 STT 결과 추출 배치 개발 (3만여 건)**
  - 기존 쉘 스크립트로 100분 걸리던 원인이 건당 DB 왕복임을 찾아, IN 청크 조회로 **DB 왕복을 약 4만 회 → 200회로 단축**. 운영 DB 부하를 고려해 커넥션 풀·동시 실행 수 상한을 보수적으로 고정
  - 블록 단위 체크포인트로 **중단 후 재실행해도 누락·중복이 없게** 설계, 조회 실패를 빈 결과로 삼켜 데이터가 조용히 빠지던 결함 수정
  - 100건 시험에서 5건이 흔적 없이 사라지던 문제 → 결과에 담기지 못한 건을 사유별 리포트로 분리
  - 서버에서 외부 설정이 적용되지 않던 문제를 **Spring 설정 우선순위**(프로필 파일 > 일반 파일)에서 원인을 찾아 해결, 실행 전 설정 점검 명령 추가
- **서버 2대 구성 장애 분석** 및 배포 설정 정비
- **통합 테스트 도입** — Kafka 없이 컨슈머 전체 흐름(STT → 분류 → 요약)을 검증하는 테스트 작성, 테스트 DB가 없는 환경에서도 빌드가 깨지지 않게 조건부 실행
- **SSL 인증서 조회 API** — 인증서 교체 작업 지원용. 조회 대상 도메인을 제한해 임의 호스트 스캔 통로가 되지 않게 방어


### STT 솔루션 납품

`2026.02 ~ 2026.06` &nbsp; 실시간 음성 수신 TCP 서버 + STT 결과 전달 컨슈머

**역할** &nbsp;Netty 기반 TCP 서버 개발(음성 바이트 수신 → gRPC로 STT 엔진 전달) · STT 결과를 외부 API로 전달하는 Kafka 컨슈머 개발 · AWS 운영 배포<br/>
**기술** &nbsp;Java · Spring Boot · Netty · gRPC · Kafka · Docker · AWS

**문제 해결**
- **음성 스트림 수신 및 STT 엔진 연동** — Netty로 음성 바이트 데이터를 수신해 gRPC로 STT 엔진에 전달, 송·수신(RX/TX) 채널이 섞이던 문제 해결
- **채널 증설 대응 (200 → 480채널)** — 연결 ID 파싱 오류 수정과 채널 수 제한 적용 후, 프로필 분리 + 작업 균등 분배로 서버 간 부하 분산
- **Consumer LAG 누적 해결** — 외부 API 호출 중 연결이 조기 종료(prematurely closed)되며 LAG이 쌓이던 문제를 재시도 + 비동기 처리로 해결. NAT 환경의 Kafka 연결 문제와 종료(graceful shutdown) 방식도 함께 개선
- **병렬 처리와 메시지 순서 보장 양립** — 파티션별 워커 분리 + 스트라이프 큐로 병렬 처리하면서도 같은 통화의 메시지 순서와 통화 ID를 유지
- **운영 가시성 개선** — 토큰 인증 오류 추적 로그 구체화, 헬스체크 포트를 서비스 포트와 분리, 로그 노이즈 감소


### STT 솔루션 납품

`2025.12 ~ 2026.02` &nbsp; 실시간 STT 결과 컨슈머 + 개인정보 마스킹

**역할** &nbsp;실시간 STT 이벤트를 받아 암호화 저장하는 Kafka 컨슈머 개발 · 개인정보 마스킹·금칙어 API 연동 · 운영 배포 및 운영 도구 개발<br/>
**기술** &nbsp;Java · Spring Boot · Kafka · MySQL · Vault · Linux(systemd)

**문제 해결**
- **개인정보 마스킹 연동** — 확정 문장 단위로 마스킹 호출, 하드코딩된 타임아웃을 설정값으로 전환, 금칙어 검사 타임아웃을 2초 → 1초로 줄여 처리 지연 단축
- **불필요한 저장·로그 제거** — 중간(Partial) 이벤트는 즉시 건너뛰고 빈 결과는 저장하지 않으며, 발화 시각은 Kafka 타임스탬프로 저장
- **기존 데이터 재마스킹 애플리케이션** — 날짜·통화 이중 체크포인트로 중단 지점부터 재개, 배치 UPDATE로 결과 컬럼만 갱신
- **DB 크레덴셜 로테이션 감시 재설계** — 무한 루프 감시 데몬이 죽은 뒤 50일간 방치된 것을 발견. 판단 기준을 "시간"에서 "크레덴셜이 실제로 바뀌었는가"로 바꾸고 systemd 타이머 기반 1회성 스크립트로 재구성해 데몬이 죽는 문제를 구조적으로 제거. root로 도는 스크립트를 서비스 계정이 수정하지 못하게 잠가 권한 상승 경로 차단
- 장애 알림 연동, 일별 로그 30일 보관 정책 적용


### STT 솔루션 납품

`2025.08 ~ 2025.12` &nbsp; STT → 유형분류 → 요약 파이프라인 백엔드

**역할** &nbsp;STT·분류·요약 엔진 결과를 Kafka로 주고받아 저장하는 파이프라인 백엔드 개발 · 벡터DB 이관 API · 누락 데이터 기간별 복구 애플리케이션<br/>
**기술** &nbsp;Java · Spring Boot · Kafka · JPA · MySQL · Qdrant

**문제 해결**
- **운영 메모리가 하루 3%씩 증가하던 원인 규명** — 중복 차단 레지스트리의 만료 검사가 신규 등록 때만 돌아서, 통화 ID가 매번 유일한 특성상 만료가 영영 실행되지 않던 것이 원인. 서버 2대 구성에서 생긴 중복 행 때문에 완료 판정이 실패해 해제되지 않던 근본 원인까지 함께 수정
- **서버 2대 간 중복 처리 방지** — 서버마다 따로인 인메모리 맵을 DB 테이블로 옮기고 `INSERT IGNORE` 영향 행 수로 판정해 동시 요청도 한 건만 통과. DB 장애 시에는 중복보다 통화 유실이 더 나쁘다고 판단해 통과(fail-open)시킴
- **긴 파일명 하나로 통화 처리 전체가 멈추던 문제** — 컬럼 길이 초과로 저장이 실패하며 뒤 단계까지 중단되던 것을, 컬럼 특성별로 방어(JSON은 비우고 나머지는 잘라 저장)
- **화자 구분이 통째로 뒤집히던 문제** — 실제 엔진 라벨(`SPEAKER_00`)과 비교 기준(`SPEAKER_0`)·대소문자 불일치로 모든 발화가 상담사로 저장되던 것을 정규화 + 회귀 테스트로 해결. 상담사 확정 멘트 기반 판별을 추가하되, 애매하면 판별을 포기해 오판을 막음
- **요약 결과 영구 유실** — 중복 행 조회 예외가 컨슈머에서 삼켜져 요약이 사라지던 문제, 응답 없는 외부 API에 요청이 무한 대기하던 문제(타임아웃 추가) 수정
- **운영 DB에서만 조회가 항상 실패하던 문제** — 테이블명 대소문자를 구분하는 MySQL 설정 차이를 찾아 쿼리 수정

<br/>

## 🛠 Tech Stack

<table width="100%">
<tr>
<td width="130"><b>Language</b></td>
<td width="900">
<img src="https://img.shields.io/badge/Java-ED8B00?style=flat-square&logo=openjdk&logoColor=white"/> <img src="https://img.shields.io/badge/SQL-336791?style=flat-square"/>
</td>
</tr>
<tr>
<td><b>Framework</b></td>
<td>
<img src="https://img.shields.io/badge/Spring%20Boot-6DB33F?style=flat-square&logo=springboot&logoColor=white"/> <img src="https://img.shields.io/badge/Spring%20MVC-6DB33F?style=flat-square&logo=spring&logoColor=white"/>
</td>
</tr>
<tr>
<td><b>Network</b></td>
<td>
<img src="https://img.shields.io/badge/Netty-000000?style=flat-square"/>
</td>
</tr>
<tr>
<td><b>Database</b></td>
<td>
<img src="https://img.shields.io/badge/PostgreSQL-4169E1?style=flat-square&logo=postgresql&logoColor=white"/> <img src="https://img.shields.io/badge/MySQL-4479A1?style=flat-square&logo=mysql&logoColor=white"/> <img src="https://img.shields.io/badge/MariaDB-003545?style=flat-square&logo=mariadb&logoColor=white"/>
</td>
</tr>
<tr>
<td><b>Messaging</b></td>
<td>
<img src="https://img.shields.io/badge/Apache%20Kafka-231F20?style=flat-square&logo=apachekafka&logoColor=white"/>
</td>
</tr>
<tr>
<td><b>Container</b></td>
<td>
<img src="https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white"/>
</td>
</tr>
<tr>
<td><b>OS</b></td>
<td>
<img src="https://img.shields.io/badge/Ubuntu-E95420?style=flat-square&logo=ubuntu&logoColor=white"/> <img src="https://img.shields.io/badge/Red%20Hat-EE0000?style=flat-square&logo=redhat&logoColor=white"/>
</td>
</tr>
<tr>
<td><b>Version&nbsp;Control</b></td>
<td>
<img src="https://img.shields.io/badge/Git-F05032?style=flat-square&logo=git&logoColor=white"/> <img src="https://img.shields.io/badge/GitHub-181717?style=flat-square&logo=github&logoColor=white"/> <img src="https://img.shields.io/badge/GitLab-FC6D26?style=flat-square&logo=gitlab&logoColor=white"/>
</td>
</tr>
<tr>
<td><b>Build</b></td>
<td>
<img src="https://img.shields.io/badge/Gradle-02303A?style=flat-square&logo=gradle&logoColor=white"/>
</td>
</tr>
<tr>
<td><b>CI/CD</b></td>
<td>
<img src="https://img.shields.io/badge/GitHub%20Actions-2088FF?style=flat-square&logo=githubactions&logoColor=white"/>
</td>
</tr>
<tr>
<td><b>IDE</b></td>
<td>
<img src="https://img.shields.io/badge/IntelliJ%20IDEA-000000?style=flat-square&logo=intellijidea&logoColor=white"/>
</td>
</tr>
<tr>
<td><b>AI</b></td>
<td>
<img src="https://img.shields.io/badge/Claude-D97757?style=flat-square&logo=claude&logoColor=white"/>
</td>
</tr>
</table>
