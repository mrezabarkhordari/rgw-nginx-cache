# RGW data caching for OSS

based on: https://docs.ceph.com/en/quincy/radosgw/rgw-cache/


To setup nginx exporter enable it through default/main.yml
and after that need to run nginx_exporter and prometheus using docker

### Nginx Exporter

docker run \
  -p 9113:9113 -d \
  nginx/nginx-prometheus-exporter:0.10.0 \
  -nginx.scrape-uri=http://<nginx_IP>:<nginx_exporter_port:>/metrics \
  -web.telemetry-path=/metrics

docker run \
    -p 9090:9090 -d \
    -v /etc/prometheus/prometheus.yml:/etc/prometheus/prometheus.yml \
    prom/prometheus

