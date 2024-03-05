wzzrd.gitea.act
===============

An Ansible role to download, install, configure and run the Gitea fork of act_runner.

Requirements
------------

No explicit requirements.

Role Variables
--------------

| Variable name | Default value | Meaning | Mandatory |
|---------------|---------------|---------|-----------|
| act_install_dependencies | true | Whether or not to install dependencies | false |
| act_user | act_runner | User to run the act_runner process under | false |
| act_user_group | act_runner | Group for the act_user | false |
| act_user_additional_groups | '' | Any additional groups for act_user | false |
| act_user_home | /home/{{ act_user }} | Home directory for the act_user | false |
| act_bin_dir | /usr/local/bin | Location for the act_runner binary | false |
| act_bin_owner | root | Owner of the act_runner binary | false |
| act_bin_owner_group | root | Group of the owner of the act_runner binary | false |
| act_runner_version | "0.2.6" | Version of act_runner to install | false |
| act_download_dir_url | "https://dl.gitea.com/act_runner/{{ act_runner_version }}" |  Download location | false |
| act_config_content | '' | Contents for the `config.yaml` configuration file for act_runner, if left empty, it will be populated with the defaults by the act_runner process itself (i.e. not this role) | false |
| act_gitea_config_path | /etc/gitea/gitea.ini | Location of the Gitea configuration (required for automatic registration) | false |
| act_do_register | false | Whether or not to auto-registre this act_runner with a Gitea instance | false |
| act_scope | '' | Scope for the act_runner, see below | false |
| act_gitea_host | gitea.example.com | Hostname of the Gitea instance to auto-register with | false |
| act_gitea_host_port | 443 | Port of the Gitea instance to auto-register with | false |
| act_gitea_host_protocol | https | Conenction protocol to the Gitea instance | false |
| act_gitea_become_user | root | User to become *on the Gitea server* during auto-registration token generation | false |
| act_gitea_bin | /usr/local/bin/gitea | Location of the gitea binary on the Gitea instance | false |
| act_labels | ['ubuntu-latest', 'ubuntu-22.04', 'ubuntu-20.04'] | Labels to attach to this runner during auto-registration | false |

Runner scoping
--------------

By default, the `act_scope` variable is set to '' (an empty string). This means that during registration of the act_runner with a Gitea instance, the act_runner will be available for all actions in all repositories on the server.

This might not be what you want. It is also possible to limit availability of the act_runner to a single repository on the Gitea instance, or to an organization.

You can achieve this by setting the `act_scope` variable to 'owner/repo' for a repository scoped act_runner, or to `organization` or `user` for an organization / user scoped runner.

Dependencies
------------

None

Example Playbook
----------------

Run the following playbook to install `act_runner` on all hosts in the `act_runners` group and automatically register them with the Gitea instance at gitea.example.com with the default labels.

```yaml
- name: Set up a set of act_runner instances
  hosts: act_runners
  gather_facts: false
  vars:
    act_gitea_host: gitea.example.com
    act_gitea_host_port: 443
    act_gitea_host_protocol: https
    act_gitea_become_user: gitea
    act_gitea_bin: /usr/local/bin/gitea
    act_gitea_config_path: /etc/gitea/gitea.ini
    act_do_register: true

  pre_tasks:
    - name: Gather required subset of facts
      ansible.builtin.setup:
        gather_subset:
          - '!all'

  roles:
    - role: act

```

License
-------

BSD-3-Clause

Author Information
------------------

This role was created by Maxim Burgerhout <maxim@wzzrd.com> as part fo the wzzrd.gitea Ansible collection.

Please log issues at https://github.com/wzzrd/ansible-collection-gitea.
