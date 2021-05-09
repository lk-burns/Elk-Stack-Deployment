# Elk-Stack-Deployment
Project 1 - Automated ELK Stack Deployment


## Automated ELK Stack Deployment

The files contained in this repository were used to configure the network depicted below.

![image](https://user-images.githubusercontent.com/75952979/117557330-ae61b800-b03f-11eb-8e40-433abf45f41a.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above or alternatively, pieces of each playbook file may be used to install certain applications, such as Filebeat or Metricbeat.

  - Install-Elk.yml
  - Filebeat-Playbook.yml
  - Metricbeat-Playbook.yml

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting inbound access to the network. A load balancer efficiently distributes network traffic within a multi-server environment, preventing too many requests from negatively impacting a server. It allows for work to be distributed evenly and ensures application responsiveness and availability. In addition to the load balancer, a jump box was designed to provide a secure access point for authorized users to connect to the DVWAs and other environments.

Integrating an ELK (Elasticsearch-Logstash-Kibana) server allows users to easily monitor the vulnerable VMs for changes to their file systems on the network, record log events, and periodically collect and assess system and service metrics (i.e. attempted SSH logins, sudo escalation failures, and CPU usage).

The configuration details of each machine may be found below.

|   Name   |  Function  | IP Address | Operating System |
|:--------:|:----------:|:----------:|:----------------:|
| Jump Box |   Gateway  |  10.0.0.7  |       Linux      |
| DVWA 1   | Web Server |  10.0.0.5  |       Linux      |
| DVWA 2   | Web Server |  10.0.0.6  |       Linux      |
| ELK      | Monitoring |  10.1.0.4  |       Linux      |


### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the jump box machine can accept connections from the Internet. Access to this machine is only allowed from my personal IP address: 108.XXX.XX.XX

Machines within the network can only be accessed by each other. For example, the ELK server can receive traffic from the VMs of DVWA 1 and DVWA 2.

A summary of the access policies in place can be found in the table below.

|   Name   | Publicly Accessible | Allowed IP Addresses |
|:--------:|:-------------------:|:--------------------:|
| Jump Box |         Yes         |    108.XXX.XX.XX     |
| DVWA 1   |          No         |       10.0.0.5       |
| DVWA 2   |          No         |       10.0.0.6       |
| ELK      |          No         |       10.1.0.4       |


### ELK Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because it reduces human error and coding mistakes, eliminates repetitive tasks, as well as saves time and effort that can be applied elsewhere.

The Install-Elk playbook implements the following tasks:
- Downloads and configures ELK VM with Docker container
  - In header of Ansible playbook, specify hosts and remote users
  - Ensures system requirements for the ELK container
    - Install docker.io
    - Install python3-pip
    - Install docker (Docker Python pip module)
    - Use sysctl module to increase memory of container
- Downloads and launches sebp/elk:761 Docker container
  - Designates published ports to start container
     - 5601:5601
     - 9200:9200
     - 5044:5044 
- Configures service Docker to start on boot 

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![image](https://user-images.githubusercontent.com/75952979/117557926-3fd42880-b046-11eb-9dae-d9c8ed49658e.png)

Below is a copy of the Install-Elk playbook:

```yaml
---
# install_elk.yml
- name: Configure Elk VM with Docker
  hosts: elkservers
  remote_user: elk
  become: true
  tasks:
    # Use apt module
    - name: Install docker.io
      apt:
        update_cache: yes
        name: docker.io
        state: present

      # Use apt module
    - name: Install pip3
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present

      # Use pip module
    - name: Install Docker python module
      pip:
        name: docker
        state: present

      # Use command module
    - name: Increase virtual memory
      command: sysctl -w vm.max_map_count=262144

      # Use sysctl module
    - name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: "262144"
        state: present
        reload: yes

      # Use docker_container module
    - name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        published_ports:
          - 5601:5601
          - 9200:9200
          - 5044:5044
```

### Target Machines & Beats
This ELK server is configured to monitor the following virtual machines: DVWA 1 (10.0.0.5) abd DVWA 2 (10.0.0.6).

We have installed the following Beats on these machines:
- Filebeat
- Metricbeat

These Beats allow us to collect the following information from each machine:
- **Filebeat:** collects and ships log files from the container including audit logs, gc logs, server logs, depreciation logs, and slow logs.
- **Metricbeat:** periodically collects system-wide metrics on CPU, disk, and memory usage, network utilizations, as well as detects failed sudo escalations and SSH login attempts.

The playbooks below will install Filebeat and Metricbeat, respectively, on the target hosts:

```yaml
---
- name: Install and Launch Filebeat
  hosts: webservers
  become: true
  tasks:
    # Use command module
  - name: Download filebeat .deb file
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.6.1-amd64.deb

    # Use command module
  - name: Install filebeat .deb
    command: dpkg -i filebeat-7.6.1-amd64.deb

    # Use copy module
  - name: Drop in filebeat config
    copy:
      src: /etc/ansible/files/filebeat-config.yml
      dest: /etc/filebeat/filebeat.yml

    # Use command module
  - name: Enable and configure system module
    command: filebeat modules enable system

    # Use command module
  - name: Setup filebeat
    command: filebeat setup

    # Use command module
  - name: Start filebeat service
    command: service filebeat start
    
    # Enable filebeat on boot
  - name: Enable service filebeat on boot
    systemd:
      name: filebeat
      enabled: true
```


```yaml
---
- name: Install and Launch Metricbeat
  hosts: webservers
  become: true
  tasks:
    # Use command module
  - name: Download metricbeat
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-7.4.0-amd64.deb

    # Use command module
  - name: Install metricbeat .deb
    command: dpkg -i metricbeat-7.4.0-amd64.deb

    # Use copy module
  - name: Drop in metricbeat config
    copy:
      src: /etc/ansible/files/metricbeat-config.yml
      dest: /etc/metricbeat/metricbeat.yml

    # Use command module
  - name: Enable and configure docker module for metricbeat
    command: metricbeat modules enable docker

    # Use command module
  - name: Setup metricbeat
    command: metricbeat setup

    # Use command module
  - name: Start metricbeat service
    command: service metricbeat start
    
    # Enable filebeat on boot
  - name: Enable service metricbeat on boot
    systemd:
      name: metricbeat
      enabled: true
```

### Using the Playbooks
In order to use the playbooks, an Ansible control node should be configured onto the jump box. Assuming you have such a control node provisioned: 

- SSH into the Ansible control node and copy the following playbook files into the /etc/ansible directory:
  - Install_Elk.yml
  - Filebeat-Playbook.yml
  - Metricbeat-Playbook.yml

You can also copy the playbooks with Git to their destination: git clone https://github.com/lk-burns/Elk-Stack-Deployment.git

- Update the `hosts` file in /etc/ansible/ to specify which VMs to run each playbook. Ensure that the private IP addresses of the ELK and DVWA servers are correct. In addition, include the command 'ansible_python_interpreter=/usr/bin/python3' beside each IP address as shown below to configure a Python 3 interpreter:

![image](https://user-images.githubusercontent.com/75952979/117558267-ca6a5700-b049-11eb-98a5-873a4cc3c468.png)


- Run the playbooks with the following commands:
  - $ cd /etc/ansible
  - $ ansible-playbook Install_Elk.yml elk
  - $ ansible-playbook Filebeat-Playbook.yml webservers
  - $ ansible-playbook Metricbeat-Playbook.yml webservers

It may take a few minutes for the ELK server to start up so be patient! 

- Navigate to http://[ELKServerPublicIP]:5601/app/Kibana#. If this link renders the Kibana website, a connection to the ELK server has been created. To check if installation was successful for Filebeat and Metricbeat, click the "Check data" button located toward the bottom of their corresponding download pages on Kibana. If data is shown as being delivered, all is well and you will see the below images.

![image](https://user-images.githubusercontent.com/75952979/117560882-c77a6100-b05f-11eb-8c9a-b4ffa83b212b.png)

![image](https://user-images.githubusercontent.com/75952979/117559279-f4277c00-b051-11eb-9965-edc158a3cf00.png)

![image](https://user-images.githubusercontent.com/75952979/117559285-03a6c500-b052-11eb-8942-a6b13090e437.png)
