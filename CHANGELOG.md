# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.9]
#### Fixed

- The `Reload systemd` handler now runs before `Restart gitea_runner`. Handlers
  run in the order they are defined, so the runner used to be restarted from
  the unit file systemd still had cached, and a changed unit file only took
  effect on the restart after that.

#### Added

- The `gitea_runner` role now manages the labels of a runner over its whole
  life, not just at registration time. The labels are passed to
  `gitea-runner daemon` in the systemd unit file, which takes precedence over
  both `runner.labels` in `config.yaml` and the labels in the `.runner` file,
  and which the runner declares to the Gitea server on every start.
  `gitea_runner_labels` remains the set of labels a runner should have. Setting
  `gitea_runner_labels_state` to `merged` instead applies
  `gitea_runner_labels_add` and `gitea_runner_labels_remove` to the labels the
  runner already has. Labels are matched on their name, so adding a label that
  already exists repoints it at another image.
- A `labels` molecule scenario covering both label states.

## [1.0.8]
#### Fixed

- Removed the `creates:` guard on the gitea-runner unarchive task and kept the
  downloaded archive on disk (`unxz -k`). The guard skipped decompression
  whenever the artifact filename was unchanged, so tracking a moving channel
  (`gitea_runner_version: nightly`) re-downloaded the new build but silently
  went on installing the previous one. Reporting of the change now comes from
  the copy task that installs the binary.

#### Added

- Documented how to point `gitea_runner` at the nightly channel at
  https://dl.gitea.com/gitea-runner/nightly

## [1.0.7]
#### Added

- A `gitea_runner` role, which installs and registers the `gitea-runner` that
  Gitea recently moved to from `act_runner`. It mirrors the `act_runner` role,
  so moving an existing runner over is a matter of switching roles, and comes
  with `default` and `update` molecule scenarios.

## [1.0.6]
#### Fixed

- Add workflow to test act upgrade

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
