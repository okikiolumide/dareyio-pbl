# Ansible Refactoring & Static Assignments (Imports)

In this project I will continue working with ansible-config-mgt repository and make some improvements to the code. This will inNow you need to refactor your Ansible code, create assignments, and learn how to use the imports functionality. Imports allow to effectively re-use previously created playbooks in a new playbook - it allows you to organize your tasks and reuse them when needed.

## TASKS



### Code Refactoring
Refactoring is a general term in computer programming. It means making changes to the source code without changins expected behaviour of the software. The main idea of refactoring is to enhance code readability, increase maintainability and extensibility, reduce complexity, add proper comments without affecting the logic.

Step 1 - Jenkins job enhancement

- Go to your Jenkins-Ansible server and create a new directory called ansible-config-mgt - we will store there all artifacts after each build.

      mkdir /home/ubuntu/ansible-config-mgt
- Change permissions to this directory, so Jenkins could save files there - 
      
      chmod -R 0777 /home/ubuntu/ansible-config-mgt
- Go to Jenkins web console -> `Manage Jenkins` -> `Manage Plugins` -> on Available tab search for `Copy Artifact` and install this plugin without restarting Jenkins
- Create a new Freestyle project (you have done it in Project 9) and name it `save_artifacts`.
- This project will be triggered by completion of your existing ansible project. Configure it accordingly:

*Note: You can configure number of builds to keep in order to save space on the server, for example, you might want to keep only last 2 or 5 build results. You can also make this change to your ansible job.*

- Test your set up by making some change in README.MD file inside your ansible-config-mgt repository (right inside master branch)
- If both Jenkins jobs have completed one after another - you shall see your files inside /home/ubuntu/ansible-config-mgt directory and it will be updated with every commit to your master branch.

## Step 2 - Refactor Ansible code by importing other playbooks into site.yml

- Within playbooks folder, create a new file and name it site.yml - This file will now be considered as an entry point into the entire infrastructure configuration. Other playbooks will be included here as a reference. In other words, site.yml will become a parent to all other playbooks that will be developed. Including common.yml that you created previously. Dont worry, you will understand more what this means shortly.
- Create a new folder in root of the repository and name it static-assignments. The static-assignments folder is where all other children playbooks will be stored. This is merely for easy organization of your work. It is not an Ansible specific concept, therefore you can choose how you want to organize your work. You will see why the folder name has a prefix of static very soon. For now, just follow along.
- Move common.yml file into the newly created static-assignments folder.
- Inside site.yml file, import common.yml playbook.

      ---
      - hosts: all
      - import_playbook: ../static-assignments/common.yml

Tree

        ├── static-assignments
        │   └── common.yml
        ├── inventory
        └── dev
        └── stage
        └── uat
        └── prod
        └── playbooks
            └── site.yml
            
- Run ansible-playbook command against the dev environment
Since you need to apply some tasks to your dev servers and wireshark is already installed - you can go ahead and create another playbook under static-assignments and name it common-del.yml. In this playbook, configure deletion of wireshark utility.

                        ---
                        - name: update web, nfs and db servers
                          hosts: webservers, nfs, db
                          remote_user: ec2-user
                          become: yes
                          become_user: root
                          tasks:
                          - name: delete wireshark
                            yum:
                              name: wireshark
                              state: removed

                        - name: update LB server
                          hosts: lb
                          remote_user: ubuntu
                          become: yes
                          become_user: root
                          tasks:
                          - name: delete wireshark
                            apt:
                              name: wireshark-qt
                              state: absent
                              autoremove: yes
                              purge: yes
                              autoclean: yes


- update site.yml with - import_playbook: ../static-assignments/common-del.yml instead of common.yml and run it against dev servers:

      ansible-playbook -i /home/ubuntu/ansible-config-mgt/inventory/dev.yml /home/ubuntu/ansible-config-mgt/playbooks/site.yaml

Make sure that wireshark is deleted on all the servers by running wireshark --version

Now you have learned how to use import_playbooks module and you have a ready solution to install/delete packages on multiple servers with just one command.



## GITHUB REPO
https://github.com/okikiolumide/ansible-config-mgt

## PROJECT DEMONSTRATION
https://drive.google.com/drive/folders/1WmrGZhdQPoQBc_FUVaY7nDcQAJh_pwSS?usp=sharing

## BLOCKERS

## RESOURCES
1. https://darey.io
2. https://docs.ansible.com/
3. 


