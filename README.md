
# Ansible Top 5 Playbooks for Valid DevOps Use Cases

Welcome to our repository of Ansible playbooks tailored for common DevOps challenges. This collection demonstrates how to automate everyday tasks using a wide range of Ansible modules. Each playbook emphasizes idempotency, clear structure, and modular design while following industry best practices.

---

## Table of Contents

1. [Server Provisioning and Configuration](#server-provisioning-and-configuration)
2. [Application Deployment with Zero Downtime](#application-deployment-with-zero-downtime)
3. [Container Management and Orchestration](#container-management-and-orchestration)
4. [Infrastructure Monitoring and Logging Setup](#infrastructure-monitoring-and-logging-setup)
5. [CI/CD Pipeline Integration](#cicd-pipeline-integration)
6. [Project Structure](#project-structure)
7. [Best Practices](#best-practices)
8. [Getting Started](#getting-started)
9. [Contributing](#contributing)
10. [License](#license)

---

## 1. Server Provisioning and Configuration

This playbook automatically provisions servers by installing required packages, configuring users, and setting up the firewall. It uses a mix of modules such as `apt`, `yum`, and `user` to ensure your servers are consistently prepared across multiple distributions.

**Sample Playbook (`playbooks/1-server_provisioning.yml`):**

```yaml
- name: Provision and Configure Server
  hosts: all
  become: yes
  tasks:
    - name: Ensure required packages are installed (Debian)
      apt:
        name: ["git", "curl", "htop"]
        state: present
      when: ansible_os_family == "Debian"

    - name: Ensure required packages are installed (RedHat)
      yum:
        name: ["git", "curl", "htop"]
        state: present
      when: ansible_os_family == "RedHat"

    - name: Create non-root user for DevOps tasks
      user:
        name: devopsuser
        state: present
        groups: sudo
```

---

## 2. Application Deployment with Zero Downtime

This playbook focuses on coding deployments in a live environment by pulling the latest code from a Git repository, installing application dependencies, and performing a rolling restart on the service. Modules such as `git`, `command`, and `systemd` help maintain seamless deployments.

**Sample Playbook (`playbooks/2-application_deployment.yml`):**

```yaml
- name: Deploy Application with Zero Downtime
  hosts: app_servers
  become: yes
  vars:
    app_path: /var/www/myapp
  tasks:
    - name: Pull the latest code from the repository
      git:
        repo: 'https://github.com/example/myapp.git'
        dest: '{{ app_path }}'
        version: master

    - name: Install application dependencies
      command: npm install
      args:
        chdir: '{{ app_path }}'

    - name: Restart the application service
      systemd:
        name: myapp
        state: restarted
```

---

## 3. Container Management and Orchestration

In this playbook, Ansible integrates directly with container technologies. It builds Docker images and deploys containers using the `docker_image` and `docker_container` modules. This is ideal for microservices and scalable environments.

**Sample Playbook (`playbooks/3-container_management.yml`):**

```yaml
- name: Manage Docker Containers
  hosts: docker_hosts
  become: yes
  tasks:
    - name: Build Docker image for the application
      docker_image:
        path: "./docker"
        name: myapp_image
        tag: latest

    - name: Launch a Docker container based on the new image
      docker_container:
        name: myapp_container
        image: myapp_image:latest
        state: started
        restart_policy: always
        ports:
          - "80:8080"
```

---

## 4. Infrastructure Monitoring and Logging Setup

Monitoring is critical for maintaining system health. This playbook deploys configuration files via the `template` module, installs monitoring agents, and ensures that services are active using the `service` module.

**Sample Playbook (`playbooks/4-monitoring_logging.yml`):**

```yaml
- name: Setup Monitoring and Logging
  hosts: all
  become: yes
  tasks:
    - name: Deploy monitoring configuration file
      template:
        src: templates/monitoring.conf.j2
        dest: /etc/monitoring/monitoring.conf
        mode: '0644'

    - name: Install monitoring agent (Debian example)
      apt:
        name: monitoring-agent
        state: present
      when: ansible_os_family == "Debian"

    - name: Ensure the monitoring agent is running
      service:
        name: monitoring-agent
        state: started
        enabled: yes
```

---

## 5. CI/CD Pipeline Integration

This playbook bridges automated testing and deployment. It triggers build tests using the `command` module and then notifies stakeholders via the `mail` module upon successful deployment. This facilitates a smooth integration within your CI/CD pipeline.

**Sample Playbook (`playbooks/5-ci_cd_integration.yml`):**

```yaml
- name: CI/CD Pipeline Integration
  hosts: ci_cd_servers
  become: yes
  tasks:
    - name: Execute unit tests
      command: "./run_tests.sh"
      args:
        chdir: /opt/myapp

    - name: Notify team of successful deployment
      mail:
        host: smtp.example.com
        port: 25
        subject: "CI/CD Pipeline: Deployment Success"
        to: "team@example.com"
        body: "The build and deployment have been successfully completed."
```

---

## Project Structure

A recommended project structure for organizing your playbooks:

```
├── playbooks
│   ├── 1-server_provisioning.yml
│   ├── 2-application_deployment.yml
│   ├── 3-container_management.yml
│   ├── 4-monitoring_logging.yml
│   └── 5-ci_cd_integration.yml
├── templates
│   └── monitoring.conf.j2
├── inventory
│   ├── production
│   └── staging
├── roles
│   ├── common/
│   ├── app_deploy/
│   └── container/
└── README.md
```

---

## Best Practices

- **Idempotence:** Each task runs safely multiple times without causing unintended changes.
- **Modular Design:** Break tasks into roles and sub-playbooks where possible, making them reusable and easier to maintain.
- **Use Variables and Conditionals:** Adapt playbooks for different environments with variables, host/group inventories, and conditionals tailored to the operating system or environment ().
- **Clear Task Names:** Write descriptive names for tasks to ease troubleshooting.
- **Error Handling:** Use error handling and notify handlers to respond to failed tasks gracefully.
- **Version Control:** Track playbook changes with Git or another version control system.
- **Documentation:** Comment tasks and provide external documentation for complex procedures.

---

## Getting Started

1. **Install Ansible:**   
   Ensure you have a recent Ansible release (2.9 or later) installed.  
   ```bash
   pip install ansible
   ```

2. **Set up Your Inventory:**  
   Define your environments in the `inventory` folder.

3. **Run a Playbook:**  
   Execute any playbook using:
   ```bash
   ansible-playbook -i inventory/production playbooks/1-server_provisioning.yml
   ```

---

## Contributing

Contributions are welcome! Please fork this repository and submit a pull request with improvements or additional playbooks. For any questions or suggestions, feel free to open an issue.

---

## License

This project is licensed under the [MIT License](LICENSE).

---

By exploring this repository, you'll gain insight into practical automation workflows that integrate a variety of Ansible modules—from system provisioning to container orchestration. This setup not only benefits DevOps engineers striving for reliable infrastructure management but also enriches your continuous improvement toolkit.

Happy automating!
```
