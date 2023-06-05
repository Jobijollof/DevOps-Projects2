
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

Now paste the instruction below into the env-vars.yml file.

```
---
- name: collate variables from env specific file, if it exists
  hosts: all
  tasks:
```
