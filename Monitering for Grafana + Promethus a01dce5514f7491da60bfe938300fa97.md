# Monitering for Grafana + Promethus

## Promethus

### What is the Promethus?

Prometheus는 SoundCloud사에서 만든 오픈소스 시스템 모니터링 및 경고 툴킷이다.

- 그라파나를 통한 시각화 지원
- 많은 시스템을 모니터링할 수 있는 다양한 플러그인을 가지고 있다.
- 쿠버네티스의 메인 모니터링 시스템으로 많이 사용된다.
- 프로메테우스가 주기적으로 exporter(모니터링 대상 시스템)로부터 pulling 방식으로 메트릭을 읽어서 수집한다.

![Untitled](Monitering%20for%20Grafana%20+%20Promethus%20a01dce5514f7491da60bfe938300fa97/Untitled.png)

### How to install Promethus?

**다운로드 및 설치**

<aside>
💡 **wget https://github.com/prometheus/prometheus/releases/download/v2.31.1/prometheus-2.31.1.linux-amd64.tar.gz**

</aside>

**압축 해제**

<aside>
💡 **tar xvfz prometheus-2.31.1.linux-amd64.tar.gz**

</aside>

**디렉토리 이동**

<aside>
💡 **cd prometheus-2.31.1.linux-amd64**

</aside>

**구성 파일(prometheus.yml) 설정 (example)**

<aside>
💡

- global:
scrape_interval: 15s
    - scrape_configs:
        - job_name: 'prometheus'
        static_configs:
            - targets: ['localhost:9090']
        - job_name: 'node'
        static_configs:
            - targets: ['localhost:9100']
</aside>

서비스 시작 및 자동 시작 설정

<aside>
💡 **./prometheus --config.file=prometheus.yml**

</aside>

## **Architecture**

### **Prometheus Basic**

![Untitled](Monitering%20for%20Grafana%20+%20Promethus%20a01dce5514f7491da60bfe938300fa97/Untitled%201.png)

1. Prometheus Server 
    1. 메트릭 데이터를 스크래핑하고 저장하는 서버 
2. 스크랩할 대상 : 메트릭을 노출하는 계측된 애플리케이션이나 다른 애플리케이션의 메트릭을 노출하는 익스포터입니다.
3. **Alertmanager**: 사전에 설정된 규칙에 따라 경고를 발생시키는 컴포넌트입니다.
4. Time Series Database(시계열 데이터베이스)의 약자로, **시간에 따라 저장된 데이터** 를 의미한다. 
    
    [TSDB란 ](Monitering%20for%20Grafana%20+%20Promethus%20a01dce5514f7491da60bfe938300fa97/TSDB%E1%84%85%E1%85%A1%E1%86%AB%207597938074174a628fcf8f5e33fdb283.md)
    

 Prometheus는 시스템의 상태를 모니터링하고 문제가 발생했을 때 알림을 보낼 수 있습니다.

### Promethus  **Nomal**

![Untitled](Monitering%20for%20Grafana%20+%20Promethus%20a01dce5514f7491da60bfe938300fa97/Untitled%202.png)

구조만 보면 일반적인 모니터링 시스템과 다른걸  볼수 있다. 

[메트릭 이란? ](Monitering%20for%20Grafana%20+%20Promethus%20a01dce5514f7491da60bfe938300fa97/%E1%84%86%E1%85%A6%E1%84%90%E1%85%B3%E1%84%85%E1%85%B5%E1%86%A8%20%E1%84%8B%E1%85%B5%E1%84%85%E1%85%A1%E1%86%AB%2084ae88bf26dd4b6194399515c3a4408d.md)

일반적으로 많이 쓰인다는 nagios 와 zabbix같은 경우 모니터링 방식은 push 방식을 사용하고 있지만, Datadog, Promethus 같은 경우 pull 방식을 사용하고 있다. 특히 Promethus 같은 경우, 메트릭을 대상서버로 pull하여 중앙 서버로 가져오는 형태를 가지고 있다. 

### zabbix basic **Architecture**

![Untitled](Monitering%20for%20Grafana%20+%20Promethus%20a01dce5514f7491da60bfe938300fa97/Untitled%203.png)

### What is the pulling? Pushing?

위에 모니터링툴에서 push 방식과 pull 방식이 있다고 언급하였은데 하나씩 봐보자. 

- push 방식
    - 메트릭은 중앙에서 정의되고 할당된 에이전트에 푸시된다.
    - 수집된 데이터는 에이전트가 중앙 서버로 보낸다.
- pull 방식
    - 에이전트 자체에 메트릭 정보가 포함되어 있다.
    - 에이전트는 메트릭을 수집하며, 중앙 시스템으로 데이터 전송을 위한 정보가 보통 에이전트에 포함되어있다.
    - 수집된 데이터는 중앙서버가 에이전트에서 긁어간다.
    
    [pull 방식 vs push 방식 ](Monitering%20for%20Grafana%20+%20Promethus%20a01dce5514f7491da60bfe938300fa97/pull%20%E1%84%87%E1%85%A1%E1%86%BC%E1%84%89%E1%85%B5%E1%86%A8%20vs%20push%20%E1%84%87%E1%85%A1%E1%86%BC%E1%84%89%E1%85%B5%E1%86%A8%20228ea65929f74c48b4e23aba75f03674.md)
    

## Grafana

### what is the Grafana

Grafana란 [Grafana Labs](https://www.grafana.com/)가 개발한 [오픈소스](https://www.redhat.com/ko/topics/open-source/what-is-open-source) 인터랙티브 데이터 **시각화 플랫폼** 으로, 사용자가 하나의 대시보드(또는 여러 대시보드)로 통합된 차트와 그래프를 통해 데이터를 확인할 수 있어 데이터를 손쉽게 해석하고 이해할 수 있습니다. 

### How to install Grafana?

 **저장소 추가**

<aside>
💡 **sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"**

</aside>

**GPG 키 추가**

<aside>
💡 **wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -**

</aside>

**패키지 목록 업데이트**

<aside>
💡 **sudo apt-get update**

</aside>

**Grafana 설치**

<aside>
💡 **sudo apt-get install grafana**

</aside>

## Node Exporter

Prometheus의 에이전트로서, Linux 시스템의 하드웨어와 OS 수준의 메트릭을 수집하는 데 사용됩니다. 

이는 Prometheus가 모니터링하고 수집하는 데이터를 제공합니다. 

Node Exporter는 다양한 메트릭을 제공하여 시스템의 성능과 상태를 실시간으로 모니터링할 수 있도록 합니다.

### **Architecture**

<aside>
💡

+---------------+     Scrape Metrics      +---------------+
| Node Exporter | <---------------------> | Prometheus    |
|   (Node 1)         |                                              |    Server     |
+---------------+                                       +---------------+
            |                                              
            |                                             
+---------------+                                      +---------------+
| Node Exporter | <--------------------->  |                         |
|   (Node 2)         |                                           |                         |
+---------------+                                          |   Prometheus |
            |                                                            |       Server        |
            |                                                           |                         |
+---------------+                                          |                         |
| Node Exporter | <--------------------->  |                         |
|   (Node N)     |                                              |                         |
+---------------+                                         |                         |
                                                                       |                         |
                                                                       |                         |
                                                                       +---------------+
                                                                                   |
                                                                                   |
                                                                                   v
                                                                       +---------------+
                                                                       |    Grafana    |
                                                                       | (Visualization)|
                                                                       +---------------+

</aside>

빈 네모 박스는 Prometheus 서버를 의미합니다. 이 박스 안에 아무것도 적혀있지 않은 것은 Prometheus 서버가 여러 Node Exporter 인스턴스에서 메트릭을 스크랩하는 중앙 집중식 역할을 한다는 것을 나타냅니다.

- **Node Exporter**:
    - 각 노드(서버)에 설치되어 해당 노드의 메트릭을 수집합니다. Node Exporter는 CPU 사용량, 메모리 사용량, 디스크 I/O, 네트워크 트래픽 등과 같은 시스템 메트릭을 수집하여 Prometheus가 스크랩할 수 있도록 합니다.
- **Prometheus 서버**:
    - 여러 Node Exporter 인스턴스에서 메트릭을 정기적으로 스크랩하여 저장합니다. Prometheus 서버는 수집한 메트릭을 쿼리할 수 있으며, 이를 기반으로 알림을 설정하거나 Grafana와 같은 시각화 도구에 데이터를 제공합니다.
- **Grafana**:
    - Prometheus 서버에서 저장된 메트릭을 시각화하는 도구입니다. Grafana는 대시보드 형태로 데이터를 시각화하여 사용자가 쉽게 모니터링하고 분석할 수 있도록 합니다.

### How to install Node exporter?

Node Exporter 다운로드:

<aside>
💡 wget https://github.com/prometheus/node_exporter/releases/download/v*/node_exporter-*.*-amd64.tar.gz

</aside>

압축 해제 및 실행:

<aside>
💡 tar xvfz node_exporter-*.*-amd64.tar.gz
cd node_exporter-*.*-amd64
chmod +x node_exporter
./node_exporter

</aside>

Prometheus에서 Node Exporter의 메트릭을 스크랩하도록 설정:
   Prometheus 설정 파일(prometheus.yml)에 다음과 같이 추가합니다:

<aside>
💡 scrape_configs:
  - job_name: 'node'
    static_configs:
      - targets: ['localhost:9100']

</aside>