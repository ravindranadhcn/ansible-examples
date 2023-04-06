
```
ansible-playbook -i hosts testroot.yaml -e "host=host.iil.corp.com" --ask-become-pass -k
```
```
testroot.yaml
---
- hosts: '{{ host }}'
  gather_facts: yes
  tasks:
    - name: Get current user on remote
      ansible.builtin.shell: |
        whoami
      become: true
      become_exe: "sudo rootsh"
      register: out
    - debug:
        msg: "{{ out }}"

````
```
ansible-playbook -i hosts testroot.yaml -e "host=host.iil.corp.com" --ask-become-pass -k
```

#In ansible if you have become: true on a task, that task will run with elevated privileges. On Linux the default is to try sudo. You don't need to specify become_exe. Any command given to your shell task will run in a root privileged shell. The user ID you run the playbook as must have login access to the remote system and sudo privilege on the remote system via /etc/sudoers or a file in /etc/sudoers.d.

#In our environment we have some common files we populate in /etc/sudoers.d based on server function. For example, all servers we manage have a server mgmt id we use for remote mgmt and a special group for our own user IDs when we remote into those machines. We place a file in /etc/sudoers.d that grants our mgmt ID and mgmt group the rights we need. For all database servers our DBA group requires some privileges so we add an /etc/sudoers.d/dba file that controls their privileged access for members of the DBA group members.

#In your testroot.yaml file you can remove the become_exe line.
