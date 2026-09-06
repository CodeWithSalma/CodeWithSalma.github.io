# Local DevOps HomeLab

<!-- [![Ansible](https://img.shields.io/badge/Ansible-Automation-red)]()
[![Docker](https://img.shields.io/badge/Docker-Container-blue)]()
[![Status](https://img.shields.io/badge/license-portfolio-blue)]() -->

A local DevOps homelab that provisions and configures multi-host infrastructure using **Vagrant** and **Ansible**, deploying a containerized web server (Nginx via Docker) and a database server (MariaDB) without relying on any cloud provider.

---

## The Problem

Learning real-world infrastructure automation typically requires cloud resources, which adds cost and masks a lot of the underlying provisioning and configuration mechanics:

- Cloud sandboxes cost money and expire
- Managed services hide the manual steps of server setup
- Hard to safely experiment with destructive changes (re-provisioning, failure recovery) on shared/cloud infra

## The Solution

This homelab reproduces a real infrastructure workflow entirely on local virtual machines. Vagrant provisions the VMs, and Ansible playbooks configure them into two roles — a **web** host (Docker + Nginx) and a **db** host (MariaDB) — giving hands-on practice with Infrastructure-as-Code, multi-host orchestration, and Linux system administration in an isolated, disposable environment.

---

## Technology Stack

| Technology | Purpose |
|---|---|
| Vagrant | Local VM definition and provisioning |
| Ansible | Configuration management and IaC automation |
| Docker | Container runtime for the web service |
| Nginx (container) | Serves static web content |
| MariaDB | Database server on the `db` host |
| Ubuntu 20.04 (`generic/ubuntu2004`) | Guest OS for both VMs |

> **Note:** the project's reference material and title also mention Kubernetes and a monitoring dashboard. The current repository only contains the Vagrant/Ansible/Docker provisioning layer (web + db hosts) — no Kubernetes manifests or dashboard configuration are present in the codebase yet. See [Future Enhancements](#future-enhancements).

---

## Architecture

Two VMs are provisioned locally and configured independently through separate Ansible task blocks in the same playbook run.

```
                     Vagrant
                        │
          ┌─────────────┴─────────────┐
          ▼                           ▼
      VM: web                      VM: db
   (private network)           (private network)
          │                           │
   Ansible: hosts=web          Ansible: hosts=db
          │                           │
   Install Docker CE            Fix DNS / apt config
   Copy index.html                    │
   Run Nginx container          Install MariaDB
   (port 80 → host 8080)        Start MariaDB service
```

### Host Roles

| Host | Role | Key Tasks |
|---|---|---|
| `web` | Web server | Install Docker CE, copy `index.html`, run an Nginx container mounting the content directory |
| `db` | Database server | Fix DNS/apt config, install and start MariaDB |

### Inventory & Access

- Ansible inventory defines the `web` and `db` groups with private IPs and SSH key paths.
- The `inventory` file and `Vagrantfile` are excluded from version control (see [Security & Configuration Practices](#security-configuration-practices)) since they contain environment-specific IPs and key paths.

### Playbook Variants

| File | Behavior |
|---|---|
| `playbook.yml` | Full provisioning: installs Docker, deploys Nginx on `web`, installs and starts MariaDB on `db` |
| `playbook_update.yml` | Lightweight update pass: only re-copies `index.html` and restarts the Nginx container on `web`; the `db` block and Docker install steps are commented out |

---

## Key Capabilities

| Capability | Description |
|---|---|
| Local IaC Provisioning | VMs defined and provisioned via Vagrant, no cloud dependency |
| Multi-host Configuration | Single Ansible run targets distinct `web` and `db` host groups |
| Containerized Web Serving | Nginx runs in Docker, serving content mounted from the host VM |
| Idempotent Updates | Separate lightweight playbook for redeploying content without full re-provisioning |
| DNS & Package Repair Tasks | Playbook includes fixes for common apt/DNS issues on lab VMs |
| Secrets Isolation | Sensitive inventory and VM definitions kept out of version control |

---

## Security & Configuration Practices

Since this repository is public, sensitive local configuration is deliberately excluded and replaced with masked examples in the README.

**Excluded via `.gitignore`:**

- `inventory` — contains private IPs and SSH private key paths
- `Vagrantfile` — contains local network/provider-specific VM config
- `.vagrant/` — local Vagrant state directory
- `*.pem`, `*.key`, `*.env`, `*.private`, `*.ssh` — private keys and credentials
- `*.retry` — Ansible retry files

**Masked example — Vagrantfile:**

```ruby
Vagrant.configure("2") do |config|
  config.vm.box = "generic/ubuntu2004"

  config.vm.define "web" do |web|
    web.vm.network "private_network", ip: "192.168.xxx.xxx"
    web.vm.network "forwarded_port", guest: 80, host: 8080
  end

  config.vm.define "db" do |db|
    db.vm.network "private_network", ip: "192.168.xxx.xxx"
  end
end
```

**Masked example — inventory:**

```ini
[web]
192.168.xxx.xxx ansible_user=vagrant ansible_ssh_private_key_file=~/.ssh/vagrant_web

[db]
192.168.xxx.xxx ansible_user=vagrant ansible_ssh_private_key_file=~/.ssh/vagrant_db
```

If a sensitive file is accidentally committed, it should be removed from tracking with `git rm --cached <file>` and replaced with a masked `.example` version.

---

## Setup & Configuration

### Prerequisites

- Vagrant
- A hypervisor (e.g. Hyper-V, VirtualBox)
- Ansible
- SSH client

### Files You Need Locally (not in the repo)

- `Vagrantfile` — define the `web` and `db` VMs (see masked example above)
- `inventory` — list host IPs and SSH key paths for `web` and `db`

### Running the Project

1. Clone the repository
2. Create your own `Vagrantfile` and `inventory` based on the masked examples
3. Bring up the VMs: `vagrant up`
4. Run the full provisioning playbook:
   ```bash
   ansible-playbook -i inventory playbook.yml
   ```
5. For a lightweight content-only update instead:
   ```bash
   ansible-playbook -i inventory playbook_update.yml
   ```
6. Access the web server at the forwarded port (e.g. `http://localhost:8080`)

---

## Deployment

Deployment here means re-running Ansible against the provisioned VMs rather than a CI/CD pipeline:

- `playbook.yml` is used for the initial full setup of both hosts
- `playbook_update.yml` is used for subsequent content or config updates on the `web` host only, avoiding a full Docker reinstall
- Both are triggered manually via `ansible-playbook`, targeting hosts defined in `inventory`

---

## Troubleshooting

**Docker GPG key / repository step fails**

- Confirm internet connectivity from inside the VM
- Re-run the playbook — the tasks use `creates:` guards so they're safe to repeat

**MariaDB install fails with an apt hash sum mismatch**

- This is usually caused by stale package indices or a flaky mirror behind NAT
- The playbook already includes a fix: disabling apt HTTP pipelining and clearing the apt cache before reinstalling

**Nginx container not serving updated content**

- The playbook removes and recreates the `webapp` container on every run (`docker rm -f webapp`) — confirm the play actually reached that task
- Verify `index.html` was copied to `/var/www/html` on the VM

**SSH connection refused / inventory errors**

- Confirm the private IP and SSH key path in your local `inventory` file match the running VM
- Check `host_key_checking = False` is set in `ansible.cfg` (already configured) to avoid host-key prompts on fresh VMs

---

## Learning Outcomes

- Hands-on Infrastructure-as-Code workflow using Vagrant + Ansible
- Multi-host configuration management (web vs. db roles)
- Container lifecycle management with Docker
- Linux system administration (DNS, apt package management, service management)
- Practical experience separating sensitive config from version-controlled code

---

## Future Enhancements

- Kubernetes orchestration for multi-service workloads (referenced in the project scope, not yet implemented in this repo)
- Service health monitoring and a deployment reporting dashboard
- Migrating the manual `ansible-playbook` trigger into a CI/CD pipeline
- Adding automated tests for playbook idempotency

---

*This project is intended for educational and portfolio purposes.*