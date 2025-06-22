# Exercise 3: Creating some Playbooks. 

Now its time to get Automating and its going to our tide and true Setting up a IIS website. Yes I'm sorry, but this is the easiest way to get people learning the fudementals. :) The Documentation to all the modules we use is in the links provided. I recommend reading through them to understand some of the modules.

Lets get this started by creating a super simple Hello world. 

## Step 1: Setting up VSCODE


```
---
- name: Installing IIS on a Windows Server
  hosts: windows
  gather_facts: yes
  vars:
    win_features:
      - "Web-Mgmt-Tools"  
      - "Web-Asp-Net45"
    local_users:
      - name: "iis_admin"
        description: "Break Glass Account for Administrators"
        groups: "Administrators" # Add to Administrators group for demo purposes
        locked: true
      - name: "iis_general_user"
        description: "General service account for IIS applications."
        groups: "Users" # Add to Users group
        locked: false
      
  tasks:

    - name: Install IIS server utilities
      ansible.windows.win_feature:
        name: "{{ item }}"
        state: present
      loop: "{{ win_features }}"

    - name: Install required local service accounts
      ansible.windows.win_user:
        name: "{{ item.name }}"
        password: "{{ lookup('ansible.builtin.password', '/tmp/windows_user_password.txt', length=8 chars=['ascii_letters,digits']) }}"
        description: "{{ item.description }}"
        groups: "{{ item.groups }}"
        account_locked: "{{ item.locked }}" #
        state: present
      loop: "{{ local_users }}"
```

```
---
- name: Installing IIS on a Windows Server
  hosts: windows
  gather_facts: yes
  vars:
    iis_port: 8080
    iis_path: C:\inetpub\wwwroot\index.html 
  tasks:
  - block: 
    - name: Install iis
      ansible.windows.win_feature:
        name: Web-Server
        state: present

    - name: Deploy welcome page from ourwebsite template
      ansible.windows.win_template:
        src: templates/index.html.j2 
        dest: "{{ iis_path }}"

    - name: Create IIS site
      community.windows.win_iis_website:
        name: Ansible Playbook Test
        state: started
        port: "{{ iis_port }}"
        physical_path: "{{ iis_path }}"
      notify: 
        - "restart iis service"

    - name: Open port for site on the firewall
      community.windows.win_firewall_rule:
        name: "iisport{{ iis_port }}"
        enable: true
        state: present
        localport: "{{ iis_port }}"
        action: Allow
        direction: In
        protocol: Tcp

    - name: set a url variable
      ansible.builtin.set_fact:
        iis_url: "http://{{ ansible_host }}:{{ iis_port }}"

    - name: Show website address
      ansible.builtin.debug:
        msg: "{{ iis_url }}"
    
    - name: test if I can access my site.
      ansible.windows.win_uri:
        url: "{{ iis_url }}"
      register: http_output
    
    - name: review HTTP POST
      ansible.builtin.debug:
        var: http_output.status_code
      failed_when: http_output.status_code != 200

    rescue: 
    - name: Running Handler to restart service
      debug:
        msg: "uri failed, running handler"
      notify: "restart iis service"

  handlers:
    - name: restart iis service
      ansible.windows.win_service:
        name: W3Svc
        state: restarted
        start_mode: auto
```
