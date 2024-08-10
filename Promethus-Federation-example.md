# Large-scale server in Promethus

## 일반적인 구조

## Large - Scale - up / out

다중 스케일업과 스케일 아웃에 대해서는 일반적인 구조가 아닌 **Prometheus Federation** 형태로 구성해야 한다.  

해당 **Prometheus Federation  형태로 모니터링하는것이 아닌 하나의 Prometheus에 다중 node exporter 설치후 수집하게 되면 아래와 같은 문제가 있다.** 

[단일 promethus에 다중 node exporter 설치를 한다면 문제점 ](https://www.notion.so/promethus-node-exporter-fe7f21706a3a44768d96badca1bdbd9d?pvs=21)

### What is the Prometheus Federation

Prometheus Federation을 사용하여 여러 Prometheus 서버를 계층 구조로 구성할 수 있습니다. 이는 특히 수백 대의 서버를 모니터링할 때 유용합니다.

- **기본 구조**: 여러 개의 Prometheus 서버가 각기 다른 서버 그룹을 모니터링하고, 상위 레벨의 Prometheus 서버가 하위 레벨의 Prometheus 서버로부터 메트릭을 수집합니다.
- **장점**: 스케일 아웃이 가능하며, 각 Prometheus 서버의 부하를 분산할 수 있습니다

### How to setting Prometheus Federation

참고 : https://prometheus.io/docs/prometheus/latest/federation/#configuring-federation

메인 Promethus : 114.207.112.***

서브 Promethus & Node exporter 1 : 180.71.58.**

서브 Promethus & Node exporter 2 : 43.201.56.***

메인 Promethus.yml 

```jsx
global:
  scrape_interval: 15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets:
          # - alertmanager:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.

scrape_configs:
  - job_name: 'federate'
    honor_labels: true
    metrics_path: '/federate'
    params:
      'match[]':
        - '{job="node"}'
        - '{__name__=~"job:.*"}'
    static_configs:
      - targets:
        - '180.71.58.10:9091'   # 하위 서버 1의 Prometheus
        - '43.201.56.103:9090'   # 하위 서버 2의 Prometheus
```

서브 Promethus & Node exporter 1 :  180.71.58.**

```jsx
# my global config
global:
  scrape_interval: 15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).
  external_labels:
    instance: '180.71.58.**'

# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets:
          # - alertmanager:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9999"]

  - job_name: 'node'
    static_configs:
      - targets: ['localhost:9100']
```

서브 Promethus & Node exporter 2 : 43.201.56.***

```jsx
# my global config
global:
  scrape_interval: 15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).
  external_labels:
    instance: '43.201.56.***'

# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets:
          # - alertmanager:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: "prometheus"

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.
    static_configs:
      - targets: ["localhost:9090"]
  - job_name: 'node'
    static_configs:
      - targets: ["localhost:9100"]
```
