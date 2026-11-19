# measured-labs

백엔드와 인프라에서 "당연하다"고 넘기는 것들을 직접 재 보고 고르는 실험 저장소 모음이다. 저장소마다 먼저 평범한 베이스라인을 만들고, 그 위에서 문제를 재현해 수치로 잰다. 그다음 선택지를 같은 조건에서 실험하고, 수치를 근거로 방식을 정해 ADR(결정 기록)에 남긴다. 조직 이름 measured-labs는 이 방식, "재 보고 고른다"를 뜻한다.

라벨의 뜻은 다음과 같다.

- **완료**: 결론이나 결정이 문서에 있다.
- **진행 중**: ADR을 쓰는 중이거나 최근 작업이 있다.
- **예정**: 설계 문서(README 등)만 있고 실험은 아직 시작하지 않았다.
- **학습 예정**: 코드는 있지만 실행 기록이 없다. 나중에 코드를 함께 읽으며 하나씩 진행한다.
- **측정 실험**: 부하, 성능, 정합성 수치로 방식을 정한다.
- **학습 실험**: 직접 구현하고 동작을 관찰해 원리를 배운다.

`(비공개)`가 붙은 저장소는 비공개라 링크가 조직 구성원에게만 열린다.

## backend-labs-commerce: 커머스 정합성과 동시성

좌석 예약, 결제, 주문, 포인트처럼 돈과 재고가 걸린 API에서 생기는 동시성 문제를 다루는 저장소 목록이다.

| 저장소 | 묻는 것 | 상태 | 성격 | 핵심 결론 |
|---|---|---|---|---|
| [seat-reservation-lab](https://github.com/measured-labs/seat-reservation-lab) | 좌석 1만 석에 20만 명이 몰리면 좌석 예약의 정합성을 어떻게 지키나? | 진행 중 | 측정 실험 | 결제 시작 시 홀드를 연장하고 결제 마감 시각을 두자 결제하고도 좌석을 잃는 사용자가 11.09%에서 0이 됐다. |
| [payment-consistency-lab](https://github.com/measured-labs/payment-consistency-lab) | 중복 요청, 부분 실패, PG 응답 유실이 생기면 우리 기록과 PG 기록이 맞나? | 진행 중 | 측정 실험 | 베이스라인에서 mock PG 지연 2초, 동시 30건이면 DB 커넥션 10개가 모두 PG를 기다려 응답 p50이 5.4초였다. |
| [order-saga-lab](https://github.com/measured-labs/order-saga-lab) | 재고는 차감됐는데 쿠폰이나 결제가 실패하면 주문을 어떻게 되돌리나? | 예정 | 측정 실험 | 해당 없음 |
| [ledger-lab](https://github.com/measured-labs/ledger-lab) | 잔액 1,000P에 1,000P 사용 요청이 동시에 두 번 오면? | 진행 중 | 측정 실험 | 베이스라인에서 동시 사용 2건을 100회 보내자 100회 모두 둘 다 성공해 1,000P로 2,000P를 썼다. |
| [auction-lab](https://github.com/measured-labs/auction-lab) | 마감 1초 전에 같은 금액 입찰이 동시에 들어오면? | 예정 | 측정 실험 | 해당 없음 |
| [notification-lab](https://github.com/measured-labs/notification-lab) | 같은 알림이 두 번 나가거나 발송사가 429를 돌려주면 어떻게 하나? | 진행 중 | 측정 실험 | 해당 없음 |
| [cache-consistency-lab](https://github.com/measured-labs/cache-consistency-lab) | DB는 바뀌었는데 캐시에 옛 값이 남으면 몇 ms까지 허용할까? | 예정 | 측정 실험 | 해당 없음 |
| [integration-concert-ticketing](https://github.com/measured-labs/integration-concert-ticketing) | 좌석, 주문, 결제, 알림 랩을 하나의 예매 흐름으로 합치면 타임아웃 연쇄와 재시도 증폭이 생기나? | 예정 | 측정 실험 | 해당 없음 |
| [integration-auction-to-order](https://github.com/measured-labs/integration-auction-to-order) | 경매 마감 순간의 입찰 폭주가 알림, 주문, 결제로 번지면? | 예정 | 측정 실험 | 해당 없음 |

## backend-labs-tenancy: 멀티테넌시와 인증, 인가

여러 고객사(테넌트)가 한 시스템을 함께 쓸 때 생기는 격리, 공정성, 인증 문제를 다루는 저장소 목록이다.

| 저장소 | 묻는 것 | 상태 | 성격 | 핵심 결론 |
|---|---|---|---|---|
| [tenant-isolation-lab](https://github.com/measured-labs/tenant-isolation-lab) | 개발자가 `WHERE tenant_id`를 빠뜨리면 다른 테넌트 데이터가 보이나? | 예정 | 측정 실험 | 해당 없음 |
| [noisy-neighbor-lab](https://github.com/measured-labs/noisy-neighbor-lab) | 한 테넌트가 50배로 폭주하면 나머지 테넌트의 p99는 얼마나 나빠지나? | 예정 | 측정 실험 | 해당 없음 |
| [tenant-provisioning-migration-lab](https://github.com/measured-labs/tenant-provisioning-migration-lab) | 스키마 1,000개 마이그레이션이 500번째 테넌트에서 실패하면 롤백할까, 계속 진행할까? | 예정 | 측정 실험 | 해당 없음 |
| [tenant-aware-cache-search-lab](https://github.com/measured-labs/tenant-aware-cache-search-lab) | 캐시 키에 `tenant_id`가 빠지면 다른 테넌트의 응답이 나가나? | 예정 | 측정 실험 | 해당 없음 |
| [token-lifecycle-lab](https://github.com/measured-labs/token-lifecycle-lab) | 로그아웃한 사용자의 JWT가 만료 전까지 계속 쓰이면? | 예정 | 측정 실험 | 해당 없음 |
| [authorization-model-lab](https://github.com/measured-labs/authorization-model-lab) | 팀, 폴더, 문서 공유 도메인에는 RBAC, ABAC, ReBAC 중 무엇이 맞나? | 예정 | 측정 실험 | 해당 없음 |
| [cross-tenant-authz-lab](https://github.com/measured-labs/cross-tenant-authz-lab) | 다른 테넌트의 주문 ID를 넣으면 조회, 수정, 삭제가 되나? | 예정 | 측정 실험 | 해당 없음 |
| [sso-service-auth-lab](https://github.com/measured-labs/sso-service-auth-lab) | 한 테넌트의 외부 IdP가 죽으면 다른 테넌트의 로그인은 괜찮나? | 예정 | 측정 실험 | 해당 없음 |
| [gatekeeper-iam-lab](https://github.com/measured-labs/gatekeeper-iam-lab) | 인증, 신원 관리, JSON 정책 평가를 갖춘 IAM 서버를 직접 만들면 어떻게 동작하나? | 예정 | 학습 실험 | 해당 없음 |

## concurrency-labs: 동시성 모델과 스레드

스레드, 이벤트 루프, 락이 실제로 어떻게 동작하고 얼마나 다른지를 다루는 저장소 목록이다.

| 저장소 | 묻는 것 | 상태 | 성격 | 핵심 결론 |
|---|---|---|---|---|
| [sync-async-lab](https://github.com/measured-labs/sync-async-lab) | 동기, 비동기 서버는 I/O 부하에서 처리량이 얼마나 다른가? | 완료 | 측정 실험 | 500→1000 VU 단계 부하 평균에서 Go 3,180, WebFlux 3,161, 가상 스레드 2,333, Spring MVC 954 req/s였다(실패 0). |
| [mvc-webflux-lab](https://github.com/measured-labs/mvc-webflux-lab) | 동시에 기다리는 요청이 스레드 풀보다 많아지면 MVC(일반 · 가상 스레드)와 WebFlux는 얼마나 다른가? | 완료 | 측정 실험 | 100ms 대기 · 1600 VU에서 일반 스레드 1,999(약 2,000에서 정체), 가상 스레드 10,876, WebFlux 15,002 req/s였다. |
| [thread-scheduling-lab](https://github.com/measured-labs/thread-scheduling-lab) | 주기 작업을 sleep 루프와 ScheduledExecutorService로 돌리면 간격이 얼마나 다른가? | 완료 | 측정 실험 | 50ms 간격에서 sleep 루프는 간격이 0.8ms씩 밀렸고 ScheduledExecutorService는 밀리지 않았다. |
| [cluster-fanout-lab](https://github.com/measured-labs/cluster-fanout-lab) | 데이터를 나눠 병렬 처리하고 합치는(fan-out/fan-in) 구조는 어디서 비용이 생기나? | 예정 | 학습 실험 | 해당 없음 |
| [process-thread-lab](https://github.com/measured-labs/process-thread-lab) | 프로세스와 스레드(플랫폼, 가상)는 생성, 전환, 메모리에서 어떻게 다른가? | 학습 예정 | 학습 실험 | 해당 없음 |
| [lost-exception-lab](https://github.com/measured-labs/lost-exception-lab) | 스레드와 프로세스 경계를 넘은 예외는 어디로 사라지나? | 예정 | 학습 실험 | 해당 없음 |
| [deadlock-lab](https://github.com/measured-labs/deadlock-lab) | 대표적인 데드락 4가지를 재현하고 탐지, 해결하면 무엇이 달라지나? | 학습 예정 | 학습 실험 | 해당 없음 |

## cache-labs: 캐시

캐시를 어디에, 몇 개로, 어떤 방식으로 둘지를 수치로 정하는 저장소 목록이다.

| 저장소 | 묻는 것 | 상태 | 성격 | 핵심 결론 |
|---|---|---|---|---|
| [cache-split-lab](https://github.com/measured-labs/cache-split-lab) | 캐시를 가진 서버를 같은 자원으로 두 대로 나누면 히트율과 지연은 어떻게 되나? | 완료 | 측정 실험 | 균등 접근에서는 라운드로빈 두 대가 히트율 50.01%p를 잃고 6.4배 느렸지만, 접근이 쏠리면 키 해시 분배 쪽이 100% 부하부터 p50 16에서 95ms로 늘었다. |
| [spring-cache-lab](https://github.com/measured-labs/spring-cache-lab) | Caffeine, Redis, 2단 캐시는 캐시가 없을 때보다 얼마나 빠른가? | 완료 | 측정 실험 | p95가 캐시 없음 53.15ms에서 2단 캐시 2.06ms, Caffeine 3.11ms, Redis 4.95ms로 줄었다. |
| [redis-atomicity-lab](https://github.com/measured-labs/redis-atomicity-lab) | 여러 서버가 Redis 값을 GET 후 SET 하면 갱신이 사라지나, Lua 스크립트로 막히나? | 학습 예정 | 측정 실험 | 해당 없음 |

## storage-labs: 스토리지와 DB 엔진

트랜잭션, 인덱스, 페이지 I/O처럼 DB 내부가 왜 그렇게 동작하는지를 직접 만들고 재는 저장소 목록이다.

| 저장소 | 묻는 것 | 상태 | 성격 | 핵심 결론 |
|---|---|---|---|---|
| [index-lab](https://github.com/measured-labs/index-lab) | B+tree와 Hash 인덱스는 쿼리 패턴, 데이터 규모, 버퍼풀 크기에 따라 페이지 I/O가 얼마나 다른가? | 진행 중 | 측정 실험 | 해당 없음 |
| [index-structure-lab](https://github.com/measured-labs/index-structure-lab) | 조회만 보면 해시가 빠른데 왜 DB 인덱스는 트리가 기본인가? | 예정 | 학습 실험 | 해당 없음 |
| [acid-lab](https://github.com/measured-labs/acid-lab) | ACID를 인메모리 저장소, CSV, WAL로 직접 만들면 각 속성은 어떻게 깨지고 지켜지나? | 학습 예정 | 학습 실험 | 해당 없음 |
| [db-engine-lab](https://github.com/measured-labs/db-engine-lab) | DB 엔진을 처음부터 만들면 저장, 인덱스, 트랜잭션, 복구가 어떻게 이어지나? | 학습 예정 | 학습 실험 | 해당 없음 |

## network-labs: 네트워크와 프로토콜

프로토콜 계층마다 기본값으로 넘기는 설정이 실제로 얼마의 비용인지 재는 저장소 목록이다.

| 저장소 | 묻는 것 | 상태 | 성격 | 핵심 결론 |
|---|---|---|---|---|
| [http-grpc-lab](https://github.com/measured-labs/http-grpc-lab) | 같은 조건에서 HTTP와 gRPC는 언제 어느 쪽이 빠른가? | 완료 | 측정 실험 | 페이로드 100KB 이하는 gRPC가 30~67% 빨랐고 200KB 이상은 HTTP 바이너리 전송이 14~60% 빨랐다. |
| [http-keepalive-lab](https://github.com/measured-labs/http-keepalive-lab) | Keep-Alive를 끄거나 HTTP/2, HTTP/3로 바꾸면 실제로 얼마나 달라지나? | 예정 | 측정 실험 | 해당 없음 |
| [tcp-nagle-lab](https://github.com/measured-labs/tcp-nagle-lab) | Nagle 알고리즘을 켜 두면 작은 응답에 지연이 얼마나 붙나? | 예정 | 측정 실험 | 해당 없음 |
| [dns-cost-lab](https://github.com/measured-labs/dns-cost-lab) | 서비스 간 호출에서 DNS 조회 비용을 0으로 봐도 되나? | 예정 | 측정 실험 | 해당 없음 |
| [tcp-timewait-lab](https://github.com/measured-labs/tcp-timewait-lab) | Keep-Alive 없이 초당 500건을 보내면 TIME_WAIT 소켓 때문에 포트가 고갈되나? | 예정 | 측정 실험 | 해당 없음 |
| [mtu-fragmentation-lab](https://github.com/measured-labs/mtu-fragmentation-lab) | 응답이 1,460바이트를 넘어 패킷이 둘로 나뉘면 지연이 달라지나? | 예정 | 측정 실험 | 해당 없음 |
| [tls-handshake-lab](https://github.com/measured-labs/tls-handshake-lab) | TLS 1.2, 1.3, 0-RTT의 핸드셰이크 비용은 실제로 얼마나 다른가? | 예정 | 측정 실험 | 해당 없음 |

## orm-labs: ORM

ORM이 막아 준다고 알려진 것을 실제 버전에서 재현해 확인하는 저장소 목록이다.

| 저장소 | 묻는 것 | 상태 | 성격 | 핵심 결론 |
|---|---|---|---|---|
| [jpa-injection-lab](https://github.com/measured-labs/jpa-injection-lab) (비공개) | "ORM을 쓰면 SQL 인젝션은 안 통한다"는 말이 Spring Data JPA와 Hibernate에서도 맞나? | 진행 중 | 학습 실험 | 외부 입력을 정렬 키로 넘기면 응답에 없는 값을 행 순서로 복원할 수 있음을 재현했고, 허용 목록으로 막았다. |

## data-labs: 데이터 파이프라인

로그 수집과 데이터 동기화를 직접 만들어 저장소와 처리 방식을 비교하는 저장소 목록이다.

| 저장소 | 묻는 것 | 상태 | 성격 | 핵심 결론 |
|---|---|---|---|---|
| [log-pipeline-lab](https://github.com/measured-labs/log-pipeline-lab) | 대용량 로그는 PostgreSQL 한 대와 HDFS+Spark 중 어디에 두는 게 나은가? | 완료 | 측정 실험 | 작은 파일을 합친 뒤 WHERE + GROUP BY는 HDFS+Spark가 2.4배, COUNT(*)는 PostgreSQL이 1.8배 빨랐다. |
| [reverse-etl-lab](https://github.com/measured-labs/reverse-etl-lab) | 분석 DB의 데이터를 운영 시스템으로 보내는 역ETL은 안에서 어떻게 동작하나? | 예정 | 학습 실험 | 해당 없음 |

## system-design-labs: 시스템 설계

여러 서비스가 얽힌 시스템을 설계하고, 설계의 약점을 실험으로 확인하는 저장소 목록이다.

| 저장소 | 묻는 것 | 상태 | 성격 | 핵심 결론 |
|---|---|---|---|---|
| [failure-alert-lab](https://github.com/measured-labs/failure-alert-lab) | 주문, 상품, 배송 서비스의 실패를 유실과 중복 없이 담당자에게 알리려면 어떤 구조가 필요한가? | 진행 중 | 측정 실험 | 처음 설계에서는 커밋 직후 크래시 5회가 알림 5건 유실로 이어졌고, 중복을 막은 것은 ack 왕복이 아니라 수신 측 중복 제거였다. |
| [logistics-design-lab](https://github.com/measured-labs/logistics-design-lab) | 이벤트 때 주문이 수백만 건 몰리는 OMS, WMS, TMS는 서버를 어떻게 나눠야 하나? | 진행 중 | 학습 실험 | 해당 없음 |

## algorithm-labs: 알고리즘

알고리즘을 직접 구현하고 같은 데이터에서 실행 시간을 재는 저장소 목록이다.

| 저장소 | 묻는 것 | 상태 | 성격 | 핵심 결론 |
|---|---|---|---|---|
| [sorting-graph-lab](https://github.com/measured-labs/sorting-graph-lab) | 정렬과 그래프 탐색의 복잡도 차이는 10만 건에서 실제로 얼마나 벌어지나? | 완료 | 학습 실험 | 랜덤 데이터 10만 개에서 Quick Sort는 7.6ms, Bubble Sort는 14,033ms였다. |

## frontend-labs: 프론트엔드 동작 원리

프레임워크가 숨기는 동작을 순수 JS로 작게 직접 만들어 확인하는 저장소 목록이다.

| 저장소 | 묻는 것 | 상태 | 성격 | 핵심 결론 |
|---|---|---|---|---|
| [react-portal-lab](https://github.com/measured-labs/react-portal-lab) | React Portal은 DOM을 옮기는데 왜 이벤트와 Context는 원래 부모를 따르나? | 완료 | 학습 실험 | DOM 트리와 논리 트리를 따로 두면 되고, 코드에서는 노드를 만들 때 논리 부모와 DOM을 붙일 곳을 따로 받는 한 줄 차이다. |
| [rendering-lab](https://github.com/measured-labs/rendering-lab) | CSR, SSR, SSG, ISR은 HTML을 누가, 언제 만드나? | 예정 | 학습 실험 | 해당 없음 |

## 진행 방식

- 결정은 저장소마다 `docs/adr/`에 ADR로 남긴다. ADR에는 질문, 가설, 미리 정한 합격 기준, 선택지별 측정값, 결정을 적는다.
- 작업 기록(요구사항 명세, 작업 로그)은 `docs/plans/<날짜>/<작업명>/`에, 다음 작업 후보는 `docs/plans/NEXT.md`에 둔다. 이 방식보다 먼저 만든 저장소는 결과를 README에 정리했다.

마지막 갱신: 2026-11-19
