# OneTwo 쿠버네티스 매니페스트

이 레포지토리는 OneTwo 서비스의 쿠버네티스 배포 매니페스트 파일을 포함하고 있습니다. 개발 환경(dev)에서 사용되는 쿠버네티스 리소스 정의를 제공합니다.

## 구조

```
.
├── README.md
└── dev/
    ├── ingress.yaml                    # 메인 API 인그레스 설정
    ├── monitoring-ingress.yaml         # 모니터링 도구 인그레스 설정
    ├── otel-collector-config.yaml      # OpenTelemetry 수집기 설정
    ├── api-docs-homepage/              # API 문서 홈페이지 서비스
    ├── config-service/                 # 설정 서비스
    ├── data-service/                   # 데이터 서비스
    ├── event-service/                  # 이벤트 서비스
    └── monitoring/                     # 모니터링 스택
        ├── grafana/                    # Grafana 설정
        ├── loki/                       # Loki 로그 수집기
        ├── prometheus/                 # Prometheus 메트릭 수집기
        └── tempo/                      # Tempo 분산 추적 시스템
```

## 서비스 구성

### 마이크로서비스

- **data-service**: 데이터 관리 서비스
- **event-service**: 이벤트 처리 서비스
- **config-service**: 설정 관리 서비스
- **api-docs-homepage**: API 문서 홈페이지

### 모니터링 스택

- **Prometheus**: 메트릭 수집 및 저장
- **Grafana**: 대시보드 및 시각화
- **Loki**: 로그 집계
- **Tempo**: 분산 추적
- **OpenTelemetry**: 애플리케이션 계측 및 텔레메트리 데이터 수집

## 배포 방법

이 매니페스트 파일들은 쿠버네티스 클러스터에 다음과 같이 적용할 수 있습니다:

```bash
# 네임스페이스 생성 (필요한 경우)
kubectl create namespace monitoring

# 모니터링 스택 배포
kubectl apply -f dev/monitoring/

# 서비스 배포
kubectl apply -f dev/config-service/
kubectl apply -f dev/data-service/
kubectl apply -f dev/event-service/
kubectl apply -f dev/api-docs-homepage/

# OpenTelemetry 설정 적용
kubectl apply -f dev/otel-collector-config.yaml

# 인그레스 설정 적용
kubectl apply -f dev/ingress.yaml
kubectl apply -f dev/monitoring-ingress.yaml
```

## 요구사항

- Kubernetes 1.24 이상
- Nginx Ingress Controller
- 쿠버네티스 클러스터에 설정된 이미지 풀 시크릿 (onetwo-gcp-docker-secret)

## 참고사항

- 이 매니페스트는 개발 환경(dev)용으로 설계되었습니다.
- 모든 서비스는 OpenTelemetry를 통한 관측성을 갖추고 있습니다.
- 각 서비스는 적절한 리소스 제한과 프로브 설정이 되어 있습니다.
