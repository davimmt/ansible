## Examples summary
- [Adding user to development and homologation environments](#adding-user-to-development-and-homologation-environments)
- [Removing user from all environments](#removing-user-from-all-environments)
- [Checking if user exists](#checking-existence-of-user)
---
## General
  ### Environments
    - development
    - homologation
    - production

  ### Logical groups
    - development
    - homologation
    - production

  ### Options
    - add.yml
    - del.yml
    - check.yml

## Checking inventory
  ### All of it
    ansible-inventory -i inventories/ --graph
  
  ### Per environment
    ansible-inventory -i inventories/{environment} --graph
---
## Managing users
  ---
  ### Notes
  - `add.yml, del.yml` 
    - You must specify the user attributes in the `roles/{add,del}/defaults/main.yml`.
  - `check.yml`
    - You can only check the existence of one user per call;
    - No need to specify user attributes in any files, instead pass the user's name when calling the playbook as --extra-vars.
  ---

  ### Adding/deleting on all environments
    ansible-playbook --key-file {/path/to/private_ssh_key.pem} -i inventories/ {add.yml,del.yml}

  ### Adding/deleting per environment
    ansible-playbook --key-file {/path/to/private_ssh_key.pem} -i inventories/{environment} {add.yml,del.yml}

  ### Adding/deleting per logical group
    ansible-playbook --key-file {/path/to/private_ssh_key.pem} -i inventories/ -l {logical_group,...} {add.yml,del.yml}

  ### Adding/deleting per host name or logical group
    ansible-playbook --key-file {/path/to/private_ssh_key.pem} -i inventories/ -e "cli_hosts={host_name/logical_group,...}" {add.yml,del.yml}

  ### Checking user existence on all environemnts
    ansible-playbook --key-file {/path/to/private_ssh_key.pem} -i inventories/ check.yml -e "cli_name={user_name}"

  ### Checking user existence per environment
    ansible-playbook --key-file {/path/to/private_ssh_key.pem} -i inventories/{environment} check.yml -e "cli_name={user_name}"

  ### Checking user existence per logical group
    ansible-playbook --key-file {/path/to/private_ssh_key.pem} -i inventories/ -l {logical_group,...} check.yml -e "cli_name={user_name}"

  ### Checking user existence per host name or logical group
    ansible-playbook --key-file {/path/to/private_ssh_key.pem} -i inventories/ check.yml -e "cli_hosts={host_name/logical_group,...} cli_name={user_name}"

## Examples
  ### Adding user to development and homologation environments
  #### Using logical groups with --limit
    ansible-playbook --key-file ../ansible.pem -i inventories/ -l development,homologation add.yml

  #### Using logical groups with --extra-vars
    ansible-playbook --key-file ../ansible.pem -i inventories/ -e "cli_hosts=development,homologation" add.yml

  #### Using pure inventory
    ansible-playbook --key-file ../ansible.pem -i inventories/development add.yml; \
    ansible-playbook --key-file ../ansible.pem -i inventories/homologation add.yml

  #### Using host names with --extra-vars
    ansible-playbook --key-file ../ansible.pem -i inventories/ -e "cli_hosts=dev_centos8,hom_centos8" add.yml

  ### Removing user from all environments
    ansible-playbook --key-file ../ansible.pem -i inventories/ del.yml
  
  ### Checking existence of user
  #### On all environments
    ansible-playbook --key-file ../ansible.pem -i inventories/ check.yml -e "cli_name=ansible_child"
  
  #### On production environment only with pure inventory
    ansible-playbook --key-file ../ansible.pem -i inventories/production check.yml -e "cli_name=ansible_child"
      
  #### On production environment only with logical group
    ansible-playbook --key-file ../ansible.pem -i inventories/ -l production check.yml -e "cli_name=ansible_child"

  #### On specifics hosts (works with logical groups)
    ansible-playbook --key-file ../ansible.pem -i inventories/ check.yml -e "cli_hosts=dev_nginx,hom_nginx cli_name=ansible_child"