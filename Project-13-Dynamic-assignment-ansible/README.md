
# ANSIBLE DYNAMIC ASSIGNMENTS (INCLUDE) AND COMMUNITY ROLES

In Ansible, there are two ways to include external files or other playbooks: the import statement which we learnt in [Project 12](https://github.com/Jobijollof/DevOps-Projects2/tree/main/Project12-Ansible-Refactoring) and the include statement, which we will execute in this project. These statements allow you to reuse existing code, break down your playbooks into smaller parts, and organize your Ansible configuration more effectively.

### Import statement:

The import statement is used to incorporate external files or playbooks into the main playbook. It includes the content of the imported file directly into the main playbook at the point where the statement is used. The imported content is treated as if it was written in place.

Here's an example of using the import statement:

```
yaml
Copy code
- hosts: servers
  tasks:
    - name: Include tasks from another playbook
      import_playbook: path/to/other-playbook.yml

    - name: Perform other tasks in the main playbook
      # Additional tasks go here

```
In this example, the tasks defined in the other-playbook.yml file will be included in the main playbook at the specified location. Once the imported tasks are executed, the execution flow continues with the remaining tasks in the main playbook.

### Include statement:

The include statement is used to include external files or playbooks into the main playbook. However, unlike the import statement, the include statement treats the included content as a separate entity that is executed independently. The included file or playbook is processed as if it were a separate playbook.
Here's an example of using the include statement:

```
yaml
Copy code
- hosts: servers
  tasks:
    - name: Include tasks from another playbook
      include: path/to/other-playbook.yml

    - name: Perform other tasks in the main playbook
      # Additional tasks go here

```      
In this example, the tasks defined in the other-playbook.yml file will be executed separately as if it was a stand alone playbook. Once the included tasks are executed, the execution flow continues with the remaining tasks in the main playbook.

Both import and include statements provide flexibility and reusability in Ansible playbooks. You can choose the appropriate statement based on whether you want the included content to be treated as part of the main playbook (import) or as a separate playbook (include).

```
import = Static
include = Dynamic

```

In this case, think of import statements as a way to bring in specific tasks or instructions that you can directly follow. It's like having a set of step-by-step guides for different parts of the party. For example, you might have a guide on how to decorate the venue, another guide on how to prepare the food, and so on. With import statements, you can easily include these guides into your overall plan and execute them when needed.

On the other hand, include statements are more like adding separate sections to your overall plan. Instead of following specific instructions right away, you include entire sections that describe different aspects of the party. For instance, you might have a section dedicated to entertainment, another section for managing the guest list, and so forth. These sections can be included in your plan, and you can decide when to execute them based on the overall flow of the party preparations.

In summary, import statements allow you to directly bring in specific instructions or tasks that you can execute as part of your plan, while include statements let you include entire sections that describe different parts of your plan, allowing for better organization and flexibility in the overall process.


# INTRODUCING DYNAMIC ASSIGNMENT INTO OUR STRUCTURE


In your https://github.com/<your-name>/ansible-config-mgt GitHub repository start a new branch and call it dynamic-assignments.

```
git checkout -b dynamic-assignments

```

![act](./images/git-checkout.png)


Create a new folder, name it dynamic-assignments. Then inside this folder, create a new file and name it env-vars.yml. We will instruct site.yml to include this playbook later. For now, let us keep building up the structure.

Your GitHub shall have following structure by now.

Note: Depending on what method you used in the previous project you may have or not have roles folder in your GitHub repository – if you used ansible-galaxy, then roles directory was only created on your Jenkins-Ansible server locally. It is recommended to have all the codes managed and tracked in GitHub, so you might want to recreate this structure manually in this case – it is up to you.

```
├── dynamic-assignments
│   └── env-vars.yml
├── inventory
│   └── dev
    └── stage
    └── uat
    └── prod
└── playbooks
    └── site.yml
└── roles (optional folder)
    └──...(optional subfolders & files)
└── static-assignments
    └── common.yml

```   



Since we will be using the same Ansible to configure multiple environments, and each of these environments will have certain unique attributes, such as servername, ip-address etc., we will need a way to set values to variables per specific environment.
For this reason, we will now create a folder to keep each environment’s variables file. Therefore, create a new folder env-vars, then for each environment, create new YAML files which we will use to set variables.

In Ansible, the files with extensions like .yml or .yaml are commonly used to define playbooks and configurations. The files dev.yml, prod.yml, stage.yml, and uat.yml, correspond to different environments in a deployment pipeline, such as development (dev), production (prod), staging (stage), and user acceptance testing (UAT).

These YAML files are typically used to define variables, roles, tasks, and configurations specific to each environment. They allow you to manage and deploy your infrastructure and applications consistently across multiple environments with different settings. Let's look at the significance of each file:

- dev.yml: This file contains the playbook or configuration specific to the development environment. It may include variables, roles, tasks, and other settings required for deploying and managing your application in the development environment.

- prod.yml: The prod.yml file is used for the production environment. It includes the playbook or configuration tailored for deploying your application in a production-ready manner. It may have different settings compared to the development environment, such as production database connections, optimized performance configurations, and stricter security measures.

- stage.yml: This file corresponds to the staging environment, which is often used to mimic the production environment closely. Staging environments allow you to test and validate your application in an environment that closely resembles the production setup. The stage.yml file contains the playbook or configuration for the staging environment, including specific variables, tasks, and configurations relevant to staging.

- uat.yml: UAT stands for User Acceptance Testing, which is performed to ensure that the application meets the requirements and expectations of end-users. The uat.yml file contains the playbook or configuration specific to the UAT environment. It may include tasks and configurations necessary for testing the application in a controlled UAT environment.

Your layout should now look like this.

```

├── dynamic-assignments
│   └── env-vars.yml
├── env-vars
    └── dev.yml
    └── stage.yml
    └── uat.yml
    └── prod.yml
├── inventory
    └── dev
    └── stage
    └── uat
    └── prod
├── playbooks
    └── site.yml
└── static-assignments
    └── common.yml
    └── webservers.yml

```

![act](./images/new-file%20structure.png)

![png](./images/new-filestucture2.png)



Now paste the instruction below into the `env-vars.yml` file.


```
---
- name: collate variables from env specific file, if it exists
  hosts: all
  tasks:
    - name: looping through list of available files
      include_vars: "{{ item }}"
      with_first_found:
        - files:
            - dev.yml
            - stage.yml
            - prod.yml
            - uat.yml
          paths:
            - "{{ playbook_dir }}/../env-vars"
      tags:
        - always

```


![act](./images/code-env-vars.png)



Notice 3 things to note here:

We used include_vars syntax instead of include, this is because Ansible developers decided to separate different features of the module.

From Ansible version 2.8, the include module is deprecated and variants of include_* must be used. These are:

include_role (https://docs.ansible.com/ansible/latest/collections/ansible/builtin/include_role_module.html#include-role-module)

include_tasks (https://docs.ansible.com/ansible/latest/collections/ansible/builtin/include_tasks_module.html#include-tasks-module)

include_vars (https://docs.ansible.com/ansible/latest/collections/ansible/builtin/include_vars_module.html#include-vars-module)

In the same version, variants of import were also introduces, such as:

import_role (https://docs.ansible.com/ansible/latest/collections/ansible/builtin/import_role_module.html#import-role-module)

import_tasks(https://docs.ansible.com/ansible/latest/collections/ansible/builtin/import_tasks_module.html#import-tasks-module)

We made use of (special variables)[https://docs.ansible.com/ansible/latest/reference_appendices/special_variables.html] `{{ playbook_dir }}` and `{{ inventory_file }}`. `{{ playbook_dir }}` will help Ansible to determine the location of the running playbook, and from there navigate to other path on the filesystem.

`{{ inventory_file }}` on the other hand will dynamically resolve to the name of the inventory file being used, then append .yml so that it picks up the required file within the env-vars folder.

We are including the variables using a loop. with_first_found implies that, looping through the list of files, the first one found is used. This is good so that we can always set default values in case an environment specific env file does not exist.

### Update `site.yml` file to use of the dynamic assignment (At this point, we cannot test it yet).