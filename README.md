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
