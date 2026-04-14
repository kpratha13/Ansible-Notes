# Ansible – Configuration Management Quick Reference

## 🛠️ Configuration Management Tools

| Tool | Vendor | Notes |
|---|---|---|
| **Ansible** | Red Hat | Agentless, SSH-based |
| **Chef** | Progress | Agent-based, Ruby DSL |
| **Puppet** | Puppet Inc. | Agent-based, declarative |
| **SaltStack** | VMware | Agent or agentless |

---

## 🔵 Ansible – Core Concepts

| Feature | Detail |
|---|---|
| **Origin** | Open Source (Red Hat) |
| **Language** | Python |
| **Architecture** | Agentless |
| **Communication** | SSH Mechanism |
| **Method** | Push Mechanism |
| **Extensibility** | Module Support |

---

## 🧩 Ansible Components

| Component | Description |
|---|---|
| **Ad-hoc** | One-liner commands for quick tasks |
| **Playbooks** | YAML files defining automation workflows |
| **Roles** | Reusable, structured sets of playbooks |
| **Tower** | GUI / enterprise management layer (AWX) |
| **Vault** | Encrypted storage for secrets & passwords |

---

## ⚙️ Setup Guide

### 🖥️ Master Node Setup

```bash
# Step 1 – Generate SSH key pair
ssh-keygen
# Public key location:
# /root/.ssh/id_rsa.pub

# Step 2 – Install EPEL repo and Ansible
sudo amazon-linux-extras install epel
yum install ansible pip -y

# Step 3 – Verify installation
ansible --version

# Step 4 – Test SSH connection to agent
ssh root@<agentPublicIP>
```

---

### 🤖 Agent Node Setup

```bash
# Step 1 – Generate SSH key on agent (optional)
ssh-keygen

# Step 2 – Copy Master's public key to Agent's authorized_keys
# On MASTER, run:
ssh-copy-id root@<agentPublicIP>

# OR manually append master's public key:
# Master:  /root/.ssh/id_rsa.pub
# Agent:   /root/.ssh/authorized_keys
```

---

## 📁 Ansible Configuration Files

### Main Configuration File
```bash
vi /etc/ansible/ansible.cfg
```

Uncomment these two lines:
```ini
inventory   = /etc/ansible/hosts
sudo_user   = root
```

---

### Inventory File
```bash
vi /etc/ansible/hosts
```

```ini
[agents]
[agents]
agent ansible_host=54.159.58.173 ansible_user=root

[master]
master ansible_host=54.166.248.125 ansible_user=root
```

> Add all agent IPs under a group name (e.g., `[agents]`). You can define multiple groups.

---

## ⚡ Ad-hoc Commands

Ad-hoc commands run one-off tasks directly from the command line without a playbook.

### Syntax
```bash
ansible <group> -m <module> -a "<arguments>"
```

### Examples

| Task | Command |
|---|---|
| **Ping all agents** | `ansible agents -m ping` |
| **Install a package** | `ansible agents -m yum -a "name=git state=present"` |
| **Copy a file** | `ansible agents -m copy -a "src=/opt/abc.txt dest=/opt/abc.txt"` |
| **Remove a package** | `ansible agents -m yum -a "name=httpd state=absent"` |
| **Start a service** | `ansible agents -m service -a "name=httpd state=started"` |
| **Stop a service** | `ansible agents -m service -a "name=httpd state=stopped"` |

---

## 📦 Common Modules Reference

| Module | Purpose |
|---|---|
| `ping` | Test connectivity to agents |
| `yum` | Install / remove packages (RHEL/CentOS) |
| `apt` | Install / remove packages (Ubuntu/Debian) |
| `copy` | Copy files from master to agent |
| `service` | Start, stop, restart system services |
| `shell` | Run shell commands on agents |
| `file` | Manage files and directories |
| `user` | Manage user accounts |

---

## 🔄 How Ansible Works (Push Model)

```
Master Node
    │
    │  SSH (Push)
    ▼
Agent Nodes
  ├── Agent 1 (IP: x.x.x.x)
  ├── Agent 2 (IP: x.x.x.x)
  └── Agent N (IP: x.x.x.x)
```

1. Master generates SSH keys and copies the **public key** to each agent's `authorized_keys`
2. Master **pushes** commands/playbooks over SSH — no agent software needed on nodes
3. Ansible modules execute on the agent and return results to master

---

## 📂 Key File Locations

| File | Path | Purpose |
|---|---|---|
| Main config | `/etc/ansible/ansible.cfg` | Global Ansible settings |
| Inventory | `/etc/ansible/hosts` | List of managed nodes |
| SSH Private Key | `/root/.ssh/id_rsa` | Master's private key |
| SSH Public Key | `/root/.ssh/id_rsa.pub` | Copy this to agents |
| Agent Auth Keys | `/root/.ssh/authorized_keys` | Paste master's public key here |
