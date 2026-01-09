🖥️ Ansible Project to Monitor VM Health

Author: Priyansh Saini
GitHub Repo: Ansible-VM-Monitor

📌 Project Overview

This project demonstrates end-to-end infrastructure automation using Ansible to monitor the health of multiple EC2 virtual machines.
It uses dynamic inventory, AWS EC2 tags, Ansible playbooks, and email reporting to collect and send system metrics automatically.

🎯 Project Goal

Set up Ansible Master–Worker architecture

Manage multiple EC2 instances at scale

Use dynamic inventory to fetch public IPs automatically

Collect system health metrics from worker nodes

Send automated email reports

Learn modular Ansible playbooks & variable management

🧰 Tech Stack Used

Ansible

AWS EC2

AWS CLI

Dynamic Inventory (amazon.aws.aws_ec2)

Python (boto3, botocore)

Shell Scripting

Linux

Gmail SMTP

🏗️ Architecture

1 Ansible Master Node

10 Worker Nodes (EC2)

Workers tagged using Environment=dev

Dynamic inventory groups instances using AWS tags

🚀 Step-by-Step Implementation
1️⃣ Launch EC2 Instances

Instance Type: t3.medium

Storage: 20 GB

Create 1 master and 10 worker nodes

Tag workers with:

Environment=dev

2️⃣ Install Ansible on Master Node
sudo apt update && sudo apt upgrade -y
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible -y

3️⃣ Install AWS CLI
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt install unzip -y
unzip awscliv2.zip
sudo ./aws/install

4️⃣ Configure AWS CLI
aws configure


Provide:

Access Key

Secret Key

Region

Output format

5️⃣ Rename Worker Nodes Sequentially
#!/bin/bash

instance_ids=$(aws ec2 describe-instances \
  --filters "Name=tag:Environment,Values=dev" "Name=instance-state-name,Values=running" \
  --query 'Reservations[*].Instances[*].InstanceId' \
  --output text)

sorted_ids=($(echo "$instance_ids" | tr '\t' '\n' | sort))

counter=1
for id in "${sorted_ids[@]}"; do
  name="web-$(printf "%02d" $counter)"
  aws ec2 create-tags --resources "$id" --tags Key=Name,Value="$name"
  ((counter++))
done

6️⃣ Create SSH Key for Master → Worker Communication
ssh-keygen

7️⃣ Dynamic Inventory Configuration

📁 inventory/aws_ec2.yaml

plugin: amazon.aws.aws_ec2
regions:
  - eu-north-1
filters:
  tag:Environment: dev
  instance-state-name: running
compose:
  ansible_host: public_ip_address
keyed_groups:
  - key: tags.Name
    prefix: name
  - key: tags.Environment
    prefix: env

8️⃣ Create Python Virtual Environment
sudo apt install python3-venv -y
python3 -m venv ansible-env
source ansible-env/bin/activate

9️⃣ Install Required Python Packages
pip install boto3 botocore docker

🔟 Install AWS Ansible Collection
ansible-galaxy collection install amazon.aws


Verify inventory:

ansible-inventory -i inventory/aws_ec2.yaml --graph

1️⃣1️⃣ Inject SSH Keys into Worker Nodes
#!/bin/bash

PEM_FILE="DevOps-Shack.pem"
PUB_KEY=$(cat ~/.ssh/id_rsa.pub)
USER="ubuntu"
INVENTORY_FILE="inventory/aws_ec2.yaml"

HOSTS=$(ansible-inventory -i $INVENTORY_FILE --list | jq -r '._meta.hostvars | keys[]')

for HOST in $HOSTS; do
  ssh -o StrictHostKeyChecking=no -i $PEM_FILE $USER@$HOST "
    mkdir -p ~/.ssh &&
    echo \"$PUB_KEY\" >> ~/.ssh/authorized_keys &&
    chmod 700 ~/.ssh &&
    chmod 600 ~/.ssh/authorized_keys
  "
done

1️⃣2️⃣ Ansible Configuration

📄 ansible.cfg

[defaults]
inventory = ./inventory/aws_ec2.yaml
host_key_checking = False

[ssh_connection]
ssh_args = -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null

📁 Project Structure
Ansible-VM-Monitor/
│
├── inventory/
│   └── aws_ec2.yaml
│
├── vars/
│   └── variables.yaml
│
├── playbooks/
│   ├── collect_metrics.yaml
│   ├── send_email.yaml
│   └── main.yaml
│
├── ansible.cfg
├── copy_ssh_key.sh
└── README.md

📊 Workflow

Gather system facts from worker nodes

Install required monitoring packages

Collect CPU, memory, disk metrics

Generate formatted health report

Send report via email automatically

📬 Final Output

✅ Health metrics collected
✅ Tasks executed successfully
✅ Automated email report received

🏁 Conclusion

This project demonstrates:

Scalable Ansible automation

Dynamic inventory using AWS EC2

Secure SSH key-based access

Modular Ansible playbooks

Real-world DevOps monitoring workflow
