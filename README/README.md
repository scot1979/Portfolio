## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

!(Images/ELK_stack_network.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the YML file may be used to install only certain pieces of it, such as Filebeat.

[install-elk.yml](../Ansible/install-elk.yml)
[filebeat-playbook.yml](../Ansible/filebeat-playbook.yml)
[metricbeat-playbook.yml](../Ansible/metricbeat-playbook.yml)

---
- name: Configure Elk VM with Docker
  hosts: elk
  remote_user: sysadmin
  become: true
  tasks:
    # Use apt module
    - name: Install docker.io
      apt:
        update_cache: yes
        force_apt_get: yes
        name: docker.io
        state: present

      # Use apt module
    - name: Install python3-pip
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present

      # Use pip module (It will default to pip3)
    - name: Install Docker module
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
        value: '262144'
        state: present
        reload: yes

      # Use docker_container module
    - name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        # Please list the ports that ELK runs on
        published_ports:
          -  5601:5601
          -  9200:9200
          -  5044:5044

      # Use systemd module
    - name: Enable service docker on boot
      systemd:
        name: docker
        enabled: yes



This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly efficient, in addition to restricting access to the network.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the server logs and system metrics.

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name          | Function        | IP Address | Operating System |
|---------------|-----------------|------------|------------------|
| Jump Box      | Gateway         | 10.0.0.4   | Ubuntu Linux     |
| Web 1         | Web Server      | 10.0.0.5   | Ubuntu Linux     |
| Web 2         | Web Server      | 10.0.0.6   | Ubuntu Linux     |
| Web 3         | Web Server      | 10.0.0.7   | Ubuntu Linux     |
| ELK Stack     | Data Analytics  | 10.1.0.4   | Ubuntu Linux     |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jump Box machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
[Personal IP address]

Machines within the network can only be accessed by the Jump Box at IP address 10.0.0.4.

A summary of the access policies in place can be found in the table below.

| Name          | Publicly Accessible | Allowed IP Addresses     |
|---------------|---------------------|--------------------------|
| Jump Box      | Yes                 | Personal                 |
| Load Balancer | Yes		      | Open			 |
| Web 1         | No                  | 10.0.0.4                 |
| Web 2         | No                  | 10.0.0.4                 |
| Web 3         | No                  | 10.0.0.4                 |               
| ELK Stack     | Yes                 | 10.0.0.4, Personal       |                

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because automation allows you to easily spin up a new virtual machine with your chosen configurations.

The playbook implements the following tasks:

	Install Docker, if not already installed
	Install PIP3 (a Python package that allows Python to run in isolation), if not already installed
	Install Docker Python library, if not already installed
	Increase Virtual Memory
	Use the Extra Virtual Memory that was just increased
	Download and launch docker ELK container image and set to always restart if fails
	Enable Docker Service on Boot
	
The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

!(Images/docker_ps_output.png)


### Target Machines & Beats
This ELK server is configured to monitor the following machines:
 	Web 1: 10.0.0.5
	Web 2: 10.0.0.6
	Web 3: 10.0.0.7

We have installed the following Beats on these machines:
	Filebeats
	Metricbeats
	
These Beats allow us to collect the following information from each machine:
	Filebeats collects changes to server logs. For example, filebeats can collect audit logs, which documents which resources were accessed at which time. 
	Metricbeats collects periodic metrics and statistics from the server that is can then be parsed, analyzed, and visualed. For example, metricbeats can regularly collect data for CPU usage, which can then be visualized in Kabana. 


### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the install-elk.yml file to onto the Web VMs.
- Update the /etc/ansible/host file to include the IP addresses of both the ELK Stack VM and Web VMs.
- Run the playbook, and navigate to http://[ELK_Stack's_Public_IP_address]:5601/app/kibana to check that the installation worked as expected.
