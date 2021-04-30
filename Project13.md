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
      
   ![apache](https://user-images.githubusercontent.com/30922643/116758578-47714d00-aa08-11eb-8ffe-cbbaf93dee08.PNG)
![ansible-galaxy-mysql](https://user-images.githubusercontent.com/30922643/116758499-12fd9100-aa08-11eb-8b9f-2a185c3c3514.PNG)

- Rename the folder to mysql

      mv geerlingguy.mysql/ mysql
      
- Read `README.md` file, and edit roles configuration to use correct credentials for MySQL required for the tooling website.
 ![mysql-file](https://user-images.githubusercontent.com/30922643/115928087-7b36fa80-a47d-11eb-8a65-9dc4e9f26693.PNG)
![mysql-pb-log](https://user-images.githubusercontent.com/30922643/116758675-7e476300-aa08-11eb-8f45-77808e2a297e.PNG)
![mysql-db-status-after-pb-2](https://user-images.githubusercontent.com/30922643/116758700-88696180-aa08-11eb-8e5b-33c714cdbb92.PNG)



- upload the changes into GitHub:

      git add .
      git commit -m "Commit new role files into GitHub"
      git push --set-upstream origin role-feature
 
 - Now, if satisfied with the codes, Create a Pull Request and merge it to main branch on GitHub.

## Setting up Load Balancer roles

- Setup to roles so that `Nginx` or `Apache` can be selected as the load balancer
- Install Nginx Ansible Role

       ansible-galaxy install geerlingguy.nginx or ansible-galaxy collection install nginxinc.nginx_core

- Rename the folder to nginx

       mv geerlingguy.nginx/ nginx
       
- Install Apache Ansible Role

       ansible-galaxy install geerlingguy.apache 
       
    ![apache](https://user-images.githubusercontent.com/30922643/116758614-58ba5980-aa08-11eb-9139-243225eb3946.PNG)


- Rename the folder to apache

       mv geerlingguy.apache/ apache
       
- Update both static-assignment and site.yml files to refer the roles
- Add a condition to enable either one of Apache or Nginx since both cannot be selected at the same time
    -  Declare a variable in `defaults/main.yml` file inside the Nginx and Apache roles and name each variables `enable_nginx_lb` and `enable_apache_lb` respectively.
    -  Set both values to false i.e `enable_nginx_lb: false` and `enable_apache_lb: false`.
    -  Declare another variable in `defaults/main.yml` file inside both roles as `load_balancer_is_required` and set its value to `false`
    
    ![var-apache](https://user-images.githubusercontent.com/30922643/116752264-3bcc5900-a9fd-11eb-87ba-a8bfb4651fe9.PNG)
    ![var-nginx](https://user-images.githubusercontent.com/30922643/116752276-40910d00-a9fd-11eb-871f-395ee5abe98e.PNG)

- Update both assignment and site.yml files as follows
  
 `loadbalancers.yml` file

    - hosts: lb
      roles:
        - { role: nginx, when: enable_nginx_lb and load_balancer_is_required }
        - { role: apache, when: enable_apache_lb and load_balancer_is_required }

`site.yml` file

     - name: Loadbalancers assignment
       hosts: lb
         - import_playbook: ../static-assignments/loadbalancers.yml
        when: load_balancer_is_required  
        
Now make use of `env-vars\uat.yml file` to define which loadbalancer to use in UAT environment by setting respective environmental variable to `true`

- Activate the load balancer, and enable nginx by setting these in the respective environment’s env-vars file.

      enable_nginx_lb: true
      load_balancer_is_required: true

![run-pb](https://user-images.githubusercontent.com/30922643/116758443-f06b7800-aa07-11eb-8c61-3842c770eca1.PNG)
![apache-status](https://user-images.githubusercontent.com/30922643/116758631-6374ee80-aa08-11eb-9c3a-3cc0e78cda08.PNG)


## IMPLEMENTATION VIDEO
https://drive.google.com/drive/folders/12TiOI2OCK6_CWQtohnWj2ta0Db4FKkcL?usp=sharing

## GITHUB BRANCH
https://github.com/okikiolumide/ansible-config-mgt/tree/dynamic-assignments

## BLOCKER
1. Error: `no file was found when using with_first_found....` while running dynamics assignment playbook
>Solution: Used the ignore_error to skip error and run playbook successfully

## RESOURCES
1. https://darey.io
2. https://docs.ansible.com/
3. https://galaxy.ansible.com/
4. https://www.digitalocean.com/
5. https://www.toptechskills.com/ansible-tutorials-courses/ansible-include-import-variables-tutorial-examples/
