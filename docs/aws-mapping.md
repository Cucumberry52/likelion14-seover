# 기존 실습과 AWS 서비스 연결

[README로 돌아가기](../README.md) · [8회차 Notion 원문](https://app.notion.com/p/8-31-8-3c9ac3ee3cde80bfb733f942c6486b34) · [WEEK8](https://github.com/likelion-server-study/WEEK8)

8회차에서는 로컬에서 다룬 서비스 운영 구조를 AWS 서비스와 연결해 학습했습니다. 아래는 역할을 이해하기 위한 비교이며, 각 기술이 일대일로 동등하거나 모든 AWS 구성을 실제 구축했다는 의미는 아닙니다.

## 서비스 운영에서 클라우드로

| 기존 실습 | 연결해서 학습한 AWS 개념 | 연결 지점 |
| --- | --- | --- |
| Docker가 실행되는 로컬 컴퓨터 | EC2 | 앱·웹 서버를 실행할 컴퓨팅 환경 |
| Docker 이미지 | AMI | 실행 환경을 이미지로 재사용한다는 관점. AMI는 가상 컴퓨터 이미지 |
| Compose의 `proxy-net`·`db-net` | VPC·Subnet | 서비스와 접근 경로를 네트워크 단위로 구성 |
| Nginx만 외부에 공개 | Subnet·Route Table·Internet Gateway | 인터넷 연결과 서비스 진입 경로 구성 |
| 앱·DB의 접근 대상 제한 | Security Group | 리소스별 허용 포트와 통신 대상 제어 |
| PostgreSQL 컨테이너 직접 운영 | RDS | DB를 직접 관리하는 방식과 관리형 DB 운영 비교 |
| DB를 내부 네트워크에 배치 | DB Subnet Group | RDS의 배치 네트워크 구성 |
| Named Volume | EBS | 실행 환경의 수명과 데이터 저장을 분리한다는 관점 |
| Nginx의 앱 요청 분산 | ALB·Target Group | 요청을 여러 앱 서버로 전달 |
| 앱 중단 시 다른 앱으로 우회 | ALB Health Check | 앱 상태와 요청 전달 대상 관리 |
| Prometheus·Grafana | CloudWatch | 서비스·리소스 상태와 지표 관찰 |
| Nginx 정적 파일 캐시 | CloudFront | 캐시를 통한 반복 요청 처리. CloudFront는 분산된 CDN |

## 추가로 비교한 개념

- **IAM:** AWS 리소스를 생성·조회·변경할 주체와 권한을 관리합니다. Compose 네트워크나 컨테이너 설정의 직접적인 대응물이 아닙니다.
- **Lambda·API Gateway:** 항상 실행되는 PetClinic 서버와 비교해 이벤트 기반 실행과 API 진입점을 학습했습니다.
- **JMeter:** AWS 서비스가 아닌 부하 테스트 도구이며, 앞서 사용한 k6와 같은 도구 범주에서 비교했습니다.

## 자료를 읽는 순서

1. [스터디 메인 기록](https://app.notion.com/p/1-bdbac3ee3cde83f7977901a38a6758fa)에서 로컬 운영 구성을 확인합니다.
2. 위 표에서 컴퓨팅·네트워크·DB·로드밸런싱·모니터링 역할을 연결합니다.
3. [8회차 자료](https://app.notion.com/p/8-31-8-3c9ac3ee3cde80bfb733f942c6486b34)와 [WEEK8](https://github.com/likelion-server-study/WEEK8)에서 학습 및 실습 범위를 확인합니다.
