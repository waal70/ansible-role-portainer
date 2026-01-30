ansible-role-portainer
=======================

Portainer - the coolest UI for Docker http://portainer.io/

This role installs Portainer using Docker container. Adopted by waal70 to perform the following behavior:

- Will (re-)install portainer on the host, if needed
- If role_included with ```stack_list``` specified, deploys the containers from stack_list
- If the container from ```stack_list``` already exists, will update the stack definition (e.g. for setting new environment variables) if ```stack_update``` is true (defaults to true)
- Creates a network ```reverse-proxy``` that may be employed later to link containers in one network

This role has two main modes of operation:

- Installs portainer so that your node now has portainer
- Called from another playbook with a compose.yml, so that this stack may be deployed onto the Portainer instance

Tasks in Role
-------------

- Ensure docker-py is present
- Deploy Portainer container to host [define persistent_data_path]
- Configure Admin user password
- Generate authentication token
- Define endpoints [DICT | list]
- Configure Portainer settings [Jinja2 template]
- Configure registry [Jinja2 template]

Requirements
------------

- `waal70.docker` (Service + python package)

Role variables
--------------

|name | description | default |
|-----|-------------|---------|
| configure_settings  | override default portainer settings with template  | false |
| configure_registry | configure a docker registry for Portainer to use   | false |
| remove_persistent_data | remove the persistent data directory on the host | false |
| remove_existing_container | remove an existing container named 'portainer' | false |
| persistent_data_path | path that will be used to store persistent data | /opt/portainer:/data |
| auth_method | use LDAP or standalone [2 for ldap, 1 for standalone] | 1 |
| registry_type | 1 (Quay.io), 2 (Azure container registry) or 3 (custom registry) | 3 |
| version | portainer version to use at the time of commiting 'develop' is the version that supports LDAP | latest |
| do_stack_update | Whether to re-upload the docker-compose.yml | true |
| env | Along with the compose.yml, you may specify a list of .env (stack.env) | ANSIBLEMARKER |

See main.yml under 'defaults' for a complete list

Running the role
----------------

To 'just' install Portainer on the host:

```yaml
- hosts: thishost
  become: true
  roles:
    - waal70.portainer
```

To install a docker-compose (from another role):

```yaml
- name: "Include role to deploy stacks to Portainer"
  ansible.builtin.include_role:
    name: waal70.portainer
  vars:
    stack_list:
      - name: name_of_stack
        file: docker_compose_for_stack.yml
        env: "{{ dict_var }}"
      - name: stack_wo_env
        file: another_compose.yml
```

License
-------

[GPLv3](https://www.gnu.org/licenses/gpl-3.0.html#license-text)

Author Information
------------------

Unless otherwise noted, this entire repository is (c) 2024 by André (waal70). [See github profile](https://github.com/waal70)

Please contact me if you need a commercial license for any of these files
