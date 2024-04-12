# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.5]
#### Fixed

- Restart act_runner after update

## [1.0.4]

#### Fixed

- Fixed missing task for starting act_runner in act_runner role after registration

## [1.0.1]

#### Fixed

- Fixed bug where settings like SECRET_KEY_URI would not be automatically added to the
  Gitea configuration file

## [1.0.0]

### Changed

- Large rewrite of the gitea configuration file and adjecent tasks
  - No longer a template, but based on community.general.to_ini
  - Split out all secret related tasks to their own files
  - All secrets can new be auto generated, but it's recommended to provide them through
    your vault
  - Secrets are no longer part of the configuration file, but instead stored as separate
    files in /etc/gitea
- Renamed gitea_server to gitea
- Renamed act_runner to act
- Removed the act_podman role for now, but it will be back
- Properly prepended all variables with either gitea_ or act_

#### Added

- Full test scenario of a combination of both the act and gitea roles

## [0.9.1]

#### Added

- Added a molecule scenario that tests gitea server updates

#### Fixed

- Backups during updates didn't run because of some logic bug; it runs now

## [0.9.0]

- First version uploaded to Ansible Galaxy
