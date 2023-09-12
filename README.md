# Jenkins-fastapi monitoring

![Untitled](https://github.com/k-bum/js-fastapi-monitoring/assets/96854885/1397d2b3-ed69-49e2-9301-a84181bc61d6)

1. Gitclone

```bash
git clone https://github.com/k-bum/js-fastapi-monitoring.git
```

2. FastAPI Serving API 생성 / FastAPI-Prometheus Metric 수집

```bash
docker-compose build web && docker-compose up -d
```

- [http://localhost:8000](http://localhost:8000) 접속
- [http://localhost:8000/metrics](http://localhost:8000/metrics) 접속
3. Prometheus-Grafana 연동
- prometheus.yml 을 volume 으로 참조해서 실행

```bash
docker run -d --name prom-docker -p 9090:9090 -v ${pwd}/promehteus/prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus
```

- [http://localhost:9090/targets](http://localhost:9090/targets) 접속
- Grafana 실행

```bash
docker run -d --name grafana-docker -ㅔ 3000:3000 grafana/grafana
```

- [http://localhost:3000](http://localhost:9090/targets) 접속
    - 초기 ID/password : admin/admin
    - prometheus 연결 확인
        - [Connection]-[Data sources]-[Add data source]-[Prometheus]
        - [http://docker.for.mac.localhost:8000](http://docker.for.mac.localhost:8000) url 설정
4. Locust 를 이용한 Simulation 및 Dashboard 생성

```bash
cd locust
docker build -t locust-image .
docker run -d --name locust-container -p 8089:8089 locust-image
```

- [http://localhost:8089](http://localhost:8089) 접속
    - [http://docker.for.mac.localhost:8000](http://docker.for.mac.localhost:8000) url 설정
- [http://localhost:3000](http://localhost:3000) 접속
    - [+] → [Import via panel json] 에 locust. json 파일 내용 입력 후 Load
5. Jenkins pipeline 생성
- Jenkins 시작

```bash
brew services start jenkins-lts
```

- [새로운 item] - [Pipeline : monitoring-pipeline]
- Github url 추가 및 'GitHub hook trigger for GITScm polling' 선택
    - Github webhook 생성
    - Github Repository - Settings - Webhooks - Add webhook
    - Payload url 설정 및 Content type : application/json 설정
- [train.py](http://train.py) 코드 변경 후 push
6. Jenkins monitoring
- Jenkins Plugin 에서 Prometheus 추가
    - 환경설정 - Prometheus 확인
        - Collecting metrics period in seconds : 120 → 5 변경

```bash
docker restart prom-docker
```

7. Grafana dashboard
    - [https://grafana.com/grafana/dashboards/](https://grafana.com/grafana/dashboards/) 접속
    - Jenkins 검색
    - [Jenkins: Performance and Health Overview](https://grafana.com/grafana/dashboards/9964) 선택
    - ID 복사
    - [+] - [Import dashboard] - [Import via grafana.com] - ID 입력
    - data source - prometheus 선택
