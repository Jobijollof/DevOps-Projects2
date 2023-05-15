## ANSIBLE REFACTORING AND STATIC ASSIGNMENTS (IMPORTS AND ROLES)

- In this project we will continue working with ansible-config-mgt repository and make some improvements of our code. 

- We are going to refactor our Ansible code, create assignments, and learn how to use the imports functionality. 

- Imports allow to effectively re-use previously created playbooks in a new playbook. 

### Code Refactoring
Refactoring is a general term in computer programming. It means making changes to the source code without changing expected behaviour of the software. The main idea of refactoring is to enhance code readability, increase maintainability and extensibility, reduce complexity, add proper comments without affecting the logic.

In our case, we will move things around a little bit in the code, but the overall state of the infrastructure remains the same.

Let us see how you can improve our Ansible code!

### Step 1 – Jenkins job enhancement

We will begin, by making some changes to our Jenkins job – now every new change in the code creates a separate directory which is not very convenient when we want to run some commands from one place. 
Futhermore, it consumes space on the Jenkins server with subsequent changes. 

We would enhance it by introducing a new Jenkins project/job – we will require Copy Artifact plugin.

In our Jenkins-Ansible server we will create a new directory called ansible-config-artifact – we will store there all artifacts after each build.

`sudo mkdir /home/ubuntu/ansible-config-artifact`

Change permissions to this directory, so Jenkins could save files there – chmod -R 0777 /home/ubuntu/ansible-config-artifact

![jenkins](./images/Ansible-1mkdir.png)

Go to Jenkins web console -> Manage Jenkins -> Manage Plugins -> on Available tab search for 
Copy Artifact and install this plugin without restarting Jenkins
