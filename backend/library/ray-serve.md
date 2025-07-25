> 📅 Date: 2025-07-25

# 📌 Focus
- Ray Serve: 분산 서비스/모델 서빙 프레임워크 개념 및 실전 활용

<br />

# 📝 Learnings

## 1. Ray Serve란?
- Ray 프레임워크의 서비스/모델 서빙 컴포넌트
- 대규모 머신러닝 모델, 함수, API 엔드포인트 등을 분산 환경에서 효율적으로 배포·운영·스케일링
- 실시간, 고가용성, 오토스케일링, 버전 관리, 트래픽 분배 등 고급 기능 제공

---

## 2. Ray Serve 구조 및 동작 방식
- Ray Cluster(Head/Worker 노드) 위에서 동작
- Python 함수, 클래스, ML 모델 등 어떤 객체든 HTTP/gRPC API로 노출 가능
- FastAPI, Flask 등 웹 프레임워크와 연동 가능
- 오토스케일링, 배치 처리, 트래픽 동적 라우팅 등 내장 지원

---

## 3. 기본 사용법 및 코드 예시

### (1) 서비스 배포 (기본)
```python
import ray
from ray import serve

ray.init()
serve.start()

@serve.deployment
def hello(request):
    return "Hello Ray Serve!"

hello.deploy()
```
- 위 예시는 `/hello` 엔드포인트에 HTTP 요청 시 응답

### (2) FastAPI와 연동
```python
from fastapi import FastAPI
from ray import serve

app = FastAPI()

@serve.deployment
@serve.ingress(app)
class MyFastAPI:
    @app.get("/ping")
    def ping(self):
        return {"msg": "pong"}

MyFastAPI.deploy()
```
- FastAPI 서버를 Ray Serve 위에서 분산 서빙

---

## 4. 주요 기능
- **오토스케일링:** 요청량에 따라 인스턴스 자동 증감
- **동적 라우팅:** 트래픽을 여러 버전/엔드포인트에 동적으로 분배
- **배치 처리:** 여러 요청을 묶어서 효율적으로 처리
- **모델 버전 관리:** 여러 모델 버전 관리 및 롤백, A/B 테스트 지원
- **실시간 모니터링:** Ray Dashboard에서 상태 확인, 트래픽 모니터링

---

## 5. 실전 활용 예시
- MLOps 시스템에서 모델 서빙, API 게이트웨이, 모델 실험/배포 자동화
- 대규모 API 요청 처리, 비동기 함수 실행, 실시간 inference 파이프라인 구축
- 모델 버전별 트래픽 분배, 동적 실험(A/B 테스팅) 환경 구축

---

## 6. 실전 Tips & 주의사항
- 클러스터 및 Python 환경 세팅 중요(모든 노드에 동일한 환경 권장)
- 네트워크/포트 접근 권한 사전 구성 필요
- 상태 관리(무상태/유상태) 설계 신중히
- 서비스 구조 및 엔드포인트 설계 시 트래픽 분배, 확장성 고려

---

## 7. 연관 개념 및 확장
- Ray Cluster: 분산 실행 환경
- Ray Job: 개별 분산 작업 실행
- Ray Tune, Ray Data, Ray AIR: 하이퍼파라미터 탐색, 데이터 파이프라인, ML pipeline 등 확장 모듈

---

# 🔗 References
- [Ray Serve 공식 문서](https://docs.ray.io/en/latest/serve/index.html)
- [Ray Serve 튜토리얼](https://docs.ray.io/en/latest/serve/getting_started.html)
