# APM (Application Performance Monitoring)

Prometheus와 Grafana를 활용한 Application Performance Monitoring(APM) 구축 환경입니다.  
Spring Boot Actuator 메트릭을 수집하여 Grafana 대시보드를 통해 시각화합니다.

---

## 🛠️ 기술 스택 및 구조

- **Prometheus**: 메트릭 수집 및 저장 (`9090` 포트)
- **Grafana**: 모니터링 대시보드 시각화 (`3000` 포트, HTTPS 설정)
- **대상 애플리케이션**: `KnockIn` (`https://api.knock-in.com/actuator/prometheus`)

---

## 📋 사전 준비 사항 (Prerequisites)

Grafana에서 HTTPS 접속을 위해 SSL 인증서 파일이 필요합니다.  
프로젝트 루트 경로에 `ssl` 디렉토리를 생성하고 인증서 파일을 위치시켜 주세요.

- `ssl/cert.pem`
- `ssl/key.pem`

---

## 🚀 Docker Compose 실행 방법

### 1. 컨테이너 실행 (백그라운드)
```bash
docker compose up -d
```
*(또는 `docker-compose up -d`)*

### 2. 컨테이너 상태 확인
```bash
docker compose ps
```

### 3. 컨테이너 로그 확인
```bash
docker compose logs -f
```

### 4. 컨테이너 중지 및 제거
```bash
docker compose down
```

---

## 🌐 서비스 접속 안내

- **Prometheus**: [http://localhost:9090](http://localhost:9090)
- **Grafana**: [https://localhost:3000](https://localhost:3000)

---

## 📊 Grafana 설정 및 대시보드 Import

1. **Grafana 접속**: `https://localhost:3000` 접속 후 로그인 (기본 계정: `admin` / `admin`)
2. **Prometheus 데이터 소스 (Data Source) 추가**:
   - `Connections` -> `Data sources` -> `Add data source`
   - **Prometheus** 선택
   - URL 입력: `http://prometheus:9090` (또는 `http://prometheus-knock-in:9090`)
   - `Save & test` 클릭하여 연결 확인
3. **대시보드 Import**:
   - `Dashboards` -> `New` -> `Import` 메뉴 이동
   - **Dashboard ID**: `11378` (Spring Boot Statistics 대시보드) 입력 후 `Load` 클릭
   - Data Source로 방금 생성한 Prometheus 선택 후 `Import` 완료

## 📊 Grafana Monitoring Dashboard Guide

Prometheus 데이터 소스를 활용하여 애플리케이션(KnockIn)의 TPS 및 응답 시간을 모니터링하기 위한 패널 추가 가이드입니다.

### 🛠️ 기본 설정 방법
1. 그라파나 대시보드 진입
2. 우측 상단 **Edit** 클릭
3. **Add visualization** 또는 **Add panel** 선택
4. **Data source**: `Prometheus` 선택

---

### 1. 전체 TPS / RPS
* **설명**: 전체 애플리케이션의 초당 요청 수(TPS)를 모니터링합니다.
* **PromQL**:
  ```promql
  sum(rate(http_server_requests_seconds_count{application="KnockIn"}[1m]))
  ```

---

### 2. API별 TPS
* **설명**: 액츄에이터(`/actuator.*`) 경로를 제외한 각 API 엔드포인트별 TPS를 추적합니다.
* **PromQL**:
  ```promql
  sum by (method, uri) (
    rate(http_server_requests_seconds_count{application="KnockIn", uri!~"/actuator.*"}[1m])
  )
  ```
* **Panel 설정 팁**: 
  * **Legend**: `{{method}} {{uri}}`
  * **Graph style**: Time series

---

### 3. API별 TPS Top 10
* **설명**: 호출량이 가장 많은 상위 10개의 API를 확인합니다.
* **PromQL**:
  ```promql
  topk(10,
    sum by (method, uri) (
      rate(http_server_requests_seconds_count{uri!~"/actuator.*"}[1m])
    )
  )
  ```

---

### 4. API별 평균 응답 시간 (ms)
* **설명**: 각 API별 평균 응답 시간을 밀리초(ms) 단위로 측정합니다. (`sum / count * 1000`)
* **PromQL**:
  ```promql
  (
    sum by (method, uri) (
      rate(http_server_requests_seconds_sum{application="KnockIn", uri!~"/actuator.*"}[1m])
    )
    /
    sum by (method, uri) (
      rate(http_server_requests_seconds_count{application="KnockIn", uri!~"/actuator.*"}[1m])
    )
  ) * 1000
  ```
