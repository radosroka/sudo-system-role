# Sudo

Sudo System Role 

## Requirements

This role is only supported on RHEL8.1+/CentOS8.1+ and Fedora distributions. Consider reading fapolicyd documentation before setting it up.

### Collection requirements

None.

## Role Variables

### sudoers_rewrite_default_sudoers_file

Default `True` - if set to `True` the variable ensures the role will rewrite default sudoers file.

### sudoers_remove_unauthorized_included_files

Default `False` - if set to `True` the variable ensures the role will remove other config files from sudoers.d/ which are not handled by this role.


### sudoers_backup

Default `True` - if set to `True` the variable ensures the role will backup all sudo configuration files to backup directory `sudoers_backup_path`. 

### sudoers_backup_path

Default `sudoers-backup` - the name of directory placed in relative execution path.


### sudoers_backup_become

Default `True` - if set to `True` the variable ensures the role ...

### sudoers_visudo_path

Default `/usr/bin/visudo` - path to visudo utility.

### sudoers_files

#### path

#### defaults

##### secure_path

##### env_keep

#### user_specifications

##### users

###### hosts

###### operators

###### commands

#### include_direcories
#### include_files
#### aliases



## Example Playbook


```
---
- name: Example sudo role invocation
  hosts: all
  roles:
    - fapolicyd
```



## License

MIT

## Author Information

Radovan Sroka @rsroka

