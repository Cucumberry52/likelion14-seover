# 운영 실습 요약

[README로 돌아가기](../README.md)

Notion 메인 페이지의 활동 정리와 4회차 GitHub 자료를 바탕으로, 스터디에서 다룬 운영 기능과 확인 기준을 요약했습니다.

## 1. 실행 환경과 데이터 보존

서버 실행 여부를 프로세스·포트·HTTP 응답·로그로 확인한 뒤, Docker 이미지와 컨테이너로 실행 환경을 구성했습니다. PostgreSQL은 별도 컨테이너로 분리하고 Named Volume을 연결했습니다.

| 확인 대상 | 관찰할 내용 |
| --- | --- |
| 서버 실행 | 프로세스와 포트, HTTP 응답, 앱 로그가 서로 일치하는지 |
| 컨테이너 운영 | 상태, 포트 매핑, 로그, 자원 제한과 재시작 설정 |
| 데이터 유지 | 컨테이너를 다시 만든 뒤에도 데이터가 유지되는지 |
| 트랜잭션 | 변경 작업을 `ROLLBACK`했을 때 이전 상태로 돌아오는지 |

[WEEK1](https://github.com/likelion-server-study/WEEK1) · [WEEK2](https://github.com/likelion-server-study/WEEK2) · [WEEK3](https://github.com/likelion-server-study/WEEK3)

## 2. Nginx를 공통 입구로 구성

4회차에서는 Nginx만 호스트 포트를 공개했습니다. `proxy-net`은 Nginx와 앱을, `db-net`은 앱과 PostgreSQL을 연결합니다. 앱은 두 네트워크에 참여하지만 Nginx는 DB 네트워크에 참여하지 않습니다.

| 단계 | 확인 기준 |
| --- | --- |
| 연결 | 컨테이너 기동과 `nginx -t` 성공, Nginx를 경유한 응답 `200` |
| 접근 분리 | 앱·DB가 실행 중이어도 외부에서 게시된 포트로 직접 접속되지 않는지 |
| 정적 파일 캐시 | 실제 정적 파일의 `BYPASS`·`MISS`·`HIT`, 시간과 앱 전달 횟수 비교 |
| 요청 제한 | 정상 요청 `200`과 제한 응답 `429`·`413`·`405`·`403` 구분 |
| 기본 보안 | 버전 노출 축소와 보안 헤더 적용 결과 |
| 앱 장애 | Nginx 안내 화면과 상태 코드 `502`, 관련 access/error log 확인 |
| 복구 | 앱을 다시 실행한 뒤 정상 응답으로 복구되는지 |

캐시의 효과는 응답 헤더와 앱 전달 로그로 확인합니다. 요청 보호는 각 제한을 별도로 시험하고, 응답 상태와 로그를 함께 비교합니다.

[4회차 전체 자료](https://github.com/likelion-server-study/WEEK4) · [Nginx Notion 문서](https://app.notion.com/p/Nginx-1-3b1ac3ee3cde80fa99e2f43dd1b0c21a)

## 3. 요청 분산과 장애 우회

PetClinic을 app1·app2로 실행하고 Nginx가 요청을 나누어 전달하도록 구성했습니다. 앱 하나를 중단한 경우와 모두 중단한 경우를 구분해 응답과 로그를 관찰했습니다.

- 정상 상태에서는 앱별 Tomcat access log를 비교해 요청 전달 대상을 확인합니다.
- 앱 하나가 중단되면 다른 앱으로 요청이 우회되는지 확인합니다.
- 모든 앱이 중단되면 안내 화면을 제공하면서 HTTP `502` 상태를 유지합니다.
- 앱 재시작 뒤 응답과 로그로 복구 여부를 확인합니다.

[WEEK5](https://github.com/likelion-server-study/WEEK5)

## 4. 로그와 지표를 통한 관측

Nginx access/error log, 앱별 Tomcat access log, PostgreSQL 로그로 요청 경로와 오류 위치를 확인했습니다. Actuator·Micrometer로 앱 지표를 노출하고 Prometheus·Grafana로 수집·시각화했습니다.

| 도구 | 역할 |
| --- | --- |
| Actuator·Micrometer | 요청 수, 응답 시간, JVM과 DB 연결 상태 등의 지표 제공 |
| Prometheus | 앱에서 제공하는 지표를 주기적으로 수집 |
| Grafana | 대시보드와 경고로 상태·성능 변화 확인 |
| k6 | 부하를 발생시키고 장애 전·중·후 응답 시간과 실패율 비교 |

부하 조건과 실행 환경에 따른 응답 시간과 실패율은 원본 회차 기록에서 확인할 수 있습니다.

[WEEK6](https://github.com/likelion-server-study/WEEK6)

## 5. Kubernetes 기초

7회차에는 ConfigMap·Secret·PostgreSQL·PetClinic을 YAML로 정의하고 `kubectl apply`로 적용하는 방식을 다뤘습니다. 별도 기초 실습이며, 전체 Compose 서비스가 Kubernetes로 이전되었다는 의미는 아닙니다.

[7회차 Notion 기록](https://app.notion.com/p/8-28-7-3c9ac3ee3cde8042aea3e7992198c87a)
