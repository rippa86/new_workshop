# Exercise 1: Getting started with AAP and Git 

## Step 1: Setting up Git.

We're going to start by learning some Git best practices. In real life, every project should have 3 branches at a minimum. 
* Feature: What you're building ATM
* Development: Testing / Bug fixing branch used in Dev AAP
* Main (Master): used for Production. (This branch should be Protected, requireing a Pull request and require all testing to be completed and documented from Development Branch.
 
In this Lab, we will be using Development and Main (Master). To get things started: Log into Git tea from the workshop panel, using the user name and password. you will see a project under the `student1` dashboard called "workshop_project. click it to open the project repository. 
Let's start by following good Git practices and creating a new Branch called `development`.

To create this branch, simply:
1.  Click **Branches** to open the Branches menu.
2.  Then click on the branch icon (looks like a branching tree).
3.  Call the new branch `Development` and press the green **Create Branch** button.

To view the new branch, click the `< > code` tab and select branch `Development`.

Great, we have a new empty project in Git. Pretty simple :)

## Step 2: Creating Credentials in AAP 

Now that Git is set up, we're going to create 3 new credentials in AAP. The Credentials are Service accounts we're going to required to authenticate into different parts of our environment. Consisting of: Machine Cred for our Windows Host, AAP Cred for AAP and Source Control for Gitea.

To create these simple do the following: 
1.  Open and Log into AAP with the information provided in the "Ansible Workshop - Ansible for Windows Automation Access Details" page.
2.  Under Resources, click **Credentials** and then press **Add**.
3.  Create a new Credential called `AAP` with the following details:

    | Name | Value |
    | :-------------------------------- | :------------------------------------------- |
    | Name | AAP |
    | Organisation | Default |
    | Credential type | Red Hat Ansible Automation Platform |
    | Red Hat Ansible Automation Platform | URL of Red Hat Ansible Automation platform |
    | Username | Student1 |
    | Password | Your student Password |

4.  Click **Save**.

You should now have a new AAP credential. Next, create another credential by going back into **Credentials** and clicking **Add**.

Create a new Credential called `Windows_host` with the following details:

| Name | Value |
| :---------- | :------------------ |
| Name | Windows_host |
| Organisation | Default |
| Credential type | Machine |
| Username | Student1 |
| Password | Your student Password |

Click **Save** to create the new Credential.

Follow the above steps and create another Credential called `GiTEA` with the following details:

| Name | Value |
| :------------------ | :----------------- |
| Name | GitTEa |
| Organisation | Default |
| Credential type | Source Control |
| Username | Student1 |
| Password | Your student Password |

## Step 3: Creating a new Project 

Now, let's continue and create our new project in AAP. This is where we sync all the content into AAP from our Git repository.

To get things started, let's create the following project:
1.  Open your Gitea URL and copy your Project URL from the tray, ensuring that `HTTPS` is selected.
2.  Under the resources dropdown, click on **Projects**.
3.  You should see a project called "Demo Project". In this screen, click **Add**.
4.  Add in the following details:

    | Name | Value |
    | :-------------------------- | :------------------------ |
    | Name | DEV_windows_workshop |
    | Organisation | Default |
    | Source Control Type | Git |
    | Source Control URL | URL copied from GitTea |
    | Source Control Credential | GitTea |
    | Source Control Branch/Tag/Commit | Development |
    | Update Revision on Launch | Checked |

5.  Click **Save** and your project should start Syncing.
6.  You’ll see a Green Successful box when it's done successfully.

DONE! You’ve now mastered Setting up Creds and Projects in AAP. [Return home! ](index.md) 


