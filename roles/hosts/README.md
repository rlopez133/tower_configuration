# controller_configuration.hosts
## Description
An Ansible Role to add hosts on Ansible Controller.

## Requirements
ansible-galaxy collection install  -r tests/collections/requirements.yml to be installed
Currently:
  awx.awx
  or
  ansible.controller

## Variables

### Authentication
|Variable Name|Default Value|Required|Description|Example|
|:---:|:---:|:---:|:---:|:---:|
|`controller_state`|"present"|no|The state all objects will take unless overriden by object default|'absent'|
|`controller_hostname`|""|yes|URL to the Ansible Controller Server.|127.0.0.1|
|`controller_validate_certs`|`True`|no|Whether or not to validate the Ansible Controller Server's SSL certificate.||
|`controller_username`|""|yes|Admin User on the Ansible Controller Server.||
|`controller_password`|""|yes|Controller Admin User's password on the Ansible Controller Server.  This should be stored in an Ansible Vault at vars/controller-secrets.yml or elsewhere and called from a parent playbook.||
|`controller_oauthtoken`|""|yes|Controller Admin User's token on the Ansible Controller Server.  This should be stored in an Ansible Vault at or elsewhere and called from a parent playbook.||
|`controller_hosts`|`see below`|yes|Data structure describing your host entries described below.||

### Secure Logging Variables
The following Variables compliment each other.
If Both variables are not set, secure logging defaults to false.
The role defaults to False as normally the add host task does not include sensitive information.
`controller_configuration_host_secure_logging` defaults to the value of `controller_configuration_secure_logging` if it is not explicitly called. This allows for secure logging to be toggled for the entire suite of configuration roles with a single variable, or for the user to selectively use it.

|Variable Name|Default Value|Required|Description|
|:---:|:---:|:---:|:---:|
|`controller_configuration_host_secure_logging`|`False`|no|Whether or not to include the sensitive host role tasks in the log.  Set this value to `True` if you will be providing your sensitive values from elsewhere.|
|`controller_configuration_secure_logging`|`False`|no|This variable enables secure logging as well, but is shared accross multiple roles, see above.|

### Formating Variables
Variables can use a standard Jinja templating format to describe the resource.

Example:
```json
{{ variable }}
```

Because of this it is difficult to provide controller with the required format for these fields.

The workaround is to use the following format:
```json
{  { variable }}
```
The role will strip the double space between the curly bracket in order to provide controller with the correct format for the Variables.

## Data Structure
### Variables
|Variable Name|Default Value|Required|Type|Description|
|:---:|:---:|:---:|:---:|:---:|
|`name`|""|yes|str|The name of the host.|
|`new_name`|""|yes|str|To use when changing a hosts's name.|
|`description`|""|no|str|The description of the host.|
|`inventory`|""|yes|str|The inventory the host applies against.|
|`enabled`|`True`|no|bool|If the host should be enabled.|
|`variables`|{}|no|str|The variables applicable to the host.|
|`state`|`present`|no|str|Desired state of the resource.|

### Standard host Data Structure
#### Json Example
```json
{
  "controller_host": [
    {
      "name": "localhost",
      "inventory": "My Inv",
      "variables": {
        "my_var": true
      }
    }
  ]
}
```
#### Yaml Example
```yaml
---
controller_hosts:
  - name: localhost
    inventory: localhost
    variables:
      some_var: some_val
      ansible_connection: local
```

## Playbook Examples
### Standard Role Usage
```yaml
---
- name: Playbook to configure ansible controller post installation
  hosts: localhost
  connection: local
  # Define following vars here, or in controller_configs/controller_auth.yml
  # controller_hostname: ansible-controller-web-svc-test-project.example.com
  # controller_username: admin
  # controller_password: changeme
  pre_tasks:
    - name: Include vars from controller_configs directory
      include_vars:
        dir: ./yaml
        ignore_files: [controller_config.yml.template]
        extensions: ["yml"]
  roles:
    - {role: redhat_cop.controller_configuration.hosts, when: controller_hosts is defined}
```
## License
[MIT](LICENSE)

## Author
[Tom Page](https://github.com/Tompage1994)
