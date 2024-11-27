# hoa13.1
---
# Playbook: install_openstack.yml

# Install NTP on all servers
- name: Install NTP on all servers
  hosts: all
  become: true
  tasks:
    - name: Install NTP package
      apt:
        name: ntp
        state: present
        update_cache: yes

# Install OpenStack clients on controller node
- name: Install OpenStack client dependencies on controller
  hosts: controller
  become: true
  tasks:
    - name: Install OpenStack clients
      apt:
        name:
          - python3-openstackclient
          - python3-keystoneclient
          - python3-novaclient
          - python3-glanceclient
          - python3-neutronclient
          - python3-cinderclient
          - python3-heatclient
          - python3-swiftclient
          - python3-barbicanclient
          - python3-manilaclient
          - python3-troveclient
          - python3-designateclient
          - python3-ironicclient
        state: present
        update_cache: yes

    - name: Verify OpenStack client installation
      command: openstack --version
      register: openstack_version

    - name: Show OpenStack client version
      debug:
        var: openstack_version.stdout

# Install OpenStack packages on controller node
- name: Install OpenStack packages on controller
  hosts: controller
  become: true
  tasks:
    - name: Install OpenStack packages
      apt:
        name:
          - keystone
          - nova-api
          - nova-conductor
          - nova-scheduler
          - nova-cert
          - glance
          - neutron-server
          - neutron-plugin-ml2
          - neutron-linuxbridge-agent
          - neutron-dhcp-agent
          - neutron-l3-agent
          - cinder-api
          - cinder-scheduler
          - horizon
        state: present
        update_cache: yes

# Configure SQL Database on database server
- name: Configure SQL Database on database server
  hosts: database
  become: true
  tasks:
    - name: Install MySQL server
      apt:
        name: mysql-server
        state: present
        update_cache: yes

    - name: Configure MySQL database for OpenStack
      mysql_db:
        name: openstack
        state: present

    - name: Ensure MySQL service is started and enabled
      service:
        name: mysql
        state: started
        enabled: yes

# Install RabbitMQ (Message Queue) on message_queue server
- name: Install RabbitMQ (Message Queue) on message_queue server
  hosts: message_queue
  become: true
  tasks:
    - name: Install RabbitMQ server
      apt:
        name: rabbitmq-server
        state: present
        update_cache: yes

    - name: Ensure RabbitMQ service is started and enabled
      service:
        name: rabbitmq-server
        state: started
        enabled: yes

# Install Memcached on memcached server
- name: Install Memcached on memcached server
  hosts: memcached
  become: true
  tasks:
    - name: Install Memcached
      apt:
        name: memcached
        state: present
        update_cache: yes

    - name: Ensure Memcached service is started and enabled
      service:
        name: memcached
        state: started
        enabled: yes

# Install Etcd on etcd server
- name: Install Etcd on etcd server
  hosts: etcd
  become: true
  tasks:
    - name: Install Etcd
      apt:
        name: etcd
        state: present
        update_cache: yes

    - name: Ensure Etcd service is started and enabled
      service:
        name: etcd
        state: started
        enabled: yes
