# Ansible Playbooks: Automating Multiple Tasks Using YAML

Ansible playbooks are used to automate multiple tasks efficiently. They are written in **YAML format** (`.yaml` or `.yml` extension). YAML is one of the simplest ways to represent structured data and is commonly used for writing playbooks.

## Key Features of YAML in Ansible Playbooks:
- YAML files start with three dashes (`---`).
- Data is represented in a structured and human-readable format.
- Indentation is used instead of brackets, making it easy to write and understand.

---

## Writing an Ansible Playbook
A playbook consists of **plays**, which contain tasks that are executed on specified hosts. Below is an example of an Ansible playbook to install **Git** and **Apache (httpd)** on multiple servers:

```yaml
---
- name: Install Git and Apache
  hosts: all
  become: true  # Run tasks with sudo privileges

  tasks:
    - name: Install Git
      yum:
        name: git
        state: present

    - name: Install Apache
      yum:
        name: httpd
        state: present
```

## Executing an Ansible Playbook
To run the playbook, use the following command:

```bash
ansible-playbook -i inventory playbook.yaml
```
- `-i inventory` specifies the inventory file containing the list of target servers.
- `playbook.yaml` is the name of the playbook file.

If there are any code changes, you can rerun the playbook to apply updates.

---

## Managing Services Using Ansible
You can **start, stop, or restart** services using Ansible Playbooks. Example to stop a service:

```yaml
---
- name: Stop Apache Service
  hosts: all
  become: true

  tasks:
    - name: Stop Apache
      service:
        name: httpd
        state: stopped
```

If you need to restart a service on a specific server without modifying the playbook, you can use **Ad-Hoc commands** with `--limit` to target a particular host:

```bash
ansible-playbook -i inventory playbook.yaml --limit 192.168.1.100
```

---

## Using Tags to Run Specific Tasks
Tags allow running specific tasks in a playbook instead of executing all tasks. Example playbook with tags:

```yaml
---
- name: Manage Apache Service
  hosts: all
  become: true

  tasks:
    - name: Start Apache
      service:
        name: httpd
        state: started
      tags: start_httpd

    - name: Copy Application Code
      copy:
        src: /local/path/app
        dest: /var/www/html/
      tags: copy_code
```

To execute only a specific task using tags:

```bash
ansible-playbook -i inventory --tags=start_httpd playbook.yaml
```

```bash
ansible-playbook -i inventory --tags=copy_code playbook.yaml
```

---

## Working with Ansible Modules
Modules are essential in Ansible for performing various automation tasks. Some frequently used modules include:
- `yum` – Install packages
- `service` – Manage services
- `copy` – Copy files
- `template` – Deploy templates
- `user` – Manage users
- `file` – Manage file permissions

To explore more modules, refer to the **Ansible Documentation**:
🔗 [https://docs.ansible.com/](https://docs.ansible.com/)

---

## Register Variables in Ansible
You can **store command output** using the `register` variable and display it using the `debug` module.

Example:

```yaml
---
- name: Capture Host Uptime
  hosts: all
  become: true

  tasks:
    - name: Get uptime
      command: uptime
      register: uptime_output

    - name: Display uptime
      debug:
        var: uptime_output.stdout
```

---

## Conclusion
Ansible playbooks provide a powerful way to **automate repetitive tasks**. By leveraging modules, tags, and ad-hoc commands, you can efficiently manage infrastructure with minimal manual intervention.


