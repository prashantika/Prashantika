# Monitoring - Observability Stack

This directory contains configurations for centralized monitoring and logging using Prometheus, Grafana, ELK Stack, and AWS native services.

## 📂 Structure

```
monitoring/
├── prometheus/
│   ├── prometheus.yml
│   ├── rules/
│   └── alerts.yml
├── grafana/
│   ├── dashboards/
│   ├── provisioning/
│   └── datasources/
├── elk/
│   ├── elasticsearch.yml
│   ├── logstash.conf
│   ├── kibana.yml
│   └── filebeat.yml
├── cloudwatch/
│   ├── log-groups.tf
│   └── alarms.tf
└── README.md
```

## 📊 Prometheus Configuration

### prometheus.yml

```yaml
global:
  scrape_interval: 15s
  evaluation_interval: 15s
  external_labels:
    cluster: 'production'
    environment: 'prod'

alertmanager_config:
  - static_configs:
      - targets:
          - alertmanager:9093

rule_files:
  - 'rules/*.yml'

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'kubernetes-api'
    kubernetes_sd_configs:
      - role: endpoints
    scheme: https
    tls_config:
      ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
    bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token

  - job_name: 'kubernetes-nodes'
    kubernetes_sd_configs:
      - role: node
```

### Alert Rules (rules/alerts.yml)

```yaml
groups:
  - name: kubernetes.rules
    interval: 30s
    rules:
      - alert: KubernetesNodeNotReady
        expr: kube_node_status_condition{condition="Ready",status="true"} == 0
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "Kubernetes node {{ $labels.node }} not ready"

      - alert: KubernetesPodCrashLooping
        expr: rate(kube_pod_container_status_restarts_total[5m]) > 0
        for: 5m
        labels:
          severity: warning
        annotations:
          summary: "Pod {{ $labels.pod }} crash looping"
```

## 📈 Grafana Dashboards

### Kubernetes Cluster Dashboard

```json
{
  "dashboard": {
    "title": "Kubernetes Cluster",
    "uid": "k8s-cluster",
    "tags": ["kubernetes", "cluster"],
    "timezone": "UTC",
    "panels": [
      {
        "title": "Cluster CPU Usage",
        "targets": [{"expr": "sum(rate(container_cpu_usage_seconds_total[5m])) / sum(machine_cpu_cores) * 100"}]
      },
      {
        "title": "Cluster Memory Usage",
        "targets": [{"expr": "sum(container_memory_usage_bytes) / sum(machine_memory_bytes) * 100"}]
      }
    ]
  }
}
```

## 📝 ELK Stack Configuration

### Elasticsearch

```yaml
cluster.name: production-elk
node.name: node-1
network.host: 0.0.0.0
http.port: 9200
index.number_of_shards: 3
index.number_of_replicas: 1
xpack.security.enabled: true
```

### Logstash

```conf
input {
  beats {
    port => 5000
  }
}

filter {
  if [type] == "kube-apiserver" {
    grok {
      match => { "message" => "%{TIMESTAMP_ISO8601:timestamp} %{LOGLEVEL:level}" }
    }
  }
}

output {
  elasticsearch {
    hosts => ["elasticsearch:9200"]
    index => "logs-%{+YYYY.MM.dd}"
  }
}
```

## ☁️ AWS CloudWatch Configuration

### Log Groups

```hcl
resource "aws_cloudwatch_log_group" "eks_cluster" {
  name              = "/aws/eks/cluster"
  retention_in_days = 7

  tags = {
    Name = "eks-cluster-logs"
  }
}
```

### CloudWatch Alarms

```hcl
resource "aws_cloudwatch_metric_alarm" "high_cpu" {
  alarm_name          = "high-cpu-usage"
  comparison_operator = "GreaterThanThreshold"
  evaluation_periods  = "2"
  metric_name         = "CPUUtilization"
  namespace           = "AWS/EC2"
  period              = "300"
  statistic           = "Average"
  threshold           = "80"
}
```

## 🚀 Quick Start

### Deploy with Docker Compose

```bash
docker-compose up -d
```

### Access Dashboards

- **Prometheus**: http://localhost:9090
- **Grafana**: http://localhost:3000
- **Kibana**: http://localhost:5601

## 📚 References

- [Prometheus Documentation](https://prometheus.io/docs/)
- [Grafana Documentation](https://grafana.com/docs/)
- [ELK Stack Documentation](https://www.elastic.co/guide/)
- [AWS CloudWatch Documentation](https://docs.aws.amazon.com/cloudwatch/)

---

**Note**: Add your monitoring manifests here. I'm ready to incorporate them!
