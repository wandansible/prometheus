Ansible role: prometheus
========================

Install and configure prometheus.

Role Variables
--------------

```
ENTRY POINT: *main* - Install and configure prometheus

Options (= indicates it is required):

- prometheus_alerting_rule_files  List of prometheus alerting rule files to create
          default: [{name: general, rules: '{{ prometheus_alerting_rules_general }}'}, {name: prometheus,
              rules: '{{ prometheus_alerting_rules_prometheus }}'}, {name: alertmanager, rules: '{{
                prometheus_alerting_rules_alertmanager }}'}, {name: node, rules: '{{ prometheus_alerting_rules_node
                }}'}, {name: blackbox, rules: '{{ prometheus_alerting_rules_blackbox }}'}]
          elements: dict
          type: list
          options:

          = name            Name of alerting rule group
            type: str

          = rules            List of rule config, as string or list
            type: raw

- prometheus_alerting_rules_alertmanager  List of prometheus alerting rule config for alertmanager, as string
                                           or list
          default: null
          type: raw

- prometheus_alerting_rules_blackbox  List of prometheus alerting rule config for blackbox exporter, as
                                       string or list
          default: null
          type: raw

- prometheus_alerting_rules_general  List of general prometheus alerting rule config, as string or list
          default: null
          type: raw

- prometheus_alerting_rules_node  List of prometheus alerting rule config for node exporter, as string
                                   or list
          default: null
          type: raw

- prometheus_alerting_rules_prometheus  List of prometheus alerting rule config for prometheus, as string or
                                         list
          default: null
          type: raw

- prometheus_arch_map  Mapping of the possible values of ansible_architecture to the
                        prometheus package architectures
          default: null
          type: dict

- prometheus_bin_dir  Directory for the prometheus binaries
          default: /opt/prometheus/
          type: str

- prometheus_checksum_type  The prometheus package checksum type
          default: sha256
          type: str

- prometheus_clean_src_dir  Remove old downloaded archive files from prometheus src directory
          default: true
          type: bool

- prometheus_cli_tools  List of CLI tool binaries that should have symlinks created in
                         /usr/local/bin/
          default: [promtool]
          type: list

- prometheus_config  Contents of the prometheus config file, as string or dict
          default: null
          type: raw

- prometheus_config_dir  Directory for prometheus configuration
          default: /etc/prometheus
          type: str

- prometheus_config_file  Filename for prometheus config file
          default: prometheus.yml
          type: str

- prometheus_db_dir  Directory for prometheus database
          default: /var/lib/prometheus
          type: str

- prometheus_external_url  The URL under which Prometheus is externally reachable (for example,
                            if Prometheus is served via a reverse
                            proxy). Used for generating relative and
                            absolute links back to Prometheus itself.
          default: null
          type: str

- prometheus_extra_flags  Extra flags to run prometheus with
          default: null
          type: dict

- prometheus_flags  List of flags to run prometheus with, as string or list
          default: null
          type: raw

- prometheus_github_checksum_filename  Filename for the prometheus package checksums file on github
          default: sha256sums.txt
          type: str

- prometheus_github_org  Name of organisation for prometheus github repository
          default: prometheus
          type: str

- prometheus_github_repo  Name of prometheus github repository
          default: prometheus
          type: str

- prometheus_group  Name of the prometheus unix group
          default: prometheus
          type: str

- prometheus_install  If true, install prometheus
          default: true
          type: bool

- prometheus_listen  Listen address and port
          default: localhost:9090
          type: str

- prometheus_log_format  Output format of log messages
          choices: [logfmt, json]
          default: logfmt
          type: str

- prometheus_log_level  Only log messages with the given severity or above
          choices: [debug, info, warn, error]
          default: warn
          type: str

- prometheus_manage_user  If true, add prometheus unix user and group
          default: true
          type: bool

- prometheus_port  Listen port
          default: 9090
          type: int

- prometheus_recording_rule_files  List of prometheus recording rule files to create
          default: null
          elements: dict
          type: list
          options:

          = name            Name of recording rule group
            type: str

          = rules            List of rule config, as string or list
            type: raw

- prometheus_rules_dir  Directory for prometheus recording and alerting rule files
          default: /etc/prometheus/rules
          type: str

- prometheus_scrape_config_dir  Directory for prometheus scrape config files
          default: /etc/prometheus/scrape_configs
          type: str

- prometheus_scrape_config_files  List of prometheus scrape config files to create
          default: null
          elements: dict
          type: list
          options:

          = name            Name of scrape config file
            type: str

          = scrape_configs            List of prometheus scrape config, as string or list
            type: raw

- prometheus_sd_dir  Directory for prometheus service discovery files
          default: /etc/prometheus/file_sd
          type: str

- prometheus_sd_files  List of prometheus service discovery files to create
          default: null
          elements: dict
          type: list
          options:

          = name            Name of service discovery file
            type: str

          = static_configs            List of prometheus file service discovery config, as
                             string or list
            type: raw

- prometheus_src_dir  Directory for the downloaded prometheus src archive
          default: /opt/prometheus/src
          type: str

- prometheus_src_files  List of files to extract from the source archive
          default: [prometheus, promtool, consoles, console_libraries]
          elements: str
          type: list

- prometheus_storage_retention  How long to keep data for, specify as a number followed by time
                                 unit. The units supported are y, w,
                                 d, h, m, s, ms.
          default: null
          type: str

- prometheus_storage_retention_size  Maximum number of bytes that can be stored for blocks. A unit is
                                      required, supported units are B,
                                      KB, MB, GB, TB, PB, EB. Based on
                                      powers-of-2, so 1KB is 1024B.
          default: null
          type: str

- prometheus_strip_components  Strip NUMBER leading components from file names on extraction
          default: 1
          type: int

- prometheus_user  Name of the prometheus unix user
          default: prometheus
          type: str

- prometheus_version  Version to install (use "latest" for the latest version)
          default: latest
          type: str
```

Installation
------------

This role can either be installed manually with the ansible-galaxy CLI tool:

    ansible-galaxy install git+https://github.com/wandansible/prometheus,main,wandansible.prometheus
     
Or, by adding the following to `requirements.yml`:

    - name: wandansible.prometheus
      src: https://github.com/wandansible/prometheus

Roles listed in `requirements.yml` can be installed with the following ansible-galaxy command:

    ansible-galaxy install -r requirements.yml

Example Playbook
----------------

    - hosts: all
      roles:
        - role: wandansible.prometheus
          become: true
          vars:
            prometheus_config:
              global:
                scrape_interval: 15s
                evaluation_interval: 15s

              alerting:
                alertmanagers:
                  - static_configs:
                      - targets:
                          - localhost:9093

              rule_files:
                - "{{ prometheus_rules_dir }}/recording/*.yml"
                - "{{ prometheus_rules_dir }}/alerting/*.yml"

              scrape_config_files:
                - "{{ prometheus_scrape_config_dir }}/*.yml"

            prometheus_scrape_config_files:
              - name: prometheus
                scrape_configs:
                  - job_name: "prometheus"
                    relabel_configs:
                      - source_labels: [__address__]
                        target_label: instance
                      - source_labels: [__address__]
                        target_label: __address__
                        regex: "(.+)"
                        replacement: "${1}:9090"
                    static_configs:
                      - targets:
                          - localhost

              - name: alertmanager
                scrape_configs:
                  - job_name: "alertmanager"
                    relabel_configs:
                      - source_labels: [__address__]
                        target_label: instance
                      - source_labels: [__address__]
                        target_label: __address__
                        regex: "(.+)"
                        replacement: "${1}:9093"
                    static_configs:
                      - targets:
                          - localhost

              - name: node
                scrape_configs:
                  - job_name: node
                    relabel_configs:
                      - source_labels: [__address__]
                        target_label: instance
                      - source_labels: [__address__]
                        target_label: __address__
                        regex: "(.+)"
                        replacement: "${1}:9100"
                    file_sd_configs:
                      - files:
                          - "{{ prometheus_sd_dir }}/node.yml"

            prometheus_sd_files:
              - name: node
                static_configs:
                  - targets:
                      - localhost
                    labels:
                      environment: local
