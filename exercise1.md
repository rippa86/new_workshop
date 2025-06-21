# [cite_start]Exercise 1: Getting started with AAP and Git 

## Step 1: Setting up Git.

[cite_start]When you open Gitea, you will see a project under the `student1` dashboard. [cite_start]Let's start by following good Git practices and creating a new Branch called `development`.

To create this branch, simply:
1.  [cite_start]Click **Branches** to open the Branches menu.
2.  [cite_start]Then click on the branch icon (looks like a branching tree).
3.  [cite_start]Call the new branch `Development` and press the green **Create Branch** button.

[cite_start]To view the new branch, click the `< > code` tab and select branch `Development`.

## [cite_start]Step 2: Creating Credentials in AAP 

[cite_start]Now that Git is set up, we're going to create 2 new credentials in AAP. [cite_start]One credential is to interact with our host, and another is for AAP itself.

1.  [cite_start]Open and Log into AAP with the information provided in the "Ansible Workshop - Ansible for Windows Automation Access Details" page.
2.  [cite_start]Under Resources, click **Credentials** and then press **Add**.
3.  [cite_start]Create a new Credential called `AAP` with the following details:

    | Name | Value |
    | :-------------------------------- | :------------------------------------------- |
    | Name | AAP |
    | Organisation | Default |
    | Credential type | Red Hat Ansible Automation Platform |
    | Red Hat Ansible Automation Platform | URL of Red Hat Ansible Automation platform |
    | Username | Student1 |
    | Password | Your student Password |

4.  [cite_start]Click **Save**.

[cite_start]You should now have a new AAP credential. [cite_start]Next, create another credential by going back into **Credentials** and clicking **Add**.

[cite_start]Create a new Credential called `Windows_host` with the following details:

| Name | Value |
| :---------- | :------------------ |
| Name | Windows_host |
| Organisation | Default |
| Credential type | Machine |
| Username | Student1 |
| Password | Your student Password |

[cite_start]Click **Save** to create the new Credential.

[cite_start]Follow the above steps and create another Credential called `GitTEA` with the following details:

| Name | Value |
| :------------------ | :----------------- |
| Name | GitTEa |
| Organisation | Default |
| Credential type | Source Control |
| Username | Student1 |
| Password | Your student Password |

## [cite_start]Step 3: Creating a new Project 

[cite_start]Now, let's continue and create our new project in AAP. [cite_start]This is where we sync all the content into AAP from our Git repository.

[cite_start]To get things started, let's create the following project:
1.  [cite_start]Open your Gitea URL and copy your Project URL from the tray, ensuring that `HTTPS` is selected.
2.  [cite_start]Under the resources dropdown, click on **Projects**.
3.  [cite_start]You should see a project called "Demo Project". [cite_start]In this screen, click **Add**.
4.  [cite_start]Add in the following details:

    | Name | Value |
    | :-------------------------- | :------------------------ |
    | Name | DEV_windows_workshop |
    | Organisation | Default |
    | Source Control Type | Git |
    | Source Control URL | URL copied from GitTea |
    | Source Control Credential | GitTea |
    | Source Control Branch/Tag/Commit | Development |
    | Update Revision on Launch | Checked |

5.  [cite_start]Click **Save** and your project should start Syncing.
6.  [cite_start]You’ll see a Green Successful box when it's done successfully.

DONE! [cite_start]You’ve now mastered Setting up Creds and Projects in AAP.