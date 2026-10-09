# AWS Setup Guide

## 1. Deployment Overview

The Todo Summary Assistant application is deployed on an AWS EC2 instance running Ubuntu Server 24.04 LTS.

## 2. EC2 Configuration

* **Service:** Amazon EC2
* **Operating System:** Ubuntu Server 24.04 LTS
* **Instance Type:** t3.small
* **Region:** Asia Pacific (Mumbai) — ap-south-1
* **Frontend Port:** 3000
* **Backend Port:** 8080

## 3. Docker Deployment

Docker and Docker Compose are installed on the EC2 instance. The frontend, backend, and MySQL database run in containers.

## 4. Security Groups

The EC2 security group controls inbound network access.

* SSH (22): Restricted to authorized access.
* Frontend (3000): Allows browser access to the application.
* Backend (8080): Used for API testing; access should be restricted when no longer needed.

## 5. IAM Role

An IAM role named `TodoSummaryAssistant-SSM-Role` is attached to the EC2 instance. It includes AmazonSSMManagedInstanceCore and CloudWatchAgentServerPolicy.

## 6. Database

The current deployment uses a MySQL 8.0 container on EC2. A private Amazon RDS for MySQL instance is a remaining assessment requirement.

## 7. Remaining Tasks

* Configure private Amazon RDS for MySQL.
* Restrict security-group rules to the minimum required access.
* Configure automated Jenkins deployment and health checks.
* Deploy and verify Prometheus and Grafana monitoring.
* Configure monitoring alerts and document rollback procedures.

