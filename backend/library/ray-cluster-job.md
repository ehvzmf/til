> 📅 Date: 2025-07-24

# 📌 Focus
- Ray Cluster
- Ray Job
- 간단한 개념 및 활용

<br />

# 📝 Learnings

## 1. Ray
- 대규모 분산 컴퓨팅/머신러닝 워크로드를 위한 Python 기반 프레임워크
- 클러스터 환경에서 데이터 병렬처리, 분산 ML 학습, 대규모 파이프라인 구축 등에 특화
- 대표 사용처: 분산 모델 학습, 서비스형 ML(Serving), 대규모 데이터 처리 등

---

## 2. Ray Cluster란?
- 여러 대의 노드(머신)로 구성된 분산 실행 환경
- **Head Node**와 하나 이상의 **Worker Node**로 구성
    - Head Node: 클러스터 관리, 작업 스케줄링, 상태 모니터링 담당(중앙 컨트롤러)
    - Worker Node: 실제 태스크 실행 담당
- 각각의 Node는 Ray Runtime(프로세스) 실행
- 클러스터는 로컬, 온프레미스, 클라우드(AWS/GCP/Azure) 등 다양한 환경에서 실행 가능
- 클러스터 구성 예시
    - Head Node 1대 + Worker Node n대
    - 노드 간 통신은 Ray 내부 프로토콜로 처리
- 주요 기능
    - 노드 확장/축소(autoscaling)
    - 장애 복구
    - 리소스(메모리/CPU/GPU) 기반 작업 스케줄링

---

## 3. Ray Job이란?
- Ray 클러스터에서 실행되는 "하나의 분산 작업"
- 클러스터에 등록하면, Head Node가 태스크를 Worker Node에 분산 스케줄링
- Job 단위로 시작/중지/모니터링 가능
- 대표적인 사용 예시:
    - 데이터 전처리, 분산 모델 학습, 대규모 시뮬레이션 등
- Ray Job 실행 방식
    1. 로컬/원격 클러스터에 연결
    2. Python 함수/클래스를 remote로 선언
    3. remote 객체/함수를 실행하면 Ray가 클러스터 전체에 분산 실행
    4. 결과는 future 객체(ray.get)로 수집

---

## 4. 실전 예시

### (1) Ray Cluster 시작/접속
```bash
# Head 노드에서 클러스터 시작
ray start --head --port=6379

# Worker 노드에서 클러스터에 조인
ray start --address='HEAD_NODE_IP:6379'

# (클러스터 내 노드 상태 확인)
ray status
```

### (2) Ray Job 작성 및 실행
```python
import ray

# 클러스터에 연결
ray.init(address='auto')  # 로컬이면 생략 가능

@ray.remote
def f(x):
    return x * x

# 분산 태스크 실행
futures = [f.remote(i) for i in range(4)]
results = ray.get(futures)
print(results)  # [0, 1, 4, 9]
```

### (3) Ray Job 관리 및 모니터링
- Ray Dashboard(웹 UI)로 작업/노드 상태 실시간 확인
- Job status, 태스크별 리소스 사용량, 에러 로그 등 제공
- CLI 명령어로도 job submit, status 확인 가능

---

## 5. Ray Job API 활용 (최신 방식)
- Ray 2.x 이상에서는 Ray Job Submission API를 통해 클러스터에 코드 제출 가능
```bash
ray job submit --address='http://HEAD_NODE_IP:8265' -- python my_script.py
```
- REST API, Python SDK 등 다양한 방식 지원

---

## 6. 주의점 및 실전 Tips
- Python 환경(패키지) 동기화 필요: 클러스터 모든 노드에 동일한 env 권장
- 노드 리소스(CPU/GPU 메모리 등) 충분히 고려해 태스크 설계
- Worker 노드 장애 시 자동 재시작 및 태스크 복구 지원
- 코드 구조화: remote 함수/클래스 설계, 데이터 직렬화(피클링) 이슈 주의
- 대규모 데이터 이동 최소화(노드간 통신 비용 주의)

---

## 7. 연관 개념 및 확장
- Ray Serve: 분산 모델 서빙용 프레임워크
- Ray Tune: 하이퍼파라미터 자동 탐색/최적화
- Ray Data: 대용량 데이터 파이프라인 구축
- Ray AIR: ML end-to-end pipeline 통합

---

# 🔗 References
- [Ray 공식 문서](https://docs.ray.io/en/latest/)
- [Ray Jobs API](https://docs.ray.io/en/latest/cluster/running-applications/job-submission/index.html)
- [Ray GitHub](https://github.com/ray-project/ray)
