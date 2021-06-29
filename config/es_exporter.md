## Source: https://github.com/justwatchcom/elasticsearch_exporter

1. Pull binaries
```
wget https://github.com/justwatchcom/elasticsearch_exporter/releases/download/v1.1.0/elasticsearch_exporter-1.1.0.linux-amd64.tar.gz
tar xvf elasticsearch_exporter-1.1.0.linux-amd64.tar.gz
cd elasticsearch_exporter-1.1.0.linux-amd64
cp elasticsearch_exporter /usr/local/bin/es_exporter
```

2. SYSTEMD SERVICE: `vim /etc/systemd/system/es_exporter.service`
```
[Unit]
Description=Prometheus ES_exporter
After=local-fs.target network-online.target network.target
Wants=local-fs.target network-online.target network.target

[Service]
User=root
Nice=10
ExecStart = /usr/local/bin/es_exporter --es.uri=http://elastic_user:XXXXXXXXXXX@localhost:9200 --es.all --es.indices --es.timeout 20s
ExecStop= /usr/bin/killall es_exporter

[Install]
WantedBy=default.target
```

Enable on boot and start it
```
systemctl daemon-reload
systemctl start es_exporter.service
systemctl enable es_exporter.service
```

3. Prometheus config: `vim /etc/prometheus/prometheus.yml`
```
  - job_name: elasticsearch
    scrape_interval: 60s
    scrape_timeout:  30s
    metrics_path: "/metrics"
    static_configs:
    - targets: ['es-01:9114' ]
      labels:
        service: elasticsearch
```
