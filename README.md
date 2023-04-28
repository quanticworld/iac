# Minimal ansible configuration for security and monitoring

The playbook requires to have a working user (with public key declared in ~/.ssh/authorized_keys on each managed host)

Included:

- firewalld :lock:
- fail2ban :lock:
- rkhunter :lock:
- lynis :lock:
- Linux Malware Detect (LMD) :lock:
- prometheus :stethoscope:
- node-exporter :stethoscope:

The monitoring part is based on node_exporter, prometheus, grafana stack.
Grafana is not included in the playbook, as managed hosts only provide node_exporter/prometheus data exposition.

# Configuration & installation :rocket:

0. Install ansible on your control node (i.e. your local laptop) 

1. Set a file to use ansible-vault secret file : `export ANSIBLE_VAULT_PASSWORD_FILE="~/.ansible-vault-password-file"`

2. Configure the managed hosts inventory in `./inventory.yml`.
You should cipher hosts ips with ansible-vault if your repository is public : `ansible-vault encrypt_string "xx.xx.xx.xx" --name "ip"`

3. Configure group_vars variables `alerting_email`, `ansible_ssh_pk`, `prometheus_basic_auth_password`, etc.

4. For each host, generate and replace `.crt` certificates and `.key` private keys for node_exporter / prometheus :
`openssl req -new -newkey rsa:2048 -days 10000 -nodes -x509 -keyout yourhost.key -out yourhost.crt -subj "/C=ZA/ST=CT/L=SA/O=VPN/CN=hostname.domain.com" -addext "subjectAltName = IP:xx.xx.xx.xx"`

(replace values : `yourhost`, `hostname.domain.com` and `xx.xx.xx.xx`)

5. Execute the playbook : `ansible-playbook -i inventory.yaml secure_vm_playbook.yaml -u youruser` (replace `youruser`)