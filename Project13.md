# Ansible Dynamic Assignments (Include) and Community Roles

## Introducing Dynamic Assignment

- Create a new branch in the `https://github.com/<name>/ansible-config-mgt` GitHub repository and name it `dynamic-assignment`
- Within `ansible-config-mgt` folder, create a new folder and name it `dynamic-assignments`. Also create a new file with the `dynamic-assignments` and name it `env-vars.yml`. 
This playbook will be referenced in `site.yml` later using `include`.
- Create a new role folder either manually or using `ansible-galaxy init`. The updated project folder structure should look like:

  ![tree](https://user-images.githubusercontent.com/30922643/115919656-09f14a80-a471-11eb-91b8-763a5f974b09.PNG)

- Create a folder `env-vars` to set values to variables per specific environment since the same Ansible would be used to configure multiple environments, and each of these environments will have certain unique attributes, such as servername, ip-address etc.
- Then for each environment, create new YAML files to set variables. 

![env-var tree](https://user-images.githubusercontent.com/30922643/115921530-84bb6500-a473-11eb-9378-6572ab4c4ecf.PNG)

- Input the instruction below into the env-vars.yml file.

      ---
      - name: collate variables from env specific file, if it exists
        include_vars: "{{ item }}"
        with_first_found:
          - "{{ playbook_dir }}/../env_vars/{{ "{{ inventory_file }}.yml"
          - "{{ playbook_dir }}/../env_vars/default.yml"
        tags:
          - always

![env-var ](https://user-images.githubusercontent.com/30922643/115923380-0dd39b80-a476-11eb-937e-a7f1040f296a.PNG)

***Note**: Special variables `{{ playbook_dir }}` will help Ansible to determine the location of the running playbook, and from there navigate to other path on the filesystem. `{{ inventory_file }}` on the other hand will dynamically resolve to the name of the inventory file being used, then append .yml so that it picks up the required file within the env-vars folder.
Also, `with_first_found` implies that, looping through the list of files, the first one found is used. This is important to always set default values in case an environment specific env file does not exist.*

## Update site.yml with dynamic assignments

- Update site.yml file to make use of the dynamic assignment.

      ---
      - name: Include dynamic variables 
        hosts: all
        tasks:
          - import_playbook: ../static-assignments/common.yml 
          - include_playbook: ../dynamic-assignments/env-vars.yml
        tags:
          - always

      - name: Webserver assignment
        hosts: webservers
          - import_playbook: ../static-assignments/webservers.yml

## Setting up Mysql Ansible Role

- Install Mysql Ansible Role 

      ansible-galaxy install geerlingguy.mysql 
      
- Rename the folder to mysql

      mv geerlingguy.mysql/ mysql
      
- Read `README.md` file, and edit roles configuration to use correct credentials for MySQL required for the tooling website.
 ![mysql-file](https://user-images.githubusercontent.com/30922643/115928087-7b36fa80-a47d-11eb-8a65-9dc4e9f26693.PNG)

- upload the changes into GitHub:

      git add .
      git commit -m "Commit new role files into GitHub"
      git push --set-upstream origin role-feature
 
 - Now, if satisfied with the codes, Create a Pull Request and merge it to main branch on GitHub.

## Setting up Load Balancer roles

