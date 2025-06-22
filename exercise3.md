# Exercise 3: Creating some Playbooks. 

Now its time to get Automating and its going to our tide and true Setting up a IIS website. Yes I'm sorry, but this is the easiest way to get people learning the fudementals. :) The Documentation to all the modules we use is in the links provided. I recommend reading through them to understand some of the modules.

Lets get this started by creating a super simple Hello world. 

## Step 1: Setting up VSCODE

Because we're using Proper dev ways of working we're going to need to set up Vscode to use our developer branch. To do this:

1. Open and login to your vsCode application from your Workshop machine using the username and password provided
2. You should have the Workshop Project already set up, so no importing required!
3. Along the top menu bar select **Terminal** then **New Terminal** A terminal window should pop up along the bottom.
4. Enter the following commands into the Terminal:
   ```BASH
   git config --global user.email "student1@lab.com"
   git config --global user.name "student1"
   ```
5. once created run ``` git pull ``` in the terminal window to pull in your new remote branch called `origin\development`
6. Once Synced click on the 3 dots **...** to the right of **SOUCE CONTROL** and select **Checkout to**. This will open a small drop down box in the middle of the screen select branch **development**
7. Awesome! now we're cooking with gas. Lets get on to building our playbook.

## Step 2: Creating Hello World

1. Head back into our repository by clicking on the first button on the left hand side.
2. click on the icon to create a new file. Call it **hello_world.yml** open the file in your editor.
3. Lets start by defining our play and telling it which hosts to target.
4. Add the following into your **hello_world.yml** file
5. ****REMEMBER**** double space indentation!!!!
```YAML
---
- name: Running Hello World against our host.
  hosts: windows
  gather_facts: yes
```
  * the `---` indicate the start of a YAML file
  * `- name:` is our human readable label of what we're doing
  * `hosts:` the hosts we're going to automate against.
  *  `gather_facts: yes` telling it to get all the delicous facts about our host. [Facts docoo](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_vars_facts.html)
5. Now lets start to add a task to our play:
```yaml
  tasks:
    - name: debugging msg hello world
      ansible.builtin.debug:
        msg: "Hello world!" 

# Space out your tasks to make them easier to read!

    - name: debugging var ansible_os_name
      ansible.builtin.debug:
        var: ansible_os_name
  ```
  *`tasks:` All tasks you want to run in your playbook come under this parameter. There are more out this. but you'll use this the most.
  * [ansible.builtin.debug](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/debug_module.html) This is the ansible way of printing out a line like `echo "Hello world"`.
7.  Once complete our playbook should look somthing like this. press ctrl + s to save. 
```yaml
- name: Running Hello World against our host.
  hosts: windows
  gather_facts: yes
  
  tasks:
    - name: debugging msg hello world
      ansible.builtin.debug:
        msg: "Hello world!" 

    - name: debugging var ansible_os_name
      ansible.builtin.debug:
        var: ansible_os_name
```
8. So we have a playbook, lets add it to Git, click on the **souce control button** and in the message field place in some text `Inicial commit, hello_world.yml` click on the drop down arrow and press **Committ & Push**
9. If that worked correcttly, open Gitea, refresh and MAGIC!!! we have a new file in our repo! 
> **TOP TIP:** Make commits meaningful. Its easier for everyone. IF you're fixing a Bug or issue you can also #1 the issue number

## Step 3: Creating a Job Template

Now its time to incorporate this playbook into Ansible Automation Platform. Log back into your AAP platform. 
1. First off, we need to Sync our Project so Hello_world.yml is available. click **Recources** menu on the left and **Projects**.
2. For **DEV_windows_workshop** on the right had side click the **sync project** button, (the two circle arrows) and wait for the successful message.
3. Now its Synced its time to create a job template. As mentioned in the training material a Job template is where we run all our playbooks, these can also be included in a workflow template for end to end automation.
4. in the resouces menu select **Templates** press **Add** and select **Add Job Template**
5. A new screen will appear. Add the following information:
| Name | Value |
    | :-------------------------------- | :------------------------------------------- |
    | Name | hello_world |
    | Inventory | Workshop Inventory |
    | Project | Dev_windows_workshop |
    | playbook | hello_world.yml |
    | Credential | Windows_host |
6. Click **Save** to create the Job Template. To Run simply press **Launch**
7. If everything is good and gravy, you should see a output like this:
> Notice how helpful having proper names are :D.

# Step 4. Installing IIS. 
Now its time to set up the playbook we've all been waiting for Installing IIS to get started we're going to create a website template, install IIS, enable the service and of course test. . . 

1. Lets get back into our repository and create a new folder called **templates**. once created, we're going to create a new jinja template called **`website_template.html.j2`**
2. Copy the following contents into the file. Notice that we have to variables in the file called `your_name` and `colour'.
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Welcome</title>
    <style>
        body {
            margin: 0;
            font-family: sans-serif; /* Use a basic sans-serif font */
            background-color: #f0f2f5; /* Very light grey background */
            display: flex;
            flex-direction: column;
            min-height: 100vh;
        }
        header {
            background-color: {{ colour }}; /* Black header background */
            text-align: center;
            padding: 10px 0; /* Minimal padding */
        }
        header img {
            max-width: 200px; /* Smaller image size */
            height: auto;
        }
    </style>
</head>
<body>
    <header>
        <!-- Red Hat Ansible Automation Platform Logo -->
        <img src="https://developers.redhat.com/sites/default/files/styles/keep_original/public/logo-red_hat-ansible_automation_platform-a-reverse-rgb.large-logo-transparent.png?itok=Wsl5PYZ9" 
             alt="Ansible Logo"
             onerror="this.onerror=null;this.src='https://placehold.co/150x40/000000/FFFFFF?text=Logo';">
        <!-- Fallback placeholder image with simplified text -->
    </header>
            <p>Hello {{ your_name }}. Welcome to my website, deployed by Ansible Automation Platform</p>
</body>
</html>
```
3. Now thats created. Lets create a new file and call it **install_iis.yml**
4. Add in the following host information we did before:
```yml
---
- name: Installing IIS on a Windows Server
  hosts: windows
```
> TOPTIP: Remember indentation. Copying and pasting here might not be your best option 🤔

5. For tasks where going to add the following Modules: here is the documentation up front.
- [win_template](https://docs.ansible.com/ansible/latest/collections/ansible/windows/win_template_module.html) - dumps a template from the remote server
- [win_feature]() - Where using this to install the iis web server
- [win_service]() - to start the webservice
- Debug to print the URL
- Win_ping to test if its working.
```yml
  tasks:
  - name: Install iis
    ansible.windows.win_feature:
      name: Web-Server
      state: present

  - name: Deploy welcome page from ourwebsite template
    ansible.windows.win_template:
      src: /path/to/your/template/index.html.j2 
      dest: C:\inetpub\wwwroot\index.html   

  - name: Start iis service
    ansible.windows.win_service:
      name: W3Svc
      state: started

  - name: Show website address
    ansible.builtin.debug:
      msg: "http://{{ ansible_host }}"
```
6. Save you new playbook, and commit and push the code to Gitea as we've done previously.
7. Its time to create a new job template! Resync our **DEV_windows_workshop** from the **Projects** menu and clicking **Sync Project**
8. Once sync'd create a Create a new Job template with the follow details:
   | Name | Value |
    | :-------------------------------- | :------------------------------------------- |
    | Name | Install_IIS |
    | Inventory | Workshop Inventory |
    | Project | Dev_windows_workshop |
    | playbook | install_iis.yml |
    | Credential | Windows_host |
    | Variables | your_name: your first name here please! 
    | Variables | colour: Blue |
9. Press **Save**
10. Lets add a optional Survey for the colour. to this go to the **Survey** Tab and click **Add**
11. Put in the follow details:
    | Name | Value |
    | :-------------------------------- | :------------------------------------------- |
    | Question | Banner Colour |
    | Answer Varible Name | colour |
    | Default Answer | Black |

13. Click **Save**
14. Now check your new **colour** survey and toggle the survey to **Enabled**
15. To launch. click the **details tab**, and click **Launch**
> You can also hit the rocket icon
15. Your new survey will pop open, enter a colour (for example Green) and hit **Next** then press **Launch**
16. Review your output.
> You can check out the website from the `TASK [Show website address]` TASK in the Job Out put.
    
DONE! Now you know who to perform the basics of creating a Ansible playbook. Pretty easy :) 🥳  [Return home! ](index.md) 
