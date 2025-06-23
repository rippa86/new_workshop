# Exercise 5: Ansible Best Practices! 

Such a big topic, This is somthing everyone learns as a team through following recommended approaches and guides. The only tip here I can give is that everyone set and agree to a pattern and keep improving that pattern over time. 

One thing i believe everyone can and should agree on is using roles! Roles and Collections are a requirement for creating **GOOD** and reuseable code.
* If you think something is reuseable: **put it in a role**
* If you don't think it will be, **put it in a role**
* If you have lots of roles for a single product, **build a collection**

Unless you're including roles for a specific function I recommend having them in their own separate repository and including them as a requirements.yml as per [this project](https://github.com/rippa86/Win-Ansible101/tree/main/roles)
* Role becomes indipendant of code, meaning it can be managed and updated separtly
* Others can utilise your code and incorporate it into their automation. Community of practice vibes. 

We're now going to combine 2 playbooks and turn them into a IIS main role. 
Now we know this, lets create a new role (sorry, we're going to do some bash!)

## Step 1: creating a role
1. We could create a role by doing it manually and creating 1000s of files. or we can do it the smart way 🙂
2. Open VScode, if the terminal isn't open, reopen it again via the top tool bar select terminal, **new terminal**
3. Type in the following command, `pwd` to ensure you're in the project directory it should return `home/student1/windows-workshop/workshop_project` if not, `home/student1/windows-workshop/workshop_project` into that directory
4. create a new directory called roles `mkdir roles` and cd into that directory `cd roles`.
5. Once in that directory, to create a new role called manage_iis enter the command `ansible-galaxy init manage_iis` Together it should look like this:
   ```bash
    [student1@ansible-1 workshop_project]$ pwd
    /home/student1/windows-workshop/workshop_project
    [student1@ansible-1 workshop_project]$ cd roles
    [student1@ansible-1 roles]$ ansible-galaxy init manage_iis
    - Role manage_iis was created successfully
   ```
6. If you look at your repository, you should now have a list of folders and files for your role, as well as a README.md file for Documentation. Such a timesaver!
7. We want to make roles as idempotent as possible. Meaning everything which can be veriablised, should be variabled. lets start that by creating some default varibles.
   > default variables are vars we expect to be updated during a play, but want to make them available from the start. this way we remove the common error variable is undefined.
8. open roles > manage_iis > defaults > **main.yml** ( The main file is what the role executes from)
9. add in the following vars:
    ```yml
    install_iis: yes
    template_dest: C:\inetpub\wwwroot\
    html_file_name: index.html
    your_name: George
    colour: Blue
    ```
  10. Now because we don't want anyone updating a jinja 2 source, we're going to set that in a vars: directory as this is more of a static variable and is harder to change. Open the vars > **main.yml** file and add in
      ```yml
      template_src: templates/index.html.j2
      ```
  11. So we have our semi idempotent role, lets get along with creating some tasks, in the tasks\ directory create two new files called **uninstall.yml** and **install.yml**
  12. Copy all the tasks: from install.yml and paste it in the install.yml. making sure there is no extra indentation. from there also populate our new vars in the correct locations. It should like somthing like this:
      ```yml
      - name: Install iis
        ansible.windows.win_feature:
          name: Web-Server
          state: present
      
      - name: Deploy welcome page from ourwebsite template
        ansible.windows.win_template:
          src: "{{ template_src }}"
          dest: "{{ template_dest }}{{ html_file_name }}"
      
      - name: Start iis service
        ansible.windows.win_service:
          name: W3Svc
          state: started
      
      - name: set a url variable
        ansible.builtin.set_fact:
          iis_url: "http://{{ ansible_host }}"
      
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
      ```
14. Now do the same for **uninstall.yml** if you didn't do that exersise, don't stress. the code is down below :)
    ```yml
    - name: Start iis service
      ansible.windows.win_service:
        name: W3Svc
        state: stopped
    
    - name: Install iis
      ansible.windows.win_feature:
        name: Web-Server
        state: absent
    
    - name: Deploy welcome page from ourwebsite template
      ansible.windows.win_file:
        path: "{{ template_dest }}{{ html_file_name }}"
        state: absent
    
    - name: set a url variable
      ansible.builtin.set_fact:
        iis_url: "http://{{ ansible_host }}"
    
    - name: Show website address
      ansible.builtin.debug:
        msg: "{{ iis_url }}"
    
    - name: test if I can access my site.
      ansible.windows.win_uri:
        url: "{{ iis_url }}"
      register: http_output
      ignore_errors: true
    
    - name: review HTTP POST
      ansible.builtin.debug:
        var: http_output.status_code
    ```
15. Brilliant so now we have the meat n cheese of a manage_iis sandwhich. To call these tasks in the main.yml role, where going to use the [include_tasks](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/include_tasks_module.html) module.
> If tasks are of different nature, I always recommend splitting them off into their own playbook. This way every function can have its own code space.
16. open tasks / main.yml and add in the following code, where if install_iis = true, install, else remove.
```yml
- name: include tasks for installing iis
  ansible.builtin.include_tasks: tasks/install.yml
  when: install_iis

- name: include tasks for removing IIS
  ansible.builtin.include_tasks: tasks/uninstall.yml
  when: not install_iis
```
Look at that we have a role. So now, how do we call it?

Simple, we saw it earlier :).
17. Create a new playbook called **iis_main.yml** in the root project directory.
18. Add in the following code: 
```
---
- name: Setting up a IIS Development Server
  hosts: windows
  gather_facts: yes

  roles:
    - manage_iis
```
19. When run this will call the role to manage_iis and based on a flag it will install/remove it.
20. Lets test. Because we're all config as code, add in the following lines in **AAP_ENV_VARS.yml**
```
  - name: "{{ env }}_iis_main"
    job_type: run
    inventory: Workshop Inventory
    execution_environment: Default execution environment
    survey_enabled: false
    project: "{{ env }}_windows_workshop"
    playbook: "iis_main.yml"
    credentials:
    - Windows_host
```
21. In AAP run job template **setup** and test out our new role. ( to remove iis, just add in extra vars or create a survey to choose which one.

## Step 2: Its production time. 

Wow great work. It looks like we're now ready to update our code into production. lets start to merge request main. In your Git you SHOULD have merge protection turned on for Main or Master branches.

1. open gitea and press **New Pull Request**
2. ensure **merge into: student1:master** and **pull from: student1:development**
3. press **New pull request**
4. review all your changes like good code quality people and press **Merge Pull request** and press it again. (yeah merging is a big deal, enjoy the conversation with your developer friends)
5. You should now see all your code in the master branch.
6. Now, open AAP, go to **Templates** and launch **setup**, select **PRD**
7. Once compelte, click on **Templates** There should be all the templates available in "Production". Seriously, how good is infrastructure as code. Short term pain, long term gain.


That Completes the course. 

Thanks for attending and running through these labs. All feed back is Welcome :) 

Cheers, 
Rippa. 
























    
