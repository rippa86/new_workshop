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
