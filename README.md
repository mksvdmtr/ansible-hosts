# ansible-hosts
Collect ips from some servers and setting /etc/hosts on another host(s)

```
# Examples:
# ansible-playbook -i inventory hosts.yml
---
- hosts: apps
  remote_user: root
  tasks:
          - name: Collecting IPs
            lineinfile:
                    line: "{{ inventory_hostname_short }}{{':'}} {{ ansible_eth0.ipv4.address }}"
                    path: "{{ playbook_dir }}/vars/hosts.yml"
            delegate_to: 127.0.0.1

- hosts: haproxy
  remote_user: root
  tasks:
          - name: Loading hosts vars 
            include_vars:
                    file: "{{ playbook_dir }}/vars/hosts.yml"
                    name: hosts
          
          - name: Updating /etc/hosts on {{ inventory_hostname_short }}
            lineinfile:
                    line: "{{ item.value }}     {{ item.key }}"
                    path: "/etc/hosts"
            loop: "{{ hosts | dict2items }}"
```
