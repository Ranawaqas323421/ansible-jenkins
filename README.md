# ansible-jenkinsAnsible + Jenkins Multi-Server Deployment Pipeline

Automated CI/CD pipeline that provisions a two-tier application infrastructure on AWS EC2 using Ansible for configuration management and Jenkins for continuous deployment. The pipeline deploys a MySQL database container on one server and an NGINX-served website on another.

🏗️ Architecture
                    ┌─────────────────┐
                    │   Jenkins CI    │
                    │  (EC2 Instance) │
                    └────────┬────────┘
                             │
                 ansible-playbook -i inventory/hosts
                             │
              ┌──────────────┴──────────────┐
              │                              │
     ┌────────▼────────┐          ┌─────────▼─────────┐
     │   dbservers      │          │    webservers      │
     │  (EC2 - Amazon)  │          │  (EC2 - Ubuntu)    │
     │                  │          │                    │
     │  Docker Engine   │          │  Docker Engine     │
     │  └─ MySQL 8.1    │          │  NGINX              │
     │     container    │          │  └─ index.html.j2   │
     └──────────────────┘          └────────────────────┘
📁 Project Structure
ansible-jenkins/
├── ansible.cfg              # Ansible configuration (inventory, key path, etc.)
├── Jenkinsfile               # CI/CD pipeline definition
├── requirements.yml          # Ansible Galaxy collections (community.docker, community.mysql)
├── site.yml                  # Main playbook — maps roles to host groups
├── inventory/
│   └── hosts                 # Server inventory (webservers, dbservers groups)
├── group_vars/
│   └── all/
│       └── vault.yml         # Shared variables (MySQL credentials, DB name)
└── roles/
    ├── mysql/
    │   └── tasks/main.yml    # Installs Docker, runs MySQL container
    ├── docker/
    │   └── tasks/main.yml    # Installs Docker Engine on web servers
    └── website/
        ├── tasks/main.yml    # Installs NGINX, deploys web page
        └── templates/
            └── index.html.j2 # Jinja2 templated landing page
⚙️ Tech Stack
Component	Purpose
Ansible	Configuration management & orchestration
Jenkins	CI/CD pipeline automation
Docker	Containerizing MySQL and app services
NGINX	Serving the static web page
Jinja2	Templating dynamic values into HTML
AWS EC2	Hosting infrastructure
GitHub	Source control for playbooks and roles
🚀 How It Works
Checkout — Jenkins pulls the latest playbook code from GitHub.
Install Collections — ansible-galaxy collection install -r requirements.yml installs required Ansible collections (community.docker, community.mysql).
Run Playbook — ansible-playbook -i inventory/hosts site.yml executes two plays:
dbservers: installs Docker, launches a MySQL 8.1 container with persistent volume storage.
webservers: installs Docker + NGINX, deploys a Jinja2-templated landing page showing live DB connection info.
🔑 Prerequisites
Two (or more) AWS EC2 instances reachable via SSH
A private key (.pem) with access to all target hosts
Jenkins server with the following plugins:
Git
Pipeline
Credentials Binding
Ansible collections listed in requirements.yml
📋 Setup Instructions
1. Configure inventory

Edit inventory/hosts:

ini
[webservers]
server1 ansible_host=<WEB_SERVER_IP> ansible_user=ubuntu

[dbservers]
server2 ansible_host=<DB_SERVER_IP> ansible_user=ec2-user
2. Set variables

Edit group_vars/all/vault.yml:

yaml
mysql_root_pass: "your_root_password"
mysql_user: "appuser"
mysql_password: "your_app_password"
mysql_db: "myappdb"
3. Add SSH key to Jenkins

Manage Jenkins → Credentials → System → Global → Add Credentials

Kind: SSH Username with private key (or Secret file)
ID: used in Jenkinsfile's credentialsId
4. Run the pipeline

Trigger the Jenkins job manually, or configure a webhook/poll trigger on the GitHub repo.

🖥️ Manual Run (without Jenkins)
bash
ansible-playbook -i inventory/hosts site.yml --private-key rana_waqas.pem
✅ Verifying Deployment

Check MySQL container:

bash
ssh -i rana_waqas.pem ec2-user@<DB_SERVER_IP>
docker ps
docker logs myapp-mysql

Check website:

http://<WEB_SERVER_IP>
👤 Author

Waqas Saleem — DevOps Engineer

GitHub: Ranawaqas323421
Docker Hub: waqas323421
Email: rw178722@gmail.com
