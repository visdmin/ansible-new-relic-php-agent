[![Ansible Role](https://img.shields.io/ansible/role/56040?color=orange)](https://galaxy.ansible.com/visdmin/ansible_new_relic_php_agent)
[![GitHub tag (latest by date)](https://img.shields.io/github/v/tag/visdmin/ansible-new-relic-php-agent)](https://github.com/visdmin/ansible-new-relic-php-agent/releases)
[![GitHub](https://img.shields.io/github/license/visdmin/ansible-new-relic-php-agent)](https://github.com/visdmin/ansible-new-relic-php-agent/blob/main/LICENSE)
[![GitHub last commit](https://img.shields.io/github/last-commit/visdmin/ansible-new-relic-php-agent)](https://github.com/visdmin/ansible-new-relic-php-agent/commits/main)


# ansible-new-relic-php-agent


Ansible role for installing new relic php agent on CentOS 7.
  - Support multiple php installations
  - Complete control over `newrelic.ini` configuration
  - Support for uninstallting the agent

This role is still in early states of development, so issues are very possible.
Comments and pull reqeusts are welcome !

## Installation

Using `ansible-galaxy`:
```shell
ansible-galaxy install visdmin.ansible-new-relic-php-agent
```

Using `git`:
```shell
git clone https://github.com/visdmin/ansible-new-relic-php-agent.git
```

Using `requirements.yml`:
```yaml
- src: visdmin.ansible-new-relic-php-agent
```

## Requirements


- Role currenlty only has support for `CentOS 7`.
- Working php installation on taget host
- New relic license key [help here: docs.newrelic.com](https://docs.newrelic.com/docs/apis/intro-apis/new-relic-api-keys/#ingest-license-key)

## Role Variables

### [required] `anrpa_installation_state`:
  - `present`: Install new-relic php agent or keep the current version, and configure it.
  - `latests`: Keep new-relic php agent up to date, and configure it.
  - `absent`: Uninstall new-relic php agent, repository, and configurations.

#### [required] `anrpa_php_installations:` list of `php` installations:
```yaml
anrpa_php_installations:                         # List of php installations      (required)
  # php installation #1
  - appname: "my app"                            # Aplication name                (required)
    license: "123123810923789018283"             # New relic license key          (required)
    bin_path: "/opt/remi/php74/root/bin/"        # Path to the `php` executable   (required)
    config_path: "/etc/opt/remi/php74/php.d/"    # Path to `php` config directory (required)

  # php installation #2
  - appname: "another app"
    license: "adaw213131321"
    #...
  # php installation #n
```


#### Required fields for `php_installation`:
  - `appname`: application name: [help: name-your-php-application](https://docs.newrelic.com/docs/agents/php-agent/configuration/name-your-php-application/)
  - `license`: new relic license key: [help: new-relic-api-keys/#ingest-license-key](https://docs.newrelic.com/docs/apis/intro-apis/new-relic-api-keys/#ingest-license-key)
  - `bin_path`: directory that contains the `php` executable
  - `config_path`: direcotry where `php` configuration is located (`php.d/`)

#### Optional fields for `php_installation`:
  - `state`: [present | absent] can be used to disable php single installation (removes the .ini file for the installation)
  - `php_fpm_service_name`: php-fpm service name to be restarted when php configuration changes are done.
  - `web_server_service_name`: web-server service name to be restarted when php configuration changes are done.
  - `ini`: newrelic.ini configuration variables

## Important !
  - **Before overwriting `newrelic.ini` variables in `ini:` section make sure that you understand what you are doing.**
  - **Changing the .ini values could cause security flaws with the installation !**
  - Help with indivial variabled: https://docs.newrelic.com/docs/agents/php-agent/configuration/php-agent-configuration/

#### Other role variables (defaults)
  - `anrpa_new_relic_repo_name`: new-relic yum repository name
  - `anrpa_new_relic_64bit_repo_url`: new-relic yum repository url for 64-bit systems
  - `anrpa_new_relic_32bit_repo_url`: new-relic yum repository url for 32-bit systems
  - `anrpa_php_agent_package_name`: new-relic php agent package name
  - `anrpa_php_agent_service_name`: new-relic php agent service name
  - `anrpa_php_ini_filename`: new-relic php.ini file name

Variable example:
```yaml
anrpa_installation_state: latest
anrpa_php_installations:
  - appname: My php 7.4 installation 1
    license: awdbk12312adawd12312kadwaw2132
    bin_path: /opt/remi/php74/root/bin/
    config_path: /etc/opt/remi/php74/php.d/
    php_fpm_service_name: php74-php-fpm
    web_server_service_name: nginx
    # newrelic.ini configuration:
    ini:
      enabled: true
      logfile: "/var/log/newrelic/php_agent.log"
      loglevel: "info"
      high_security: false
      process_host.display_name: ""
      daemon.logfile: "/var/log/newrelic/newrelic-daemon.log"
      daemon.loglevel: "info"
      #...
  - appname: My php 7.2 installation
    license: awdbk12312adawd12312kadwaw2132
    bin_path: /opt/remi/php72/root/bin/
    config_path: /etc/opt/remi/php72/php.d/
    php_fpm_service_name: php72-php-fpm
    web_server_service_name: apache
    # newrelic.ini configuration:
    ini:
      enabled: true
      logfile: "/var/log/newrelic/php_agent.log"
      loglevel: "info"
      high_security: false
      process_host.display_name: ""
      daemon.logfile: "/var/log/newrelic/newrelic-daemon.log"
      daemon.loglevel: "info"
      #...
```


## Example Playbooks

#### Install the new relic php agent for single remi php version.
```yaml
---
- hosts: all
  vars:
    anrpa_installation_state: latest
    anrpa_php_installations:
      - appname: my application
        license: awdbk12312ada2wd12312kadwaw2132
        bin_path: /opt/remi/php74/root/bin/
        config_path: /etc/opt/remi/php74/php.d/
        php_fpm_service_name: php74-php-fpm
        web_server_service_name: nginx
        ini:
          framework: symfony2
  roles:
    - visdmin.ansible-new-relic-php-agent
```


#### Uninstall the new relic php agent and configurations. (`anrpa_installation_state: absent`)
```yaml
---
- hosts: all
  vars:
    anrpa_installation_state: absent
    anrpa_php_installations:
      - appname: my application
        license: awdbk12312ada2wd12312kadwaw2132
        bin_path: /opt/remi/php74/root/bin/
        config_path: /etc/opt/remi/php74/php.d/
        php_fpm_service_name: php74-php-fpm
        web_server_service_name: nginx
        ini:
          framework: symfony2
  roles:
    - visdmin.ansible-new-relic-php-agent
```

#### Install the new relic php agent for two different php versions.
```yaml
---
- hosts: all
  become: true
  vars:
    anrpa_installation_state: latest
    anrpa_php_installations:
      - appname: php-7.4
        license: 123123981awdawd0293129
        bin_path: /opt/remi/php74/root/bin/
        config_path: /etc/opt/remi/php74/php.d/
        php_fpm_service_name: php74-php-fpm
        web_server_service_name: nginx
      - appname: php-7.2
        license: 123123981awdawd0293129
        bin_path: /opt/remi/php72/root/bin/
        config_path: /etc/opt/remi/php72/php.d/
        php_fpm_service_name: php72-php-fpm
        web_server_service_name: nginx
  roles:
    - visdmin.ansible-new-relic-php-agent
```

#### Install the new relic php agent for standard php installation
```yaml
---
- hosts: all
  vars:
    anrpa_installation_state: latest
    anrpa_installation_state.php_installations:
      - appname: my application
        license: awdbk12312adawd3212312kadwaw2132
        bin_path: /usr/bin/
        config_path: /etc/php.d/
        php_fpm_service_name: php-fpm
  roles:
     - visdmin.ansible-new-relic-php-agent
```

## License

MIT
