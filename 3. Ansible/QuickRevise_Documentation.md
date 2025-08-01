# Ansible Documentation
### Ways of Writing Ansible Files
- Their are 8 ways
- mostly use 
    - **Playbooks** and **Inventory files** -> always
    - **Roles** -> for Large Projects

### 1. Ad-Hoc Commands
- One-line commands executed directly in the terminal without creating any YAML files.
- Useful for quick tasks or testing connectivity.
- Example:
```
ansible all -m ping
ansible web -a "yum install httpd -y"
ansible db -m shell -a "systemctl restart mysqld"
```
### 2. Inventory Files
- Define which servers Ansible will manage.
- Can be:
    - Static Inventory: INI or YAML format with fixed hostnames/IPs.
    - Dynamic Inventory: Scripts or plugins that fetch hosts dynamically (e.g. from AWS).
- Example Static Inventory (INI):
```
[web]
web1.example.com
web2.example.com

[db]
db1.example.com
```
🔹 Example Static Inventory (YAML):
```
all:
  hosts:
    web1.example.com:
    web2.example.com:
  children:
    db:
      hosts:
        db1.example.com:
```
### 3. Playbooks
- The main automation scripts written in YAML.
- Define what tasks to perform on which hosts in a declarative way.
- Basic Structure:
```
- name: Install Apache Web Server
  hosts: web
  become: yes

  tasks:
    - name: Install httpd package
      yum:
        name: httpd
        state: present

    - name: Start httpd service
      service:
        name: httpd
        state: started
        enabled: yes
```
### 4. Roles
- A structured and reusable way to organize playbooks and tasks.
- Follows a specific directory structure, making automation scalable and modular.
- Directory Structure Example:
```
roles/
└── <role-name>/                 # Role directory (e.g. webserver)
    ├── tasks/                   # Contains the main list of tasks to be executed
    │   └── main.yml             # Main tasks file (.yml extension)
    ├── handlers/                # Contains handlers, which are triggered by tasks
    │   └── main.yml             # Main handlers file (.yml extension)
    ├── files/                   # Contains files to be copied to hosts
    │   └── <file>               # e.g. index.html, script.sh (any file type)
    ├── templates/               # Contains Jinja2 templates for configuration files
    │   └── <template>.j2        # e.g. httpd.conf.j2 (.j2 extension for templates)
    ├── vars/                    # Contains variables for the role
    │   └── main.yml             # Main vars file (.yml extension)
    ├── defaults/                # Default variables for the role (lowest priority)
    │   └── main.yml             # Main defaults file (.yml extension)
    ├── meta/                    # Defines role dependencies and metadata
    │   └── main.yml             # Main meta file (.yml extension)
    └── README.md                # Documentation for the role (.md extension)

```
🔹 Usage in Playbook:
```
- hosts: web
  roles:
    - webserver
```
### 5. Collections
- Bundles of roles, plugins, modules, and more.
- Allow sharing of automation content via Ansible Galaxy or private repositories.
- Example usage:
```
- hosts: all
  roles:
    - my_namespace.my_collection.my_role
```
- Why use collections?
- To package and distribute reusable automation at scale.
### 6. Templates (Jinja2)
- Files with dynamic content, written with Jinja2 templating syntax.
- Rendered during playbook execution to generate configuration files with variables.
- Example (index.html.j2):
```
<html>
  <head><title>{{ server_name }}</title></head>
  <body>
    <h1>Welcome to {{ server_name }}</h1>
  </body>
</html>
```
🔹 Using template module in playbook:
```
- name: Deploy custom index page
  template:
    src: index.html.j2
    dest: /var/www/html/index.html
```
### 7. Variable Files
- YAML files storing variables separately for clean playbooks.
- Example (vars.yml):
```
server_name: "Production Web Server"
app_port: 8080
```
🔹 Including in playbook:
```
- hosts: web
  vars_files:
    - vars.yml
  tasks:
    - name: Show server name
      debug:
        msg: "{{ server_name }}"
```
### 8. Ansible Configuration File
- ansible.cfg file that configures default settings (inventory path, remote user, roles path, etc.).
- Example:
```
[defaults]
inventory = ./hosts
remote_user = ec2-user
roles_path = ./roles
host_key_checking = False
```
---
## 📂 Ansible Files – Structure, Extension & Usage

| **#** | **Way**                       | **File Structure / Location**                                                                                                                                                                              | **File Extension / Type**                                              | **Purpose / Usage**                                      | **Usage Level** |
| ----- | ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------- | -------------------------------------------------------- | --------------- |
| **1** | **Ad-hoc Commands**           | Not stored as files (executed in CLI)                                                                                                                                                                      | *No file*                                                              | Quick, one-time commands directly in terminal            | ⭐⭐⭐ (Quick tasks, troubleshooting) |
| **2** | **Inventory Files**           | Anywhere (commonly named `hosts` or `inventory`)                                                                                                                                                           | `.ini` or `.yaml` / `.yml`                                             | Defines managed hosts and groups                         | ⭐⭐⭐⭐⭐ (Always used) |
| **3** | **Playbooks**                 | Project root or playbooks/ directory                                                                                                                                                                       | `.yml` or `.yaml`                                                      | Main automation scripts containing tasks, vars, handlers | ⭐⭐⭐⭐⭐ (Always used) |
| **4** | **Roles**                     | `roles/role_name/` with standard subfolders: <br>• `tasks/main.yml` <br>• `handlers/main.yml` <br>• `templates/*.j2` <br>• `files/*` <br>• `vars/main.yml` <br>• `defaults/main.yml` <br>• `meta/main.yml` | `.yml` for Ansible files <br>`.j2` for templates <br>*Any* for files   | Modular, reusable automation components                  | ⭐⭐⭐⭐⭐ (Large projects) |
| **5** | **Collections**               | Under `collections/` directory with namespace and collection name: <br> `collections/ansible_collections/my_namespace/my_collection/`                                                                      | Multiple: <br>• `.yml` for roles/tasks <br>• `.py` for plugins/modules | Packaged automation bundles for sharing                  | ⭐⭐ (Enterprise, advanced) |
| **6** | **Templates (Jinja2)**        | Within `templates/` folder in role or playbook directory                                                                                                                                                   | `.j2`                                                                  | Dynamic files rendered with variables                    | ⭐⭐⭐⭐ (Dynamic configurations) |
| **7** | **Variable Files**            | Separate vars file, often in `vars/` directory or within roles                                                                                                                                             | `.yml` or `.yaml`                                                      | Stores variables separately for clarity and reuse        | ⭐⭐⭐⭐ (Commonly used) |
| **8** | **Config File (ansible.cfg)** | Project root or `/etc/ansible/ansible.cfg`                                                                                                                                                                 | `ansible.cfg` (INI format)                                             | Configures default Ansible settings                      | ⭐⭐ (One-time setup, important) |

---

## ✨ Ansible File Structure (All 8 Ways)
```
ansible-project/
├── inventory
│   └── hosts                  # (Way 2) Inventory file
├── ad-hoc-commands.txt        # (Way 1) Notes of ad-hoc commands (executed in CLI, no file)
├── playbooks/
│   ├── simple-playbook.yml    # (Way 3) Simple playbook
│   └── multiple-plays.yml     # (Way 4) Playbook with multiple plays
├── roles/
│   └── webserver/             # (Way 5) Roles structure
│       ├── tasks/
│       │   └── main.yml
│       ├── handlers/
│       │   └── main.yml
│       ├── templates/
│       ├── files/
│       ├── vars/
│       │   └── main.yml
│       └── defaults/
│           └── main.yml
├── tasks/
│   └── common-tasks.yml       # (Way 6) Task list file to include/import in playbook
├── handlers/
│   └── restart-services.yml   # (Way 7) Handlers file to include/import in playbook
├── vars/
│   └── common-vars.yml        # (Way 8) Variables file to include/import in playbook
├── group_vars/
│   └── all.yml                # Group variables
├── host_vars/
│   └── host1.yml              # Host-specific variables
└── ansible.cfg                # Project configuration file

```
## Installation
---
## Commands
### 📋 **1. Ansible Commands Summary**

| **#** | **Command Type** | **Command** | **Use** | **Notes / Additional Info** |
|---|---|---|---|---|
| 1 | Ad-hoc | `ansible all -m ping` | Check connectivity to all hosts | Uses `ping` module |
| 2 | Ad-hoc | `ansible web -a "uptime"` | Check uptime on web group | Uses default `command` module |
| 3 | Ad-hoc | `ansible web -m yum -a "name=httpd state=present" -b` | Install httpd | Uses `yum` module with sudo |
| 4 | Ad-hoc | `ansible all -m copy -a "src=/tmp/file.txt dest=/tmp/file.txt"` | Copy file to all hosts | Uses `copy` module |
| 5 | Ad-hoc | `ansible web -m service -a "name=httpd state=restarted" -b` | Restart service | Uses `service` module |
| 6 | Playbook Run | `ansible-playbook -i inventory.ini playbook.yml` | Run playbook with inventory | Standard playbook run |
| 7 | Playbook Run | `ansible-playbook playbook.yml --syntax-check` | Check playbook syntax | Validates YAML and modules |
| 8 | Inventory | `ansible-inventory -i inventory.ini --list` | List inventory hosts | Outputs in JSON |
| 9 | Inventory | `ansible all -m setup` | Gather facts | Uses `setup` module |
| 10 | Modules | `ansible all -m shell -a "echo Hello"` | Run shell command | Uses `shell` module |
| 11 | Modules | `ansible all -m command -a "ls -l"` | Run command | Uses `command` module |
| 12 | Galaxy | `ansible-galaxy install role_name` | Install role | From Ansible Galaxy |
| 13 | Galaxy | `ansible-galaxy collection install namespace.collection` ```ansible-galaxy init <apache_webserver>```| Install collection | From Galaxy |
| 14 | PowerShell | `.\generate_inventory.ps1` | Run PowerShell script | For dynamic inventory (Windows) |
| 15 | PowerShell | `type .\inventory.ini` | View file content | Equivalent to `cat` in Linux |
| 16 | Linux | `cat inventory.ini` | View file content | Linux command |


### 📋 **2. Module Categories and Commonly Used Modules**

| **#** | **Category** | **Common Modules** | **Example Usage** | **Purpose** |
|---|---|---|---|---|
| 1 | **Command Execution** | `command`, `shell`, `raw`, `script` | `ansible all -m shell -a "echo Hello"` | Run commands/scripts on hosts |
| 2 | **Package Management** | `yum`, `apt`, `package`, `pip`, `npm` | `ansible web -m yum -a "name=httpd state=latest"` | Install/remove packages |
| 3 | **Service Management** | `service`, `systemd` | `ansible db -m systemd -a "name=mysql state=restarted"` | Start, stop, enable services |
| 4 | **File Management** | `copy`, `file`, `template`, `fetch`, `unarchive` | `ansible all -m copy -a "src=localfile dest=/tmp/"` | Manage files/directories |
| 5 | **User & Group Management** | `user`, `group` | `ansible all -m user -a "name=testuser state=present"` | Create/manage users and groups |
| 6 | **Networking** | `firewalld`, `ufw`, `iptables`, `community.network.nmcli` | `ansible all -m firewalld -a "service=http permanent=true state=enabled"` | Configure firewall, network settings |
| 7 | **Cloud Modules** | `ec2`, `gcp_compute_instance`, `azure_rm_vm` | `ansible-playbook aws_create_instance.yml` | Manage cloud resources |
| 8 | **Database Modules** | `mysql_db`, `postgresql_db` | `ansible db -m mysql_db -a "name=test state=present"` | Manage databases |
| 9 | **Utilities** | `debug`, `pause`, `set_fact`, `include_tasks`, `import_tasks` | `ansible-playbook playbook.yml --tags debug` | Control playbook execution flow |
| 10 | **Facts Gathering** | `setup` | `ansible all -m setup` | Collect host facts |

---

### How Ansible connects to existing infrastructure
- **You may already have:**
   - AWS EC2 instances
   - Linux virtual machines
   - On-premise servers

Ansible connects to these remote machines using SSH and then runs tasks you define.
### 🔌 How Ansible Connects to Servers
#### 🔑 Step 1: SSH Connection
- **Ansible connects to each server using:**
  - The server’s IP address or DNS name
  - A username
  - An SSH key or password

- **🛠️ This is just like when you do:**
```
ssh -i my-key.pem ec2-user@1.2.3.4
```

**But Ansible does this automatically in the background.**
#### 📋 Step 2: Inventory File
You tell Ansible which servers to connect to using an inventory file.

This file lists all your servers like:
```
[webservers]
192.168.1.10
server1.example.com
```
**You can group servers (like [webservers], [databases]) for better management.**
#### 🗝️ Step 3: Authentication
- Ansible needs permission to access each server. You can provide:
  - SSH private key (.pem or .id_rsa)
  - Or a password (not recommended)
#### ✅ Step 4: Test the Connection
Once inventory is ready and SSH access works, test with:
```
ansible all -m ping -i inventory.ini
```
**It will send a "ping" command and check if servers respond.**
#### 🛠️ Once Connected, What Can Ansible Do?
- Install software
- Start/stop services
- Update configurations
- Create users
- Deploy code

**All this using simple YAML scripts called playbooks.**

**🔄 For Cloud Infrastructure (like AWS)?**

You can also make Ansible fetch the server list automatically using dynamic inventory plugins like:
- ``aws_ec2`` for AWS
- ``gcp_compute`` for GCP

No need to manually update the inventory when servers change.

### When you're managing infrastructure using Terraform, and you want to connect it with Ansible, the two tools can work together like this:
### 🧩 Terraform + Ansible: What’s the Relation?
- **Terraform:** Creates infrastructure (like EC2, VPCs, etc.)
- **Ansible:** Configures software/services on that infrastructure (like installing Apache, setting up users, etc.)

### 🔄 How to Connect Ansible to Terraform Infra?
Here’s the easy flow:

#### ✅ 1. Use Terraform to Create Infra (e.g., EC2)
Terraform file (example):
```
resource "aws_instance" "web" {
  ami           = "ami-0abcd1234abcd1234"
  instance_type = "t2.micro"
  key_name      = "my-key"
  
  tags = {
    Name = "WebServer"
  }
}
```
After running:
```
terraform apply
```
Terraform will create an EC2 instance.
#### ✅ 2. Output Instance IP Address
Tell Terraform to output the public IP of your EC2:
```
output "web_public_ip" {
  value = aws_instance.web.public_ip
}
```
After apply, you’ll see:
```
Outputs:

web_public_ip = "13.233.123.45"
```
#### ✅ 3. Generate Ansible Inventory from Terraform
Now, use Terraform output to create an Ansible inventory file.

**Option 1: Manual inventory file**
```
[web]
13.233.123.45 ansible_user=ec2-user ansible_ssh_private_key_file=~/.ssh/my-key.pem
```
**Option 2: Dynamic inventory using Terraform output + script (recommended for automation)**

Example Bash script:
```
terraform output -json > tf_outputs.json

jq -r '.web_public_ip.value' tf_outputs.json > inventory.ini

# Then you can append ansible info to each line using sed/awk
```
#### ✅ 4. Run Ansible on Terraform-created EC2
Test connection:
```
ansible -i inventory.ini web -m ping
```
Run a playbook:
```
ansible-playbook -i inventory.ini setup.yml
```
#### 🧠 Bonus: Full Automation – Terraform Calls Ansible
You can make Terraform call Ansible automatically after infra is created using ``null_resource`` and ``provisioner "local-exec"``:
| Step | Tool              | What it Does                       |
| ---- | ----------------- | ---------------------------------- |
| 1    | Terraform         | Creates EC2/server infra           |
| 2    | Terraform Output  | Gets server IP                     |
| 3    | Ansible Inventory | Uses IP to connect via SSH         |
| 4    | Ansible           | Runs playbooks to configure server |

---
### Write ansible file
#### ✅ 1. Playbook File (.yml)
- What It Does:
  - Defines what Ansible should do (e.g., install packages, configure services).

- File Example: ``webserver.yml``
```
---
- name: Install Apache on Ubuntu servers
  hosts: web
  become: true

  tasks:
    - name: Install Apache
      apt:
        name: apache2
        state: present
        update_cache: yes

    - name: Start Apache
      service:
        name: apache2
        state: started
        enabled: true
```
- Key Parts:
| Keyword  | Purpose                 |
| -------- | ----------------------- |
| `name`   | Describes the play/task |
| `hosts`  | Target group/host       |
| `become` | Run as sudo/root        |
| `tasks`  | List of actions to run  |

#### ✅ 2. Inventory File (.ini or .yml)
- What It Does:
  - Lists the servers to target.

- File Example: ``inventory.ini``
```
[web]
192.168.1.10 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/key.pem
```
Or in YAML format:
```
all:
  hosts:
    192.168.1.10:
      ansible_user: ubuntu
      ansible_ssh_private_key_file: ~/.ssh/key.pem
```
#### ✅ 3. Variable File (vars.yml or inline)
- What It Does:
  - Stores custom values you can reuse (like app names, ports, etc.).
```
---
http_port: 80
```
Use it in your playbook:
```
- name: Install Apache
  apt:
    name: apache2
    state: present

- name: Ensure Apache is listening on port {{ http_port }}
  lineinfile:
    path: /etc/apache2/ports.conf
    line: "Listen {{ http_port }}"
```

#### ✅ 4. Template File (.j2)
- What It Does:
  - Dynamic config files using Jinja2 templating.

- Example: index.html.j2
```
<html>
  <h1>Hello from {{ ansible_hostname }}</h1>
</html>
```
Used in playbook:
```
- name: Deploy index.html
  template:
    src: index.html.j2
    dest: /var/www/html/index.html
```
#### ✅ 5. Role Directory Structure (Optional but Best Practice)
Create using:
```
ansible-galaxy init myrole
```
Inside the role:
```
myrole/
├── tasks/main.yml
├── handlers/main.yml
├── templates/
├── files/
├── vars/main.yml
├── defaults/main.yml
└── meta/main.yml
```
Then use the role in a playbook:
```
- hosts: web
  roles:
    - myrole
```
#### 🧪 6. Running Your Playbook
```
ansible-playbook -i inventory.ini webserver.yml
```
| File                                | Purpose                  |
| ----------------------------------- | ------------------------ |
| `playbook.yml`                      | Main set of instructions |
| `inventory.ini`                     | List of target machines  |
| `vars.yml`                          | Store variable values    |
| `*.j2`                              | Jinja2 template files    |
| Role files (`tasks/main.yml`, etc.) | Organized reusable tasks |
