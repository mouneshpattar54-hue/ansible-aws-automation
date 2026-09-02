# Ansible AWS Automation Project

## Overview

This project demonstrates the setup and configuration of Ansible automation using AWS EC2 instances.

The environment consists of one Ansible Controller and two Ansible Managed Nodes running Ubuntu 24.04.

## Architecture

Ansible Controller
        |
        | SSH
        |
   +----+----+
   |         |
Node 1     Node 2

### Infrastructure

- Ansible Controller
- Ansible Node 1
- Ansible Node 2
- AWS EC2
- Ubuntu 24.04 LTS
- SSH key-based authentication
- Ansible Core 2.21.3
- Python 3.12

---

## 1. Install Ansible

Update the package repository:

    sudo apt update

Install Ansible:

    sudo apt install ansible -y

Verify the installation:

    ansible --version

Example:

    ansible [core 2.21.3]
    python version = 3.12.3

---

## 2. Create Ansible User

Create the user:

    sudo useradd devopsadmin -s /bin/bash -m -d /home/devopsadmin

Switch to the user:

    su - devopsadmin

---

## 3. Generate SSH Key

Generate an ECDSA SSH key:

    ssh-keygen -t ecdsa -b 521

The keys are stored in:

    /home/devopsadmin/.ssh/

Files generated:

    id_ecdsa
    id_ecdsa.pub

The public key is used for passwordless SSH authentication between the Ansible Controller and managed nodes.

---

## 4. Configure SSH on Managed Nodes

On each managed node:

    mkdir ~/.ssh

Create the authorized keys file:

    vi ~/.ssh/authorized_keys

Copy the public key from the Ansible Controller:

    cat ~/.ssh/id_ecdsa.pub

Paste the public key into:

    ~/.ssh/authorized_keys

Set permissions:

    chmod 700 ~/.ssh
    chmod 600 ~/.ssh/authorized_keys

Test SSH connectivity:

    ssh ansibleadmin@<NODE_IP>

---

## 5. Ansible Configuration

Ansible configuration is maintained under:

    /etc/ansible/

Important files:

    /etc/ansible/ansible.cfg
    /etc/ansible/hosts
    /etc/ansible/roles/

Example ansible.cfg:

    [defaults]
    inventory = inventory/hosts
    host_key_checking = False
    interpreter_python = auto_silent

---

## 6. Ansible Inventory

The inventory defines the managed nodes.

Example:

    [testnodes]
    samplenode1 ansible_host=<NODE1_PRIVATE_IP> ansible_user=ansibleadmin
    samplenode2 ansible_host=<NODE2_PRIVATE_IP> ansible_user=ansibleadmin

    [devservers]
    devnode1 ansible_host=<NODE1_PRIVATE_IP> ansible_user=ansibleadmin
    devnode2 ansible_host=<NODE2_PRIVATE_IP> ansible_user=ansibleadmin

Replace the placeholder IP addresses with the private IP addresses of the EC2 instances.

---

## 7. Test Ansible Connectivity

Test all hosts:

    ansible all -m ping

Expected result:

    samplenode1 | SUCCESS => {
        "changed": false,
        "ping": "pong"
    }

    samplenode2 | SUCCESS => {
        "changed": false,
        "ping": "pong"
    }

This confirms that the Ansible Controller can successfully communicate with the managed nodes.

---

## 8. Test Specific Inventory Group

Test the testnodes group:

    ansible testnodes -m ping

Test the devservers group:

    ansible devservers -m ping

---

## 9. Python Interpreter Discovery

Ansible automatically discovers Python on the managed nodes.

Example:

    "discovered_interpreter_python": "/usr/bin/python3.12"

The Python interpreter warning displayed during the setup is related to interpreter discovery.

The important result is:

    "ping": "pong"

which confirms successful Ansible connectivity.

---

## 10. Ansible Ad-Hoc Commands

Check system uptime:

    ansible all -m shell -a "uptime"

Check disk usage:

    ansible all -m shell -a "df -h"

Check memory:

    ansible all -m shell -a "free -m"

Check hostname:

    ansible all -m shell -a "hostname"

---

## 11. Ansible Playbook

Example ping playbook:

    ---
    - name: Test Ansible Connectivity
      hosts: all
      gather_facts: false

      tasks:
        - name: Ping managed nodes
          ansible.builtin.ping:

Run the playbook:

    ansible-playbook ping.yml

Expected result:

    PLAY RECAP

    samplenode1    ok=1    changed=0    failed=0
    samplenode2    ok=1    changed=0    failed=0

---

## 12. Project Structure

    ansible-aws-automation/
    |
    ├── README.md
    ├── ansible.cfg
    ├── .gitignore
    |
    ├── inventory/
    │   └── hosts
    |
    ├── playbooks/
    │   └── ping.yml
    |
    └── roles/

---

## 13. Security

Private SSH keys and sensitive credentials should never be committed to GitHub.

Files such as:

    id_ecdsa
    id_rsa
    *.pem
    *.key
    .env
    authorized_keys

should be excluded from Git.

---

## 14. Skills Demonstrated

- AWS EC2
- Linux
- Ubuntu
- Ansible
- Ansible Inventory
- Ansible Ad-Hoc Commands
- Ansible Playbooks
- SSH
- SSH Key Authentication
- Python Interpreter Discovery
- Configuration Management
- Infrastructure Automation
- Git and GitHub

---

## 15. Future Improvements

The project can be extended with:

- Nginx installation using Ansible
- Apache installation
- Application deployment
- Ansible Roles
- Jinja2 Templates
- Ansible Vault
- Jenkins CI/CD integration
- Docker deployment
- Kubernetes deployment
- AWS infrastructure automation

---

## Conclusion

This project demonstrates how Ansible can be used to centrally manage multiple AWS EC2 instances through SSH.

The successful `ansible -m ping` execution confirms that the Controller and managed nodes are correctly configured and communicating.

The project provides a foundation for implementing real-world DevOps automation and configuration management.
