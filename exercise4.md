# Exercise 4: Advanced Playbooks.

To support our application developers we're going to need to automate a environment to help give them the support they need to develop those trendy websites. To help support them we're going to create a couple of new playbooks. one to enable IIS using different ports for more complex sites and another to install development tools and local users for services accounts.

Over the next 30min we're going to start to build on our existing playbooks adding in some more functionality you'll need to utilise Ansible. In this next section you will master: 

1. Use of Conditionals. (when: failed_when: and register:)
2. Loops
3. Handlers
4. Blocks

Theres plenty of documentation on each if you'd like to learn more follow the link.

## Step 1: Adding in a new collection

First off the bat, we're going to need the support of a another windows collection. Is this bad? no, we're making use communitee modules which you're also free to use. Best check with Automation Owners before we do though 😃
To do this we're going to create a "requirements.yml" file which will pull the required collection in from Ansible Galaxy, becuase it isn't apart of our Lab execution environment.

1. In VScode, create a new folder and call it **collections**, under that directory create a file called **requirements.yml**
2. edit that file and add the following contents: 
```yaml
---
collections:
  - name: community.windows

```
> You can also include this collection as part of a Execution Environment, or have it available on Automation Hub.

3. Now thats added, when we sync the project in AAP, it will also Sync the collection from Galaxy. This means it will take a little longer for the project to sync. For Master Branch, I recommend having Sync off because you should only be syncing when you have Pull request into main.

## Step 2: IIS Dev Platform 

Now we're going to start to build a playbook to install a couple of local users as service accounts, update the Login banner and install some new features to help out of developers.

1. Create a new playbook call **iis_developer_platform.yml**
2. Add in the following host information and vars. You'll notice as part of the VARS we have some nested variables and lists. to support loops. 
  ```yaml
  ---
  - name: Setting up a IIS Development Server
    hosts: windows
    gather_facts: yes
    vars:
      win_features:
        - "Web-Mgmt-Tools"  
        - "Web-Asp-Net45"
      local_users:
        - name: "iis_admin"
          description: "Break Glass Account for Administrators"
          groups: "Administrators" 
        - name: "iis_general_user"
          description: "General service account for IIS applications."
          groups: "Users" 
      logon_message_caption: "Welcome to Our Lab Environment"
      logon_message_text: |
        This server is part of the Ansible 101 Lab.
        Unauthorized access is strictly prohibited.
        All activities may be monitored.
        If you are not an authorized user, disconnect immediately.
  ```
  Holy Moly! thats alot of vars!

3. next add in the following tasks, the first task we've seen before, its win_feature. but now, we've included a loop. Instead of Writing out the same module twice, we can now execute the same module twice, reducing our code by 4 lines. Everything inside the loop var is listed as the variable called `{{ item }}' with in the module.
> If you want to use loop over multiple tasks, you can use a module called include_tasks: tasks_file.yml to complete that. It Doesn't work with Block: 
  ```yaml
    tasks:
      - name: Install IIS server utilities
        ansible.windows.win_feature:
          name: "{{ item }}"
          state: present
        loop: "{{ win_features }}"
  ```
4. Next we're going to install a couple of local accounts using [win_user](https://docs.ansible.com/ansible/latest/collections/ansible/windows/win_user_module.html) to this server for the purpose of this Lab. Ansible does have dedicated collection for Active Directory. 
     ```yml
    - name: Install required local service accounts
      ansible.windows.win_user:
        name: "{{ item.name }}"
        description: "{{ item.description }}"
        groups: "{{ item.groups }}"
        state: present
      loop: "{{ local_users }}"
      when: add_users
    ```
   You can see at the end we're also adding a conditional to this module. this will only execute if add_users is true|yes otherwise it will be skipped.
5. To update the banner in Windows you're required to update the Policies in your registry. to date we're going to take advantage of the win_regedit module.

  > Making use of the win_regedit and the win_dsc modules are excellent tools to help enable configuration as code on Windows Systems. Together with Event Driven Ansible it will help you keep a constant configuration of Windows and instantly remediate if some one changes configuration they're not meant to. 
  ```yml
    - name: Set the Legal Notice Caption
      ansible.windows.win_regedit:
        path: HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System
        name: LegalNoticeCaption
        data: "{{ logon_message_caption }}"
        type: string 
        state: present 

    - name: Set the Legal Notice Text
      ansible.windows.win_regedit:
        path: HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System
        name: LegalNoticeText
        data: "{{ logon_message_text }}"
        type: string 
        state: present 
  ```
6. Now this is all completed save the playbook and move onto Step 3.

## Step 3: A Installing IIS Version 2.

The Developers are now asking if we can create a playbook that gives them more funtionality, supporting different ports and installing directories. To do this we're going to create a new playbook.

1. Create a new playbook called **install_iis_extra.yml** and to start add in the following. You'll notice that we're wrapping the text under a block statement, using the [win_file](https://docs.ansible.com/ansible/latest/collections/ansible/windows/win_file_module.html) to create a directory and adding a number of variables at the beginning of the play. 
  ```yml
---
- name: Installing IIS on a Windows Server
  hosts: windows
  gather_facts: yes
  vars:
    iis_port: 8080
    iis_path: C:\sites\awesomewebsite\
  tasks:
  - block: 
    - name: Install iis
      ansible.windows.win_feature:
        name: Web-Server
        state: present

    - name: Create site directory structure
      ansible.windows.win_file:
        path: "{{ iis_path }}"
        state: directory

    - name: Deploy welcome page from ourwebsite template
      ansible.windows.win_template:
        src: templates/index.html.j2 
        dest: "{{ iis_path }}/index.html"
  ```
2. Now that created its time to utilise our first community module. **[community.windows.win_iis_website](https://docs.ansible.com/ansible/latest/collections/community/windows/win_iis_website_module.html)** This will give us the functionality to change the default path and add a custom port. If this has been "Changed" we will call the handler "restart iis service"
   ```yml
    - name: Create IIS site
      community.windows.win_iis_website:
        name: Ansible Playbook Test
        state: started
        port: "{{ iis_port }}"
        physical_path: "{{ iis_path }}"
      notify: 
        - "restart iis service"
   ```
3. Because port 8080 isn't a default port for Webtraffic on Windows, we're going to have to punch a hole through that firewall! once again we're making use of the community module [win_firewall_rule](https://docs.ansible.com/ansible/latest/collections/community/windows/win_firewall_rule_module.html). add in the following plus the remaining tasks we've covered before. 
   ```yml
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
   ```
4. To finish off our block we're going to add the following rescue statement. How will it work? Because we've added a failed_when: conditional in our review HTTP POST, if we don't get a return code of 200, the playbook will trigger a failure. You can test out this use case if you disable the web service.
   ```yml
    rescue: 
    - name: Running Handler to restart service
      debug:
        msg: "somthing failed, running handler"
    - name: restart iis service
      ansible.windows.win_service:
        name: W3Svc
        state: restarted
        start_mode: auto
   ```
5. Now to finish off our playbook we're including the handler mentioned in step 2. this simply restarts IIS if we made a change above. 
  ```yml
  handlers:
    - name: restart iis service
      ansible.windows.win_service:
        name: W3Svc
        state: restarted
        start_mode: auto
  ```

DONE! you now wield the power to do great things in Ansible 🎊. Don't create these in AAP! We'll do it later 🤞 [Home!](index.md)
