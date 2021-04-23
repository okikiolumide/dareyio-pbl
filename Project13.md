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

