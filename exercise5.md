# Exercise 5: AAP as Code.

## STOP! 
It's best practice to have this section in a independant Ansible Project and including **EVERYTHING** apart of your teams organisation. For the purpose of this workshop we're running this with in our workshop_project as a example. 

 ## Step 1: Defining AAP as code

 How you've seen the pain of creating job templates over and over again in AAP, I bet you're eager to see a better way. Not only to automate the creating of AAP objectsm, but also acting as excellect backup/restore capabilities to rebuild your platform.

 I highly recommend you review the [ansible.controller](https://galaxy.ansible.com/ui/repo/published/infra/controller_configuration/docs/CONVERSION_GUIDE) collection documentationto find out the full capabilties for these roles. Pretty much everything in the AAP GUI can be recreated as code with this collection. This includes
 * Platform Configuration
 * Organisations
 * Users (including Source (LDAP, AD)
 * Credentials
 * Projects
 * Job Tempaltes
 * Inventories
 * Hosts
 * Execution Environments
 * ect, ect

In todays lab, we will be focusing on Projects and Job Templates. So lets get started.

1. First thing we need to do is add the collection into our requirements.yml file. To do this, open **collections\requirements.yml** and append the following `  - name: infra.controller_configuration` it should look like:
   ```yml
     collections:
    - name: community.windows
    - name: infra.controller_configuration
   ```
2. Once saved we're now going to create a list of vars which will create the skeleton of our role. create a new folder called **vars** and a new file under it called **AAP_ENV_VARS.yml**
3. Paste in the following variables from this file: [AAP_ENV_VARS.yml](https://github.com/rippa86/new_workshop/blob/main/Workshop_labs/vars/AAP_ENV_VARS.yml)
4. Now thats complete we're going to create a new playbook to build our environment. To do this create a new file in the root file system called **setup.yml**
5. paste in the following content:
   ```yml
    ---
    - name: Playbook to configure ansible controller post installation
      hosts: localhost
      connection: local
    
      pre_tasks:
        - name: Include vars from controller_configs directory
          ansible.builtin.include_vars: 
            file: AAP_ENV_VARS.yml
    
      roles:
        - infra.controller_configuration.dispatch
   ```
   * This playbook runs on localhost, as you can see we're not using our Windows Inventory, its going to run directly on AAP itself.
   * We're also introducing a new play. `pre_tasks_. as the name says, this will execute prior to tasks running, we're also using module [include_vars](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/include_vars_module.html). I recommend using this to remove long list of variables at the start of playbooks, making your code cleaner and removing the giant playbook of death vibes.
   * wait, theres no tasks???? thats right, instead of tasks, we're just executing a single role, roles can also be executed in tasks using the [include_role](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/include_role_module.html) module.
6. Save and sync the project in AAP as we've done previously. Now create a new Job Template called **setup**
7. Add in the following details:
      | Name | Value |
    | :-------------------------------- | :------------------------------------------- |
    | Name | setup_AAP |
    | Inventory | Demo Inventory |
    | Project | Dev_windows_workshop |
    | playbook | setup.yml |
    | Credential | ansible automation platform > AAP |
    | Execution Environment | Control Plane Execution Environment |
8. Click **Save**
9. go to the Survays Tab and add a new survey. add in the following:
       | Name | Value |
    | :-------------------------------- | :------------------------------------------- |
    | Question | Environment |
    | Answer Varible Name | env |
    | Answer type | Multiple choice (Single select) |
    | Multiple Choice Options| DEV (Push enter) PRD, select the tick to make DEV default | 
  click **Save**

11. Enable the survey back clicking the toggle and launch the job template
12. If you click on **Projects** you will notice that we have created a PRD version of windows_workshop and if you click on **Templates** you'll see the two templates we created last exersise. Give them a run and see how they work :)
    * If you run this against Prod. Nothing will happen, we there is nothing in that Main (Master) branch.


DONE! Configuration as code, how good is it! Now head to [Home!](index.md) to move onto the next exercise. After you hear about the next topic. 
