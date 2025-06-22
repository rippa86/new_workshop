# Exercise 1: Running a Ad-Hoc Command. 

Running a Ad-Hoc command in AAP is a simply way to test connectivity to a host. I *wouldn't* be using it for any other method. For this workshop, we have already added your host inside the inventory, so you don't have to do anything. But its worthwhile exploring more about it.

## Step 1: Checking our our Investory

On our AAP host you can see review our inventorys by:

1. Under **Resouces** click **Inventoryies**. There should be Demo and `Workshop Inventory`. Ignore the disabled Sync. We're not syncing this with somthing like ServiceNow.
2. Click ** Workshop Inventory** to view.
3. In here, we can provide a list of variables that relate to your hosts. These will be applied to every play attached. For example if you use a different connection method or port, you can set these details here.
4. if you click **Groups** you will see a list of Groups allocated to this inventory. A group is a list of common hosts. For example Frontend, Backend, database, ect ect. if you click **Windows** to open the group of our Lab Envionment
5. In this details screen you can see a list of variables we are using to connect to these hosts. In this Lab we're using `WINRM over HTTPS` to automate against. Other supported options could be openSSH.
6. Now click **Hosts** to review our happy little Windows Server called `student1-win11`. Lets run a command to it to test our connectivity. To do so
7. **Check** the **Student1-win1** host and press **Run Command** button. This should open a new window.
8. Under Module select **Win_Ping** and press **Next**
9. Select **Default Execution Envrionment** and press **Next**
10. Select the **Windows_host** credential we created in Exercise1. and press **Next** then push **Launch**
11. if Everything is sorted you should get a pong back from ther server :)
    ```JSON
    student1-win1 | SUCCESS => {
    "changed": false,
    "ping": "pong" }

## Step 2: Returning Facts about our host. 
This one is pretty nifty, and I would recommend using it if you want to find out details about a host and information you can utilise later.
1. Select **Inventories** again, then **Windows** and click the **Hosts** tab.
2. Check **student1-win1** and press **Run Command** like we did before.
3. For Module select **setup** from the drop down and press **Next** and in **Windows_host** credential and press next until  we can click **Launch**
4. Once launced, (you may need to push refresh, on the `output` screen, you should see a list of ansible-facts about the host (We'll learn more about these later.
5. if you click on the result, it should open a new window. push the **JSON** TAB to review the full list of details returned. it should look like this: 
   ```JSON
   "ansible_facts": {
    "ansible_ip_addresses": [
      "fe80::2d0e:54fb:4cf7:f2c0%4",
      "192.168.0.228",
      "2001:0:34f1:8072:34ee:213f:3f57:ff1b",
      "fe80::34ee:213f:3f57:ff1b%8",
      "fe80::5efe:192.168.0.228%5"
    ],
    "ansible_windows_domain_role": "Stand-alone server",
   

DONE! Now you know how to use AD-Hoc commands, please don't.  [Return home! ](index.md) 
