# APM (Application Performance Monitoring)

Prometheus, Grafana, Loki를 활용한 Application Performance Monitoring(APM) 및 로그 모니터링 환경입니다.

Spring Boot Actuator 메트릭을 Prometheus가 수집하고, 애플리케이션 로그는 Grafana Alloy를 통해 Loki로 전달하여 Grafana에서 통합 조회 및 시각화합니다.

---

## 🛠️ 기술 스택 및 구조

- **Prometheus**
  - Spring Boot Actuator 메트릭 수집 및 저장
  - Port: `9090`

- **Grafana**
  - 메트릭 및 로그 모니터링 대시보드 시각화
  - Port: `3000`
  - HTTPS 적용

- **Loki**
  - Spring Boot 애플리케이션 로그 저장 및 조회
  - Port: `3100`

- **Grafana Alloy**
  - WAS 서버의 Spring Boot 로그 파일(`nohup.out`) 수집
  - 수집한 로그를 Loki로 전송

- **대상 애플리케이션**
  - `KnockIn`
  - Prometheus Metric Endpoint:
    `https://api.knock-in.com/actuator/prometheus`

### Monitoring Architecture

```text
Spring Boot
├── Actuator Metrics
│      ↓
│  Prometheus
│      ↓
│   Grafana
│
└── nohup.out
       ↓
   Grafana Alloy
       ↓
      Loki
       ↓
    Grafana
```

---

## 📋 사전 준비 사항 (Prerequisites)

Grafana 및 Loki에서 HTTPS 접속을 위해 SSL 인증서 파일이 필요합니다.

프로젝트 루트 경로에 `ssl` 디렉터리를 생성하고 다음 인증서 파일을 위치시켜 주세요.

```text
ssl/
├── cert.pem
└── key.pem
```

---

## 🚀 Docker Compose 실행 방법

### 1. 컨테이너 실행

```bash
docker compose up -d
```

또는:

```bash
docker-compose up -d
```

### 2. 컨테이너 상태 확인

```bash
docker compose ps
```

정상적으로 실행된 경우 다음 컨테이너를 확인할 수 있습니다.

```text
prometheus-knock-in
grafana-knock-in
loki-knock-in
```

### 3. 컨테이너 로그 확인

전체 컨테이너 로그:

```bash
docker compose logs -f
```

Loki 로그만 확인:

```bash
docker logs -f loki-knock-in
```

### 4. Loki 상태 확인

HTTP 환경:

```bash
curl http://localhost:3100/ready
```

HTTPS 환경:

```bash
curl -k https://localhost:3100/ready
```

정상 실행 중이라면 다음과 같이 출력됩니다.

```text
ready
```

### 5. 컨테이너 중지 및 제거

```bash
docker compose down
```

---

## 🌐 서비스 접속 안내

| Service | URL |
|---|---|
| Prometheus | `http://localhost:9090` |
| Grafana | `https://localhost:3000` |
| Loki | `https://localhost:3100` |

운영 환경에서는 Cloudflare 및 DNS 설정에 따라 별도의 도메인을 사용할 수 있습니다.

---

# 📊 Prometheus Monitoring

## Grafana Prometheus Data Source 설정

Grafana 접속:

```text
https://localhost:3000
```

기본 계정:

```text
ID: admin
PW: admin
```

Grafana 메뉴에서 다음 경로로 이동합니다.

```text
Connections
→ Data sources
→ Add data source
→ Prometheus
```

Prometheus URL:

```text
http://prometheus:9090
```

또는:

```text
http://prometheus-knock-in:9090
```

설정 후 **Save & test**를 클릭하여 연결을 확인합니다.

---

## Spring Boot Dashboard Import

Grafana 메뉴에서 다음 경로로 이동합니다.

```text
Dashboards
→ New
→ Import
```

Dashboard ID:

```text
11378
```

`Spring Boot Statistics` Dashboard를 Import합니다.

Import 과정에서 Data Source로 기존에 생성한 `Prometheus`를 선택합니다.

---

# 📈 Grafana Monitoring Dashboard Guide

Prometheus 데이터 소스를 활용하여 KnockIn 애플리케이션의 TPS 및 응답 시간을 모니터링합니다.

## 기본 패널 생성 방법

1. Grafana Dashboard 진입
2. 우측 상단 **Edit** 클릭
3. **Add visualization** 또는 **Add panel** 선택
4. Data Source로 `Prometheus` 선택

---

## 1. 전체 TPS / RPS

전체 애플리케이션의 초당 요청 수를 모니터링합니다.

```promql
sum(
  rate(
    http_server_requests_seconds_count{
      application="KnockIn"
    }[1m]
  )
)
```

---

## 2. API별 TPS

Actuator(`/actuator.*`) 경로를 제외한 각 API Endpoint별 TPS를 확인합니다.

```promql
sum by (method, uri) (
  rate(
    http_server_requests_seconds_count{
      application="KnockIn",
      uri!~"/actuator.*"
    }[1m]
  )
)
```

### Panel 설정

```text
Legend: {{method}} {{uri}}
Graph style: Time series
```

---

## 3. API별 TPS Top 10

호출량이 가장 많은 상위 10개의 API를 확인합니다.

```promql
topk(
  10,
  sum by (method, uri) (
    rate(
      http_server_requests_seconds_count{
        uri!~"/actuator.*"
      }[1m]
    )
  )
)
```

---

## 4. API별 평균 응답 시간

각 API별 평균 응답 시간을 밀리초(ms) 단위로 확인합니다.

계산 방식:

```text
sum / count * 1000
```

PromQL:

```promql
(
  sum by (method, uri) (
    rate(
      http_server_requests_seconds_sum{
        application="KnockIn",
        uri!~"/actuator.*"
      }[1m]
    )
  )
  /
  sum by (method, uri) (
    rate(
      http_server_requests_seconds_count{
        application="KnockIn",
        uri!~"/actuator.*"
      }[1m]
    )
  )
) * 1000
```

---

# 📜 Loki Logging Monitoring

Spring Boot 애플리케이션에서 발생하는 로그는 WAS 서버의 `nohup.out` 파일에 기록됩니다.

Grafana Alloy가 해당 로그 파일을 수집하여 Loki로 전달하고, Grafana에서는 Loki Data Source를 통해 로그를 조회합니다.

### Logging Architecture

```text
Spring Boot
   ↓
nohup.out
   ↓
Grafana Alloy
   ↓
Loki
   ↓
Grafana
```

---

## Grafana Alloy 설정

WAS 서버에 설치된 Grafana Alloy는 다음과 같이 Spring Boot 로그 파일을 수집합니다.

```hcl
loki.source.file "knockin_backend" {
  targets = [
    {
      __path__ = "/home/ubuntu/app/11th-1team-BE/nohup.out",
      job      = "knockin",
      app      = "knockin-backend",
      env      = "prod",
      server   = "was-ec2",
    },
  ]

  forward_to = [
    loki.write.monitoring.receiver,
  ]
}

loki.write "monitoring" {
  endpoint {
    url = "https://<LOKI_DOMAIN>/loki/api/v1/push"
  }
}
```

로그에는 다음 Label이 추가됩니다.

```text
job="knockin"
app="knockin-backend"
env="prod"
server="was-ec2"
```

이를 통해 Grafana에서 애플리케이션, 환경, 서버별 로그를 필터링할 수 있습니다.

---

# 🔗 Grafana Loki Data Source 설정

Grafana 메뉴에서 다음 경로로 이동합니다.

```text
Connections
→ Data sources
→ Add data source
→ Loki
```

Docker 내부에서 Loki에 직접 연결하는 경우:

```text
https://loki:3100
```

외부 도메인을 사용하는 경우:

```text
https://<LOKI_DOMAIN>
```

Loki에 내부 인증서 또는 hostname과 일치하지 않는 인증서를 사용하는 경우 필요에 따라 다음 옵션을 활성화합니다.

```text
TLS
→ Skip TLS Verify
```

설정 후 **Save & test**를 클릭하여 Loki 연결을 확인합니다.

---

# 📊 Loki Dashboard Import

Grafana에서 Loki 로그를 시각화하기 위해 Logs App Dashboard를 Import합니다.

Grafana 메뉴에서 다음 경로로 이동합니다.

```text
Dashboards
→ New
→ Import
```

Dashboard ID:

```text
24866
```

Dashboard를 불러온 후 Data Source로 기존에 등록한 `Loki`를 선택하고 Import합니다.

---

## Loki Dashboard Variables

Dashboard `24866`에서는 기본적으로 다음 변수를 사용할 수 있습니다.

```text
app
level
search
```

KnockIn에서는 Alloy가 다음 `app` Label을 전송합니다.

```text
app="knockin-backend"
```

따라서 Dashboard의 `app` 항목에서 다음 값을 선택합니다.

```text
knockin-backend
```

---

# 🔎 Loki LogQL 사용 예시

## 전체 KnockIn 로그 조회

```logql
{app="knockin-backend"}
```

또는:

```logql
{job="knockin"}
```

---

## 운영 환경 로그 조회

```logql
{app="knockin-backend", env="prod"}
```

---

## 특정 서버 로그 조회

```logql
{
  app="knockin-backend",
  env="prod",
  server="was-ec2"
}
```

---

## ERROR 로그 조회

```logql
{app="knockin-backend"} |= "ERROR"
```

---

## WARN 로그 조회

```logql
{app="knockin-backend"} |= "WARN"
```

---

## Exception 로그 조회

```logql
{app="knockin-backend"} |= "Exception"
```

---

## SQL Query 로그 조회

```logql
{app="knockin-backend"} |= "org.example.knockin.query"
```

---

# 📊 최종 Monitoring 구성

```text
                    KnockIn
                       │
          ┌────────────┴────────────┐
          │                         │
       Metrics                    Logs
          │                         │
      Actuator                  nohup.out
          │                         │
     Prometheus                  Alloy
          │                         │
          │                       Loki
          │                         │
          └────────────┬────────────┘
                       │
                    Grafana
```

## Metrics Monitoring

```text
Spring Boot
→ Actuator
→ Prometheus
→ Grafana
→ Dashboard ID 11378
```

주요 모니터링 대상:

- TPS / RPS
- API별 TPS
- API 호출 Top 10
- 평균 응답 시간
- JVM / Memory
- HTTP Request Metric

---

## Logging Monitoring

```text
Spring Boot
→ nohup.out
→ Grafana Alloy
→ Loki
→ Grafana
→ Dashboard ID 24866
```

주요 모니터링 대상:

- 애플리케이션 전체 로그
- ERROR 로그
- WARN 로그
- Exception 로그
- SQL Query 로그
- 서버별 로그
- 운영 환경 로그

이를 통해 KnockIn 애플리케이션의 **성능 지표와 로그를 Grafana에서 통합 모니터링**할 수 있습니다.