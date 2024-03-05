# Ansible Collection - wzzrd.gitea

This collection enables the deployment and configuration of the Gitea service. More information about Gitea at its [homepage](https://gitea.com).

The collection contains roles for the Gitea server itself, as well as a role for setting up `act` instances for Gitea Actions. It also includes an *experimental* role for running `act` instances in podman containers. The server role depends on the community.general collection to transform the configration dictionary into the ini format expected by Gitea.

Each role contains a README.md file explaining the optional and mandatory variables.

Example Playbooks
-----------------

The roles in this collection can be used in the following way in a playbook:

```yaml
- hosts: gitea_server
  vars:
    gitea_http_host: "192.168.0.10" # address to bind to
    gitea_http_domain: "gitea.example.com"

  roles:
    - role: wzzrd.gitea.gitea

- hosts: act_server
  vars:
    act_gitea_host: "gitea.example.com" # Gitea server to register this act_runner with
    act_gitea_host_port: 3000
    act_gitea_host_protocol: http
    act_gitea_become_user: gitea
    act_gitea_bin: /usr/local/bin/gitea
    act_gitea_config_path: /etc/gitea/gitea.ini
    act_do_register: true

  roles:
    - role: wzzrd.gitea.act
```

This will result in a (minimal) Gitea configuration, and a running Gitea server, as well as an act_runner process running on the act_server, connected to the Gitea server.

If you do not know what an act_runner process does, please read [this](https://docs.gitea.com/usage/actions/act-runner).

Compatibility
-------------

The roles in this collection are compatible with EL8 and EL9 systems, and have been automatically tested through molecule on ubi8 and ubi9 container images.

-------

BSD-3-Clause

Author Information
------------------

This collection was created by Maxim Burgerhout <maxim@wzzrd.com> as the wzzrd.gitea Ansible collection.

Please log issues at https://github.com/wzzrd/ansible-collection-gitea.
