## ansible-new-relic-php-agent


Ansible role for installing new relic php agent on CentOS 7.
  - Support multiple php installations
  - Complete control over `newrelic.ini` configuration
  - Support for uninstallting the agent

This role is still in early states of development, so issues are very possible.
Comments and pull reqeusts are welcome !

Requirements
------------

- Role currenlty only has support for `Centos 7`.
- Working php installation

Role Variables
--------------
`php_installations:` **[required]** List of php installations


#### [required] fields for `php_installations` list item:
  - `appname`: application name: [help: name-your-php-application](https://docs.newrelic.com/docs/agents/php-agent/configuration/name-your-php-application/)
  - `license`: new relic license key: [help: new-relic-api-keys/#ingest-license-key](https://docs.newrelic.com/docs/apis/intro-apis/new-relic-api-keys/#ingest-license-key)
  - `bin_path`: directory that contains the `php` executable
  - `config_path`: direcotry where `php` configuration is located (`php.d/`)

#### [optional] fields for `php_installations` list item:
  - `php_fpm_service_name:` php-fpm service name to be restarted when php configuration changes are done.
  - `web_server_service_name`: web-server service name to be restarted when php configuration changes are done.
  - `ini`: newrelic.ini configuration variables

## Important !
  - **Before overwriting `newrelic.ini` variables in `ini:` section make sure that you understand what you are doing.**
  - **Changing the .ini values could cause security flaws with the installation !**
  - Help with indivial variabled: https://docs.newrelic.com/docs/agents/php-agent/configuration/php-agent-configuration/
 
example:
```yaml
php_installations:
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


Example Playbook
----------------

Example: Install the new relic php agent for single remi php version. 
```yaml
---
- hosts: all
  vars:
    php_installations:
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

Example: Install the new relic php agent for standard php installation
```yaml
---
- hosts: all
  vars:
    php_installations:
      - appname: my application
        license: awdbk12312adawd3212312kadwaw2132
        bin_path: /usr/bin/
        config_path: /etc/php.d/
        php_fpm_service_name: php-fpm
  roles:
     - visdmin.ansible-new-relic-php-agent
```

License
-------

MIT
