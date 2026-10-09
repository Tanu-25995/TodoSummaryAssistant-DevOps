# Monitoring and Operations Guide

## 1. Monitoring Tools

* **Prometheus:** Collects application and infrastructure metrics.
* **Grafana:** Displays metrics in dashboards and supports alerting.
* **Docker:** Provides container status and application logs.
* **Amazon CloudWatch:** Can be used for AWS infrastructure monitoring.

## 2. Application Metrics

The Spring Boot backend exposes Actuator endpoints for health, metrics, and Prometheus.

* Health endpoint: `/actuator/health`
* Metrics endpoint: `/actuator/metrics`
* Prometheus endpoint: `/actuator/prometheus`

## 3. Container Operations

List containers:

`sudo docker ps -a`

View backend logs:

`sudo docker logs --tail 50 todo-backend`

View frontend logs:

`sudo docker logs --tail 50 todo-frontend`

Check disk space:

`df -h`

## 4. Dashboard Recommendations

Configure Grafana dashboards to monitor:

* CPU and memory utilization.
* Disk usage.
* Container availability and restarts.
* HTTP request rates and errors.
* Application response latency.

## 5. Alerting

Configure at least two meaningful alerts, such as:

1. Backend target unavailable in Prometheus.
2. High CPU utilization on the EC2 instance.

Set appropriate thresholds and verify alerts before relying on them operationally.

## 6. Operational Checks

* Verify the frontend and backend after deployment.
* Check database connectivity.
* Review application logs after failures.
* Confirm Prometheus targets are healthy.
* Verify Grafana dashboards and alert delivery.

## 7. Current Limitations

Prometheus and Grafana were initially configured locally. Their deployment and alert configuration on EC2 still need verification.
