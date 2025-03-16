**Ansible: Configuration and Automation Tool**

### Key Points
- **Agentless**: Ansible does not require agents on remote servers.
- **Push-based**: The control server pushes configurations to remote servers.
- **YAML-based Playbooks**: Easy-to-read and write configuration files.
- **SSH Passwordless Authentication**: Required for seamless communication between the control server and remote servers.
- **Inventory File**: Stores the list of remote servers.
- **Ad-hoc Commands**: Quick, one-time tasks.
- **Modules**: Reusable, structured units for managing system resources.

### Introduction to Ansible
Ansible is an open-source configuration management, application deployment, and automation tool. Unlike traditional methods where we manually log in to each server to install software, Ansible automates these tasks efficiently.

Before Ansible, tools like Chef, Puppet, and SaltStack were used for automation. However, Ansible is preferred due to its **agentless architecture**, making it easy to use as it only requires SSH access from the control server.

### Ansible Architecture
Ansible follows a **push-based model** and utilizes **YAML-based syntax**, referred to as **playbooks**. The architecture consists of:
- **Control Server**: Where Ansible is installed and commands are executed.
- **Remote Nodes (Worker Nodes)**: The servers where configurations and deployments take place.

### Setting Up Ansible
1. **Install Ansible on the Control Server**
   ```bash
   sudo yum install -y ansible  # For RHEL/CentOS
   sudo apt install -y ansible  # For Debian/Ubuntu
   ```

2. **Configure SSH Passwordless Authentication**
   - Generate SSH keys on the control server:
     ```bash
     ssh-keygen -t rsa -b 2048
     ```
   - Copy the public key to remote servers:
     ```bash
     ssh-copy-id ec2-user@<remote-server-ip>
     ```
   - Alternatively, manually paste the public key into `~/.ssh/authorized_keys` on the remote server.

3. **Configure the Inventory File**
   - The inventory file is used to manage remote servers.
   - Default location: `/etc/ansible/hosts`
   - Example:
     ```ini
     [web_servers]
     192.168.1.10
     192.168.1.11
     ```

4. **Verify Connectivity**
   ```bash
   ansible -i /path/to/inventory all -m ping
   ```

### Running Ansible Ad-Hoc Commands
Ad-hoc commands allow executing tasks without writing playbooks.

- **Check connectivity**
  ```bash
  ansible -i inventory all -m ping
  ```
- **Create a file**
  ```bash
  ansible -i inventory all -b -a "touch /opt/example_file"
  ```
- **Create a directory**
  ```bash
  ansible -i inventory all -b -a "mkdir /opt/example_dir"
  ```
- **Add a user**
  ```bash
  ansible -i inventory all -b -a "useradd jithu"
  ```
- **Delete a directory**
  ```bash
  ansible -i inventory all -b -a "rm -rf /opt/example_dir"
  ```

### Using Ansible Modules
Modules are reusable units of code to control system resources efficiently.

- **User Management**
  ```bash
  ansible -i inventory all -b -m user -a "name=jithu state=present"
  ansible -i inventory all -b -m user -a "name=jithu state=absent"
  ```

- **Package Management (YUM)**
  ```bash
  ansible -i inventory all -m yum -b -a "name=git state=present"
  ansible -i inventory all -m yum -b -a "name=httpd state=present"
  ```

- **Service Management**
  ```bash
  ansible -i inventory all -m service -b -a "name=httpd state=started"
  ```

- **File Management**
  ```bash
  ansible -i inventory all -m file -b -a "path=/opt/example_dir state=directory mode=700"
  ansible -i inventory all -m file -b -a "path=/opt/example_file state=touch"
  ```

- **Copy Files**
  ```bash
  ansible -i inventory all -m copy -b -a "src=/opt/index.html dest=/var/www/html/index.html"
  ```

- **Gather Server Information**
  ```bash
  ansible -i inventory all -m ansible.builtin.setup
  ```

### Ansible Playbooks
Playbooks are YAML files that define tasks to be executed on remote servers. They are more powerful and reusable than ad-hoc commands.

#### Example Playbook:
```yaml
---
- name: Install Git and Jenkins
  hosts: all
  become: yes
  tasks:
    - name: Install Git on git_server
      apt:
        name: git
        state: present
        update_cache: yes
      when: "'git_server' in group_names"

    - name: Install Jenkins on jenkins_server
      apt:
        name: jenkins
        state: present
        update_cache: yes
      when: "'jenkins_server' in group_names"

    - name: Ensure Jenkins service is running
      service:
        name: jenkins
        state: started
        enabled: yes
      when: "'jenkins_server' in group_names"
```

#### Run the playbook:
```bash
ansible-playbook -i /etc/ansible/inventory playbook.yml
```

### Conclusion
Ansible simplifies server management by automating repetitive tasks, reducing human error, and increasing efficiency. By leveraging SSH passwordless authentication, inventory management, and various modules, we can streamline IT operations seamlessly.

