<!--
SPDX-FileCopyrightText: 2020 - 2024 MDAD project contributors
SPDX-FileCopyrightText: 2020 - 2024 Slavi Pantaleev
SPDX-FileCopyrightText: 2020 Aaron Raimist
SPDX-FileCopyrightText: 2020 Chris van Dijk
SPDX-FileCopyrightText: 2020 Dominik Zajac
SPDX-FileCopyrightText: 2020 Mickaël Cornière
SPDX-FileCopyrightText: 2022 François Darveau
SPDX-FileCopyrightText: 2022 Julian Foad
SPDX-FileCopyrightText: 2022 Warren Bailey
SPDX-FileCopyrightText: 2023 Antonis Christofides
SPDX-FileCopyrightText: 2023 Felix Stupp
SPDX-FileCopyrightText: 2023 Julian-Samuel Gebühr
SPDX-FileCopyrightText: 2023 Pierre 'McFly' Marty
SPDX-FileCopyrightText: 2024 Thomas Miceli
SPDX-FileCopyrightText: 2024 - 2025 Suguru Hirahara

SPDX-License-Identifier: AGPL-3.0-or-later
-->

# Setting up Immich Kiosk

This is an [Ansible](https://www.ansible.com/) role which installs [Immich Kiosk](https://immichkiosk.app/) to run as a [Docker](https://www.docker.com/) container wrapped in a systemd service.

Immich Kiosk is a software for displaying photos and videos on your [Immich](https://immich.app) server in highly customizable slideshows that run in browsers and other devices.

See the project's [documentation](https://docs.immichkiosk.app/) to learn what Immich Kiosk does and why it might be useful to you.

## Prerequisites

To run an Immich Kiosk it is necessary to prepare an Immich server.

If you are looking for an Ansible role for Immich, you can check out [ansible-role-immich](https://github.com/mother-of-all-self-hosting/ansible-role-immich) maintained by the [Mother-of-All-Self-Hosting (MASH)](https://github.com/mother-of-all-self-hosting) team.

## Adjusting the playbook configuration

To enable Immich Kiosk with this role, add the following configuration to your `vars.yml` file.

**Note**: the path should be something like `inventory/host_vars/mash.example.com/vars.yml` if you use the [MASH Ansible playbook](https://github.com/mother-of-all-self-hosting/mash-playbook).

```yaml
########################################################################
#                                                                      #
# immich_kiosk                                                         #
#                                                                      #
########################################################################

immich_kiosk_enabled: true

########################################################################
#                                                                      #
# /immich_kiosk                                                        #
#                                                                      #
########################################################################
```

### Set the Immich instance's API key

You also need to obtain and set the API key of your Immich's instance to add the following configuration to your `vars.yml` file:

```yaml
immich_kiosk_environment_variables_kiosk_immich_api_key: YOUR_IMMICH_INSTANCE_API_KEY_HERE
```

It is possible to obtain the API key in the user setting panel on the web interface. See [this section](https://docs.immich.app/features/command-line-interface#obtain-the-api-key) on the Immich's documentation for details.

Refer to [this section](https://docs.immichkiosk.app/installation/#api-key-permissions) on the documentation of Immich Kiosk for necessary permissions.

### Set Immich's URLs

The Immich Kiosk requires the Immich instance's URL for connecting to it. Add the following configuration to your `vars.yml` file to specify it:

```yaml
immich_kiosk_environment_variables_kiosk_immich_url: YOUR_IMMICH_INSTANCE_URL_HERE:YOUR_IMMICH_INSTANCE_PORT_HERE
```

Make sure to add its port number if it is needed.

It is possible to specify the public URL of your Immich server used for generating links and QR codes in the additional information overlay:

```yaml
immich_kiosk_environment_variables_kiosk_immich_external_url: YOUR_IMMICH_INSTANCE_EXTERNAL_URL_HERE
```

See [this page](https://docs.immichkiosk.app/configuration/core/) on the documentation for details.

### Set regional preferences (optional)

You can customize the language of the UI and the timezone for the clock on it:

```yaml
immich_kiosk_environment_variables_lang: en_GB

immich_kiosk_environment_variables_tz: UTC
```

See [this page](https://raw.githubusercontent.com/damongolding/immich-kiosk/refs/heads/main/assets/locales.md) for available language codes and [this page](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones#List) for available timezones, respectively.

### Exposing the instance (optional)

By default, the Immich Kiosk is not exposed externally, as it is mainly intended to be used in the internal network, connected to the Immich server.

To expose it to the internet, add the following configuration to your `vars.yml` file. Make sure to replace `example.com` with your own value.

```yaml
immich_kiosk_hostname: "example.com"

immich_kiosk_container_labels_traefik_enabled: true
```

After adjusting the hostname, make sure to adjust your DNS records to point the domain to your server.

**Note**: hosting Immich Kiosk under a subpath (by configuring the `immich_kiosk_path_prefix` variable) does not seem to be possible due to Immich Kiosk's technical limitations.

>[!NOTE]
> When exposing the instance, it is recommended to consider to set a password (see [this section](https://docs.immichkiosk.app/configuration/additional-options/#password) for the necessary configuration) as well as enable a service for authentication such as [authentik](https://goauthentik.io/) and [Tinyauth](https://tinyauth.app) based on your use-case.

### Extending the configuration

There are some additional things you may wish to configure about the component.

Take a look at:

- [`defaults/main.yml`](../defaults/main.yml) for some variables that you can customize via your `vars.yml` file. You can override settings (even those that don't have dedicated playbook variables) using the `immich_kiosk_environment_variables_additional_variables` variable

See [the official documentation](https://docs.immichkiosk.app/configuration/) for a complete list of Immich Kiosk's config options that you could put in `immich_kiosk_environment_variables_additional_variables`.

#### Configuration example (demo style UI)

As the Immich Kiosk is highly customizable, the default UI is pretty simple, and it is necessary for most of the settings and features to be enabled explicitly. Refer to <https://docs.immichkiosk.app/configuration/> for details about available options, such as selecting an album to include / exclude from being displayed.

If you are looking for the beautiful and neat UI which looks like the one at [this demo site](https://demo.immichkiosk.app/), this should work as a boilerplate, on which you can add other customizations or tweak them per your preference:

```yaml
immich_kiosk_environment_variables_additional_variables: |
  KIOSK_SHOW_TIME=true
  KIOSK_SHOW_DATE=true
  KIOSK_CLOCK_SOURCE=client
  KIOSK_DURATION=30
  KIOSK_MEMORIES=true
  KIOSK_THEME=fade
  KIOSK_LAYOUT=splitview
  KIOSK_TRANSITION=cross-fade
  KIOSK_SHOW_PROGRESS_BAR=true
  KIOSK_SHOW_ALBUM_NAME=true
  KIOSK_SHOW_IMAGE_TIME=true
  KIOSK_SHOW_IMAGE_DATE=true
  KIOSK_SHOW_IMAGE_DESCRIPTION=true
  KIOSK_SHOW_IMAGE_EXIF=true
  KIOSK_SHOW_IMAGE_LOCATION=true
```

>[!NOTE]
> The weather location overlay can only be configured using a `config.yaml` file with an API key from [OpenWeatherMap](https://openweathermap.org/). See [this page](https://docs.immichkiosk.app/configuration/weather/) for details. To use the file, you manually need to mount it to the container with `immich_kiosk_container_additional_volumes_custom` avoiding the configuration with environment variables alltogether.

## Installing

After configuring the playbook, run the installation command of your playbook as below:

```sh
ansible-playbook -i inventory/hosts setup.yml --tags=setup-all,start
```

If you use the MASH playbook, the shortcut commands with the [`just` program](https://github.com/mother-of-all-self-hosting/mash-playbook/blob/main/docs/just.md) are also available: `just install-all` or `just setup-all`

## Usage

After running the command for installation, Immich Kiosk becomes available internally to other services on the same network. If the service is exposed to the internet, it becomes available at the specified hostname like `https://example.com`.

To get started, refer to [the documentation](https://docs.immichkiosk.app/guides/digital-picture-frame-immich-kiosk-old-tablet/) and other pages for guides about how to display pictures on devices.

## Troubleshooting

### Check the service's logs

You can find the logs in [systemd-journald](https://www.freedesktop.org/software/systemd/man/systemd-journald.service.html) by logging in to the server with SSH and running `journalctl -fu immich-kiosk` (or how you/your playbook named the service, e.g. `mash-immich-kiosk`).
