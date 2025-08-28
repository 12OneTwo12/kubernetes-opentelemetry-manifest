# Redis Cluster - Development Environment

## 개요
개발 환경용 Redis Cluster 구성. ClusterIP를 사용하여 Kubernetes 클러스터 내부에서만 접속 가능.

## 특징
- **내부 접속 전용**: ClusterIP 사용 (외부 접속 불가)
- **최소 리소스**: 개발 환경에 최적화된 리소스 설정
- **클러스터 구성**: 3 Masters + 3 Replicas (각 마스터당 1개)
- **인증**: 비활성화 (비밀번호 없음)

## 배포

### 1. Helm Repository 추가
```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
```

### 2. Namespace 생성
```bash
kubectl apply -f namespace.yaml
```

### 3. Redis Cluster 설치
```bash
helm install redis-cluster bitnami/redis-cluster \
  --namespace redis-cluster \
  --values redis-values.yaml \
  --wait
```

### 4. 배포 확인
```bash
kubectl get pods -n redis-cluster
kubectl get svc -n redis-cluster
```

## 접속 방법

### Kubernetes 내부에서 접속

#### 1. Service DNS로 접속 (다른 Pod에서)
```yaml
# 애플리케이션 설정 예시
redis:
  host: redis-cluster.redis-cluster.svc.cluster.local
  port: 6379
  cluster: true
```

#### 2. 직접 Service 이름 사용
```bash
# 같은 namespace 내에서
redis-cli -c -h redis-cluster -p 6379

# 다른 namespace에서
redis-cli -c -h redis-cluster.redis-cluster.svc.cluster.local -p 6379
```

#### 3. Port Forward (로컬 테스트용)
```bash
# 로컬 머신에서 테스트할 때만 사용
kubectl port-forward -n redis-cluster svc/redis-cluster 6379:6379

# 다른 터미널에서
redis-cli -c -h localhost -p 6379
```

## 클러스터 상태 확인
```bash
# Pod 내부에서 실행
kubectl exec -it redis-cluster-0 -n redis-cluster -- redis-cli cluster info
kubectl exec -it redis-cluster-0 -n redis-cluster -- redis-cli cluster nodes
```

## 주요 설정
- **서비스 타입**: ClusterIP (내부 전용)
- **마스터**: 3개
- **레플리카**: 마스터당 1개
- **리소스**: 128-256Mi RAM, 50-250m CPU
- **스토리지**: 4Gi per node
- **네트워크 정책**: 내부 트래픽만 허용

## 삭제
```bash
helm uninstall redis-cluster -n redis-cluster
kubectl delete namespace redis-cluster
```