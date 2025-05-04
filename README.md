
# Docker Container Monitoring Stack on AWS (Terraform + Docker + Prometheus + Grafana)

## 📦 Overview

This project sets up a **Docker-based monitoring stack** on AWS EC2 using **Terraform** for infrastructure provisioning, **Prometheus** for collecting metrics, **Grafana** for visualizing data, and **cAdvisor** & **Node Exporter** for gathering metrics from Docker containers and the host system.

Additionally, an **optional Apache installation** can be deployed using **Ansible**.

## 🧱 Architecture

The stack consists of the following components:

- **Prometheus**: Collects and stores metrics data from **cAdvisor** (Docker container metrics) and **Node Exporter** (host metrics).
- **Grafana**: Visualizes the collected data from **Prometheus** with pre-configured dashboards.
- **cAdvisor**: Monitors Docker containers, collecting metrics like CPU, memory, network, and disk usage.
- **Node Exporter**: Monitors the host (EC2 instance), collecting CPU, memory, disk I/O, and network metrics.
- **Apache (optional)**: Can be installed on the EC2 instance using the `install_apache.yml` Ansible playbook.

## 📁 Project Structure

```
monitoring-stack/
├── terraform/
│   ├── main.tf
│   ├── variables.tf
│   └── outputs.tf
├── docker-compose.yml
├── prometheus.yml
├── install_apache.yml
└── README.md
```

- **terraform/**: Contains Terraform configuration files for AWS infrastructure.
- **docker-compose.yml**: Defines Prometheus, Grafana, cAdvisor, and Node Exporter Docker containers.
- **prometheus.yml**: Prometheus scrape configuration file.
- **install_apache.yml**: Ansible playbook to install Apache (optional).
- **README.md**: Documentation for setting up and using the stack.

## 🚀 Prerequisites

1. **Terraform**: Install Terraform on your local machine.
   - [Download Terraform](https://www.terraform.io/downloads.html)
2. **AWS CLI**: Install and configure AWS CLI with appropriate access rights.
   - [Install AWS CLI](https://aws.amazon.com/cli/)
3. **Docker**: Install Docker and Docker Compose.
   - [Install Docker](https://docs.docker.com/get-docker/)
   - [Install Docker Compose](https://docs.docker.com/compose/install/)
4. **Ansible (Optional)**: For running the `install_apache.yml` playbook.
   - [Install Ansible](https://docs.ansible.com/ansible/latest/installation_guide/index.html)

## 🔧 Installation & Configuration

### 1. Clone the Repository

```bash
git clone https://github.com/yourusername/monitoring-stack.git
cd monitoring-stack
```

### 2. Set Up AWS Credentials

Ensure you have the AWS CLI configured with your access keys:

```bash
aws configure
```

### 3. Provision Infrastructure Using Terraform

Navigate to the **terraform/** directory and run the following commands to create the EC2 instance and networking resources:

```bash
cd terraform
terraform init
terraform apply
```

- Terraform will output the public IP of the EC2 instance. Make a note of it for SSH access and Grafana.

### 4. SSH into the EC2 Instance

Use the output IP address from Terraform to SSH into the EC2 instance:

```bash
ssh -i ~/.ssh/your-key.pem ubuntu@<EC2_PUBLIC_IP>
```

### 5. Deploy the Monitoring Stack with Docker Compose

Once logged into the EC2 instance, navigate to the root of the project directory and run:

```bash
sudo docker-compose up -d
```

This will start the following containers:
- **Prometheus** (metrics collection)
- **Grafana** (metrics visualization)
- **cAdvisor** (Docker container metrics)
- **Node Exporter** (host metrics)

### 6. (Optional) Install Apache using Ansible

If you wish to install Apache on the EC2 instance, run the Ansible playbook:

```bash
ansible-playbook install_apache.yml -i <EC2_PUBLIC_IP>,
```

Ensure you have SSH access and Ansible installed on your local machine before running this command.

## 📊 Access Grafana and Import Dashboards

1. **Access Grafana**: Open your browser and visit `http://<EC2_PUBLIC_IP>:3000`.
   - **Username**: `admin`
   - **Password**: `admin`

2. **Add Prometheus as a Data Source**:
   - Navigate to ⚙️ → **Data Sources**
   - Add **Prometheus**
   - Set the **URL** to `http://prometheus:9090` (for Docker) or `http://<EC2_PUBLIC_IP>:9090` (external access).

3. **Import Pre-configured Dashboards**:
   - Go to ➕ → **Import**
   - Enter the following Dashboard IDs one at a time, then click **Load**:
     - **1860**: **Node Exporter Full** – Linux system metrics (CPU, memory, disk, etc.)
     - **193**: **Docker Container Stats** – Real-time Docker container metrics.
     - **179**: **Docker + Host Metrics** – A complete overview of Docker containers and host resources.
     - **893**: **Docker Metrics Compact** – Lightweight container stats overview.

4. Select **Prometheus** as the data source when prompted.

## 🧑‍💻 Troubleshooting & Tips

- **Grafana Login Issues**: If you can't log in to Grafana with the default credentials (`admin/admin`), try resetting the password from the container.
  - Example:
    ```bash
    sudo docker exec -it grafana-container-name bash
    grafana-cli reset-admin-password newpassword
    ```

- **Prometheus Not Scraping Metrics**: Ensure that Prometheus has the correct scraping targets defined in `prometheus.yml` (check that the cAdvisor and Node Exporter targets are reachable).

- **Docker Containers Not Starting**: Check the Docker Compose logs for errors:
  ```bash
  sudo docker-compose logs
  ```

## 🎯 Conclusion

This monitoring stack allows you to monitor your **Docker containers** and **AWS EC2 instance** (host) in real time. Prometheus collects metrics from **Node Exporter** (host-level) and **cAdvisor** (container-level), while Grafana visualizes these metrics with customizable dashboards. 

You can easily scale and extend the monitoring stack by adding more containers or monitoring services.

