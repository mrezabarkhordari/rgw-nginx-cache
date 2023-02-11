# RGW data caching for OSS

based on: https://docs.ceph.com/en/quincy/radosgw/rgw-cache/

Ansible Role to install nginx as cache server for Ceph RGW

## Role Variables
All variables which can be overridden are stored in defaults/main.yml file.

To setup nginx exporter enable it through default/main.yml
and after that yout can run nginx_exporter and prometheus using docker

## Nginx Exporter

docker run \
  -p 9113:9113 -d \
  nginx/nginx-prometheus-exporter:0.10.0 \
  -nginx.scrape-uri=http://<nginx_IP>:<nginx_exporter_port:>/metrics \
  -web.telemetry-path=/metrics

docker run \
    -p 9090:9090 -d \
    -v /etc/prometheus/prometheus.yml:/etc/prometheus/prometheus.yml \
    prom/prometheus


## Example
# Playbook
```
- name: Gather facts for RGWs
  hosts: ceph-rgw
  become: yes
  gather_facts: true

- name: Install nginx cache server
  hosts: ceph-rgw
  become: yes
  pre_tasks:
    - name: Gather RGWs hosts
      set_fact:
        oss_rgws_server_list : "{{ ( oss_rgws_server_list | default( [] ) ) + [ oss_rgws_address_template ] }}"
      with_items: '{{ groups["ceph-rgw"] }}'
      vars:
        oss_rgws_address_template: '{{ hostvars[item]["ansible_public"]["ipv4"]["address"] }}'

    - name: Gather Cache hosts
      set_fact:
        oss_cache_server_list : "{{ ( oss_cache_server_list | default( [] ) ) + [ oss_cache_address_template ] }}"
      with_items: '{{ groups["ceph-rgw"] }}'
      vars:
        oss_cache_address_template: '{{ hostvars[item]["ansible_public"]["ipv4"]["address"] }}'

  roles:
    - role: ansible-role-rgw-nginx-module

```
