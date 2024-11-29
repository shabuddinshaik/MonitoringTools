# AWS Cost Exporter Helm Chart

The **AWS Cost Exporter** Helm chart allows you to deploy the `aws-cost-exporter` application on Kubernetes. This application retrieves cost and usage data from AWS Cost Explorer and exposes it as Prometheus metrics for monitoring and analysis.

---

## Features

- **Cost Data Collection**: Retrieves cost and usage data from AWS Cost Explorer.
- **Prometheus Metrics**: Exposes cost data as Prometheus metrics for visualization and alerting.
- **Flexible Grouping**: Group cost data by dimensions (e.g., AWS service, region) or tags.
- **Multi-Account Support**: Collect cost data from multiple AWS accounts.
- **Role-Based Access**: Supports AWS IAM roles for secure access.

---

## Prerequisites

1. Kubernetes cluster (v1.20 or higher recommended).
2. Helm 3.x installed on your local system.
3. AWS credentials with permissions to access Cost Explorer and the associated accounts.
4. Prometheus installed for metric collection.


---
Reference: https://github.com/electrolux-oss/aws-cost-exporter/releases/tag/v1.0.7

Image: https://hub.docker.com/layers/opensourceelectrolux/aws-cost-exporter/v1.0.7/images/sha256-56a34a03aa090bea21c79ea0e5a48a6f07f3e239eb66480ce24991e78e9c1166?context=explore


---

Update your Prometheus configuration to scrape the AWS Cost Exporter endpoint. Example snippet for prometheus.yml

scrape_configs:
  - job_name: 'aws-cost-exporter'
    scrape_interval: 1h
    static_configs:
      - targets: ['aws-cost-exporter.mon.svc.cluster.local:80']



---

## Installation

### **1. Clone the Repository**

```bash
git clone <repository-url>
cd aws-cost-exporter




