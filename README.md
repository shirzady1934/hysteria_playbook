
# 📡 Hysteria Ansible Deployment

This repository provides an automated Ansible-based setup for deploying [Hysteria](https://github.com/apernet/hysteria) as a secure UDP tunneling solution with TLS and obfuscation support.

It handles:

- Installing Hysteria binaries
- Setting up systemd services:
  - `hysteriac.service` for the server
  - `hysterias.service` for the client
- Configuring TLS certificates
- Managing client-server TCP forwarding configuration

---

## 📁 Inventory Structure

Your `inventory/hosts.ini` should define server and client nodes like this:

```ini
[hysteria_server]
server1 ansible_host=<server_ip_or_dns>

[hysteria_client]
client1 ansible_host=<client_ip_or_dns>
````

You can add `ansible_user`, `ansible_ssh_pass`, or other SSH connection details if needed.

---

## ⚙️ Configuration

### 🔒 Global Variables (`group_vars/all.yml`)

Define these variables shared between server and client:

```yaml
hysteria_domain: sub1.example.com
hysteria_auth: Password
hysteria_obfs_password: obfs_password
```

These control the domain used in the TLS SNI, authentication password, and obfuscation settings.

---

### 🔀 Port Forwarding (`group_vars/hysteria_client.yml`)

To change the TCP port that the client forwards to the server:

```yaml
hysteria_forward_port: 8080
```

---

## 🚀 Running the Playbook

Once everything is configured, run the playbook using:

```bash
ansible-playbook -i inventory/hosts.ini site.yaml
```

This will:

* Copy the `hysteria` binary to `/usr/local/bin/hysteria`
* Copy Let's Encrypt certs from `roles/hysteria/files/letsencrypt/` to `/etc/letsencrypt` on the remote
* Generate config files:

  * `/etc/hysteria/server.yaml` for servers
  * `/etc/hysteria/client.yaml` for clients
* Create and enable systemd services:

  * `hysteriac.service` on servers
  * `hysterias.service` on clients

---

## 📌 Notes

* Ensure the `hysteria` binary in `roles/hysteria/files/` is compiled and executable.
* Place your Let's Encrypt certs under `roles/hysteria/files/letsencrypt/live/<domain>/`.
* The server and client configurations are generated dynamically based on your group variables.
