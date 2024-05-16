# Sudo

Sudo System Role

## Requirements

This role is only supported on RHEL8.1+/CentOS8.1+ and Fedora distributions. Consider reading fapolicyd documentation before setting it up.

### Collection requirements

None.

## Role Variables

The defaults defined for this role are based on a default RHEL7.6 `/etc/sudoers` configuration.  Please check the defaults in [`defaults/main.yml`](defaults/main.yml) prior to running for OS compatibility.

| Variable Name | Description | Default Value | Variable Type |
| --- | --- | :---: | :---: |
| sudo_rewrite_default_sudoers_file | Use role default or user defined `sudoers_files` definition, replacing your distribution supplied `/etc/sudoers` file.  Useful when attempting to deploy new configuration files to the `include_directories` and you do not wish to modify the `/etc/sudoers` file. | True | boolean |
| sudo_remove_unauthorized_included_files | ***Very Dangerous!*** Each existing sudoer file found in the `include_directories` dictionary which have not been defined in `sudoers_files` will be removed. This allows for enforcing a desired state. | False | boolean |
| sudo_visudo_path | Fully-qualified path to the `visudo` binary required for validation of sudoers configuration changes. Added for Operating System compatibility. | "/usr/sbin/visudo" | string |
| sudo_sudoers_files | Definition of all your sudoers configurations | see [defaults/main.yml](defaults/main.yml)| list of dictionaries |

## sudo_sudoers_files Dictionary Fields

| Variable Name | Description | Variable Type |
| --- | --- | :---: |
| path | Where to deploy the configuration file to on the filesystem. | string |
| aliases | Optional definition of `cmnd_alias`, `host_alias`, `runas_alias`, or `user_alias` items. | dictionary |
| defaults | This allows you to define the defaults of your sudoers configuration. Default overrides can be perfomed via the [`user_specifications`](#default-override-user_specifications) key.| list |
| include_files | Optional specific files that you would like your configuration to include.  This is a list of fully-qualified paths to include via the `#include` option of a sudoers configuration. | list |
| include_directories | Optional specific directories that you would like your configurations to include.  This is a list of fully-qualified paths to directories to include via the `#includedir` option of a sudoers configuration. | list |
| user_specifications | List of user specifications and default overrides to apply to a sudoers file configuration. | list |

### sudo_sudoers_files.aliases Dictionary Fields

| Variable Name | Description | Variable Type |
| --- | --- | :---: |
| cmnd_alias | List of command alias definitions. | list of dictionaries |
| host_alias | List of host alias definitions | list of dictionaries |
| runas_alias | List of runas alias definitions | list of dictionaries |
| user_alias | List of user alias definitions | list of dictionaries |

#### cmnd_alias Dictionary Fields

| Variable Name | Description | Variable Type |
| --- | --- | :---: |
| name | Name of the command alias. | string |
| commands | List of commands to apply to the alias | list |

#### host_alias Dictionary Fields

| Variable Name | Description | Variable Type |
| --- | --- | :---: |
| name | Name of the host alias. | string |
| hosts | List of hosts to apply to the alias | list |

#### runas_alias Dictionary Fields

| Variable Name | Description | Variable Type |
| --- | --- | :---: |
| name | Name of the runas alias | string |
| users | List of users to apply to the alias | list |

#### user_alias Dictionary Fields

| Variable Name | Description | Variable Type |
| --- | --- | :---: |
| name | Name of the user_alias | string |
| users | List of users to apply to the alias | list |

### user_specifications Dictionary Fields

This dictionary can be used to assign either user specifications or default overrides.

#### Standard user_specifications

| Variable Name | Description | Variable Type |
| --- | --- | :---: |
| users | List of users to apply the specification to. You can use a `user_alias` name as well as user names. | list |
| hosts | List of hosts to apply the specification to.  You can use a defined `host_alias` name as well as host names. | list |
| operators | List of operators to apply the specification to. You can use a defined `runas_alias` name as well as user names. | list |
| selinux_role | Optional selinux role to apply to the specification | list |
| selinux_type | Optional selinux type to apply to the specification | list |
| solaris_privs | Optional Solaris privset to apply to the specification | list |
| solaris_limitprivs | Optional Solaris privset to apply to the specification | list |
| tags | Optional list of tags to apply to the specification. | list |
| commands | List of commands to apply the specification to.  You can use a defined `cmnd_alias` name as well as commands. | list |

#### Default Override user_specifications

| Variable Name | Description | Variable Type |
| --- | --- | :---: |
| defaults | List of defaults to override from the main configuration | list |
| type | Type of default to override, this affects the operator in the configuration ( host -> `@`, user -> `:`, command -> `!`, and runas -> `>`).  The type field can be one of the following values: `command`, `host`, `runas`, or `user`. | string |
| commands | Use when `type: "command"`.  List of `cmnd_alias` names as well as commands to override specific default values.| list |
| hosts | Use when `type: "host"`.  List of `host_alias` names as well as individual host names to override specific default values. | list |
| operators | Use when `type: "runas"`. List of `runas_alias` names as well as individual user names to override specific default values. | list |
| users | Use when `type: "user"`.  List of `user_alias` names as well as individual user names to override specific default values. | list |

## Example Playbook

```yaml
# SPDX-License-Identifier: MIT
---
- name: "Apply a RHEL Default /etc/sudoers configuration"
  hosts: "all"
  roles:
    - role: "linux-system-roles.sudo"
```

```yaml
# SPDX-License-Identifier: MIT
---
- name: "Apply custom /etc/sudoers configuration"
  hosts: "all"
  vars:
    sudoers_files:
      - path: "/etc/sudoers"
        user_specifications:
          - users:
            - "root"
            hosts:
              - "x"
            commands:
              - "/usr/bin/ls"
  roles:
    - role: "linux-system-roles.sudo"
```

```yaml
# SPDX-License-Identifier: MIT
---
- name: "Apply a RHEL Default /etc/sudoers configuration"
  hosts: "all"
  vars:
    sudoers_files:
      - path: "/etc/sudoers"
        defaults:
          - "!visiblepw"
          - "always_set_home"
          - "match_group_by_gid"
          - "always_query_group_plugin"
          - "env_reset"
          - secure_path:
            - "/sbin"
            - "/bin"
            - "/usr/sbin"
            - "/usr/bin"
          - env_keep:
            - "COLORS"
            - "DISPLAY"
            - "HOSTNAME"
            - "HISTSIZE"
            - "KDEDIR"
            - "LS_COLORS"
            - "MAIL"
            - "PS1"
            - "PS2"
            - "QTDIR"
            - "USERNAME"
            - "LANG"
            - "LC_ADDRESS"
            - "LC_CTYPE"
            - "LC_COLLATE"
            - "LC_IDENTIFICATION"
            - "LC_MEASUREMENT"
            - "LC_MESSAGES"
            - "LC_MONETARY"
            - "LC_NAME"
            - "LC_NUMERIC"
            - "LC_PAPER"
            - "LC_TELEPHONE"
            - "LC_TIME"
            - "LC_ALL"
            - "LANGUAGE"
            - "LINGUAS"
            - "_XKB_CHARSET"
            - "XAUTHORITY"
    user_specifications:
      - users:
          - "root"
        hosts:
          - "ALL"
        operators:
          - "ALL"
        commands:
          - "ALL"
      - users:
          - "%wheel"
        hosts:
          - "ALL"
        operators:
          - "ALL"
        commands:
          - "ALL"
    include_directories:
      - "/etc/sudoers.d"
  roles:
    - role: "linux-system-roles.sudo"
```

```yaml
# SPDX-License-Identifier: MIT
---
- name: "Apply a multi-file sudoers configuration"
  hosts: "all"
  tasks:
    - name: "Configure /etc/sudoers and included files"
      include_role:
        name: "linux-system-roles.sudo"
      vars:
        sudoers_rewrite_default_sudoers_file: True
        sudoers_remove_unauthorized_included_files: True
        sudoers_backup: True
        sudoers_backup_path: "sudoers-backups"
        sudoers_files:
          - path: "/etc/sudoers"
            defaults:
              - "!visiblepw"
              - "always_set_home"
              - "match_group_by_gid"
              - "always_query_group_plugin"
              - "env_reset"
              - secure_path:
                  - "/sbin"
                  - "/bin"
                  - "/usr/sbin"
                  - "/usr/bin"
              - env_keep:
                  - "COLORS"
                  - "DISPLAY"
                  - "HOSTNAME"
                  - "HISTSIZE"
                  - "KDEDIR"
                  - "LS_COLORS"
                  - "MAIL"
                  - "PS1"
                  - "PS2"
                  - "QTDIR"
                  - "USERNAME"
                  - "LANG"
                  - "LC_ADDRESS"
                  - "LC_CTYPE"
                  - "LC_COLLATE"
                  - "LC_IDENTIFICATION"
                  - "LC_MEASUREMENT"
                  - "LC_MESSAGES"
                  - "LC_MONETARY"
                  - "LC_NAME"
                  - "LC_NUMERIC"
                  - "LC_PAPER"
                  - "LC_TELEPHONE"
                  - "LC_TIME"
                  - "LC_ALL"
                  - "LANGUAGE"
                  - "LINGUAS"
                  - "_XKB_CHARSET"
                  - "XAUTHORITY"
            user_specifications:
              - users:
                  - "root"
                hosts:
                  - "ALL"
                operators:
                  - "ALL"
                commands:
                  - "ALL"
              - users:
                  - "%wheel"
                hosts:
                  - "ALL"
                operators:
                  - "ALL"
                commands:
                  - "ALL"
            include_directories:
              - "/etc/sudoers.d"
            aliases:
              cmnd_alias:
                - name: "PING"
                  commands:
                    - "/bin/ping"
              user_alias:
                - name: "PINGERS"
                  users:
                    - "ahuffman"
          - path: "/etc/sudoers.d/pingers"
            user_specifications:
              - type: "user"
                defaults:
                  - "!requiretty"
                users:
                  - "PINGERS"
          - path: "/etc/sudoers.d/root"
            defaults:
              - "syslog=auth"
            user_specifications:
              - type: "runas"
                defaults:
                  - "!set_logname"
                operators:
                  - "root"
```

## License

MIT

## Author Information

Radovan Sroka @rsroka
