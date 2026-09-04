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
