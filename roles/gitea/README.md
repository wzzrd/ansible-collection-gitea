wzzrd.gitea.gitea
=================

Configures the Gitea server process

Requirements
------------

No explicit requirements.

Role Variables
--------------

| Variable name | Default value | Meaning | Mandatory |
|---------------|---------------|---------|-----------|
| gitea_version | 1.21.4 | The version of Gitea to install | false |
| gitea_version_check | true | Version updates will not happen when this is false | false |
| gitea_dl_url | See `defaults/main.yml` | URL to download Gitea from, can be set to local mirror | false |
| gitea_gpg_key | 7C9E68152594688862D62AF62D9AE806EC1592E2 | GPG key to verify Gitea download | false |
| gitea_gpg_server | hkp://keyserver.ubuntu.com:80 | Keyserver to download key from | false |
| gitea_backup_location | `/var/backups/gitea` | Backup storage location | false |
| gitea_do_backup | false | Whether to perform backup during update | false |
| gitea_systemd_cap_net_bind_service | false | Whether or not to add the CAP_NET_BIND capability to the systemd unit file, required for binding to ports under 1024 | false |
| gitea_app_name | Gitea | Name of the Gitea installation | false |
| gitea_user | gitea | User to run gitea service under | false |
| gitea_group | gitea | Group for gitea_user | false |
| gitea_shell | `/bin/false` | Shell for gitea_user | false |
| gitea_home | `/var/lib/gitea` | Home directory for gitea_user | false |
| gitea_repository_root | `{{ gitea_home }}` | Base directory for repository storage | false |
| gitea_http_domain | localhost | The DNS domain for this Gitea instance | false |
| gitea_http_port | 3000 | Port to bind the gitea process to | false |
| gitea_http_host | 127.0.0.1 | IP address to bind the gitea process to | false |
| gitea_root_url | http://{{ gitea_http_domain }}:{{ gitea_http_port }}" | Root URL; automatically configured based on `gitea_http_domain` and `gitea_http_port` mentioned above | false |
| gitea_config_file | See `defaults/main.yml` | Dictionary that will be converted into the main configuration file | false |

The role also allows for the configuration of some secrets required by Gitea. These are as follows. Mind that these will stored on the file system and not made integral part of the configuration file. The configuration file will reference the secrets stored on the file system.

It is recommended to set these variables manually and store them in your vault. Leaving them blank will make the role generate secrets automatically. These will still be stored on the file system, but you will not have a backup of them without you manually copying them into your vault!

| Variable name | Default value | Meaning | Mandatory |
|---------------|---------------|---------|-----------|
| gitea_secret_key | Auto generated | See Gitea docs | false, but recommended |
| gitea_internal_token | Auto generated | See Gitea docs | false, but recommended |
| gitea_oauth2_jwt_secret | Auto generated |  See Gitea docs | false, but recommended |
| gitea_lfs_jwt_secret | Auto generated if LFS is enabled |  See Gitea docs | false, but recommended if LFS is enabled |

The relevant part of the Gitea docs is [here](https://docs.gitea.com/administration/config-cheat-sheet).

Dependencies
------------

This role requires the availability of community.general as it uses the `to_ini` filter to build the Gitea configuration file.

Example Playbook
----------------

This is an example playbook to set up a Gitea instance that uses a PostgreSQL database. As illustrated, the `gitea_config_file` dictionary contains names for the various sections in the `gitea.ini` configuration file (DEFAULT, server, database, etc), and key value pairs describing the various values I want in the resulting configuration file.

The example below overrides the whole `gitea_config_file` dictionary. If you omit the `gitea_config_file` variable, you will get a very minimal Gitea server, listening on port 127.0.0.1 only, connected to a sqlite3 database.

Therefore, most sites will want to provide the `gitea_config_file` directionary to the role.

Mind that the SECRET_KEY_URI, INTERNAL_TOKEN_URI and JWT_SECRET_URI settings are
automatically added to the `gitea_config_file` dictionary.

```yaml

---
# file: gitea.yml
- hosts: gitea
  name: Deploy Gitea
  gather_facts: false
  become: true

  pre_tasks:
    - name: Gather subset of facts
      ansible.builtin.setup:
        gather_subset:
          - '!all'
          - default_ipv4

  roles:
    - role: wzzrd.gitea.gitea
      vars:
        gitea_version: "1.21.7"
        gitea_home: /var/lib/gitea
        gitea_shell: /bin/false
        gitea_server_do_backup: true

        gitea_config_file:
          DEFAULT:
            APP_NAME: "Gitea, Personal instance"
            RUN_USER: "gitea"
            WORK_PATH: "/var/lib/gitea"
          server:
            DOMAIN: "gitea.example.com"
            ROOT_URL: "https://gitea.example.com/"
            HTTP_ADDR: "{{ ansible_default_ipv4.address }}"
            HTTP_PORT: "3000"
            SSH_DOMAIN: "gitea.example.com"
            SSH_START_SERVER: "false"
            OFFLINE_MODE: "false"
          database:
            DB_TYPE: "postgres"
            HOST: "postgresql.example.com:5432"
            NAME: "giteadb"
            USER: "gitea"
            PASSWD: "{{ vault_postgresql_gitea_pw }}" # notsecret
            SSL_MODE: "verify-full"
          metrics:
            ENABLED: "true"
          mailer:
            ENABLED: "true"
            PROTOCOL: "smtp+starttls"
            SMTP_ADDR: "smtp.example.com"
            SMTP_PORT: "587"
            FROM: "gitea@example.com"
          session:
            PROVIDER: "file"
          webhook:
            ALLOWED_HOST_LIST: "*.example.com"
          actions:
            ENABLED: "true"
          service:
            DISABLE_REGISTRATION: "true"
            REQUIRE_SIGNIN_VIEW: "true"
            ENABLE_NOTIFY_MAIL: "true"
            SHOW_REGISTRATION_BUTTON: "false"
          indexer:
            REPO_INDEXER_ENABLED: "true"
          oauth2:
            ENABLED: "false"
          security:
            INSTALL_LOCK: "true"
          repository:
            ROOT: "/var/lib/gitea"

```

License
-------

BSD-3-Clause

Acknowledgements
----------------

This role is partially based on the gitea role originally written by Thomas Maurice, to
be found on [GitHub](https://github.com/thomas-maurice/ansible-role-gitea).

Author Information
------------------

This role was created by Maxim Burgerhout <maxim@wzzrd.com> as part fo the wzzrd.gitea Ansible collection.

Please log issues at https://github.com/wzzrd/ansible-collection-gitea.
