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

![artifact](./images/Ansible-2copyartifact.png)


![artifact](./images/ansible-3dwnld.png)

Create a new Freestyle project and name it save_artifacts.
This project will be triggered by completion of your existing ansible project. Configure it accordingly:

![artifact](./images/ansible-4freestyle.png)


![artifact](./images/ansible-5.png)

- Note: You can configure number of builds to keep in order to save space on the server, for example, you might want to keep only last 2 or 5 build results. You can also make this change to your ansible job.

- The main idea of save_artifacts project is to save artifacts into ***/home/ubuntu/ansible-config-artifact*** directory. To achieve this, create a Build step and choose Copy artifacts from other project, specify ansible as a source project and /home/ubuntu/ansible-config-artifact as a target directory.

![artifact](./images/ansible-6.png)

![artifact](./images/ansible-7.png)

![arifact](./images/ansible-8.png)

We are going to test our set up by making some changes in README.MD file inside our ansible-config repository (right inside master branch).

If both Jenkins jobs have completed one after another – you shall see our files inside /home/ubuntu/ansible-config-artifact directory and it will be updated with every commit to our main branch.

![artifact](./images/ansible-9.png)

![artifact](./images/Ansible-10.png)

![artifact](./images/ansible-11.png)


### REFACTOR ANSIBLE CODE BY IMPORTING OTHER PLAYBOOKS INTO SITE.YML
DevOps philosophy implies constant iterative improvement for better efficiency – refactoring is one of the techniques that can be used, but you always have an answer to question "why?". Why do we need to change something if it works well?

[In Project 11](https://github.com/Jobijollof/DevOps-Projects2/tree/main/Project-11%20Ansible-Config) we wrote all tasks in a single playbook common.yml.

Now it is a simple set of instructions for only 2 types of OS, but imagine we have many more tasks and we need to apply this playbook to other servers with different requirements. 
In this case, we will have to read through the whole playbook to check if all tasks written there are applicable and if there  is anything that we would need to add for certain server/OS families.
Very fast, it becomes a tedious exercise and our playbook will become messy with many commented parts. Your DevOps colleagues will not appreciate such organization of your codes and it will be difficult for them to use your playbook.
Most Ansible users learn the one-file approach first. However, breaking tasks up into different files is an excellent way to organize complex sets of tasks and reuse them.

### Execution:

– Refactor Ansible code by importing other playbooks into site.yml
Before starting to refactor the codes,  we would ensure that we have pulled down the latest code from master (main) branch.

- Create a new branch, name it refactor.

Let see code re-use in action by importing other playbooks.

- Within playbooks folder, create a new file and name it site.yml – This file will now be considered as an entry point into the entire infrastructure configuration. Other playbooks will be included here as a reference. In other words, site.yml will become a parent to all other playbooks that will be developed. Including common.yml that was created previously.

![dev](./images/Refactor-1.png)

![dev](./images/refactor-2.png)

- Create a new folder in root of the repository and name it static-assignments. The static-assignments folder is where all other children playbooks will be stored. This is merely for easy organization of your work. It is not an Ansible specific concept, therefore you can choose how you want to organize your work. You will see why the folder name has a prefix of static very soon. For now, just follow along.

![dev](./images/refactor-3.png)


- Move common.yml file into the newly created static-assignments folder.

![dev](./images/refactor-3.png)

- Create another playbook under static-assignments and name it common-del.yml. In this playbook, configure deletion of wireshark utility.

update site.yml with - import_playbook: ../static-assignments/common-del.yml instead of common.yml and run it against dev  servers:

```
cd /home/ubuntu/ansible-config-mgt/


ansible-playbook -i inventory/dev.yml playbooks/site.yaml

```



