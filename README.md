# Ansible Collection - wzzrd.gitea

This collection enables the deployment and configuration of the gitea service. More information about gitea at its [homepage](https://gitea.com).

The collection contains both a client and a server role. The server role depends on the client role for installation of the binary, after which the server role configures the server and the systemd service.

Each role contains a README.md file explaining the optional and mandatory variables.

Example Playbooks
-----------------

This role is used in the following way in a playbook:

```yaml
- hosts: atuin_server
  vars:
    atuin_client_version: 18.0.1
    atuin_server_db_uri: "postgres://atuin:atuinpw@dbserver/atuindb"

  roles:
    - role: wzzrd.atuin.atuin_server

- hosts: atuin_clients
  vars:
    atuin_client_users:
      - johnc
      - joeb
    atuin_client_version: 18.0.1

  roles:
    - role: wzzrd.atuin.atuin_client
```

Compatibility
-------------

The roles in this collection are compatible with EL8 and EL9 systems, and have been automatically tested through molecule on ubi8 and ubi9 container images.

-------

BSD-3-Clause

Author Information
------------------

This role was created by Maxim Burgerhout <maxim@wzzrd.com> as part fo the wzzrd.atuin Ansible collection.

Please log issues at https://github.com/wzzrd/ansible-collection-atuin.
