# Building RabbitMQ Cluster Using Ansible

#### Prerequisite
- Ubuntu Server (>= v16.04 LTS)
- Ansible installed

First, set up inventory hosts that include all machines for building cluster
```
[mq-cluster]
192.168.0.7
192.168.0.11
192.168.0.12

[mq-workers]
192.168.0.11
192.168.0.12

[mq-master]
192.168.0.7

[all:vars]
ansible_python_interpreter=/usr/bin/python3
```

 Define variable for usage as example in __vars/settings.yml__ and templates as in __templates__ folder. Then, Install erlang and rabbitmq server on each machine
```bash
ansible-playbook -i inventory/hosts playbooks/install-rabbitmq-broker.yml
```

After installed RabbitMQ server on each machine, now set up the cluster by copying all same cookie for all machines and restarting RabbitMQ servers

first we need copy erlang cookie from master machine into __config__ folder

```bash
 sudo cp /var/lib/rabbitmq/.erlang.cookie config/
 sudo chown your_user:your_group config/.erlang.cookie
```
Then,
```bash
ansible-playbook -i inventory/hosts playbooks/setup-rabbitmq-cluster.yml
```

Now the cluster of rabbitmq servers has been set up. Enable ssl/tls (how to create self-signed ssl/tls check this github link => https://github.com/michaelklishin/tls-gen, in case just copy all certificate generated to _ssl_ folder) and MQTT plugin for extra usage and personal purpose
```bash
ansible-playbook -i inventory/hosts playbooks/enable-ssl-mqtt.yml
```

Create specific user for MQTT
```bash
ansible-playbook -i inventory/hosts playbooks/create-mqtt-user.yml
```



