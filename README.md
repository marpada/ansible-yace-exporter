# Ansible Role: Yet-Another CloudWatch Exporter

[![License](https://img.shields.io/badge/license-MIT%20License-brightgreen.svg)](https://opensource.org/licenses/MIT)
[![Tag](https://img.shields.io/github/tag/marpada/ansible-yace-exporter.svg)](https://github.com/marpada/ansible-yace-exporter/tags)
## Description

Deploy and manage [yace exporter](https://github.com/ivx/yet-another-cloudwatch-exporter) which allows adding CloudWatch metrics into Prometheus.

## Requirements

None

## Role Variables

All variables which can be overridden are stored in [defaults/main.yml](defaults/main.yml) file as well as in table below.

| Name           | Default Value | Description                        |
| -------------- | ------------- | -----------------------------------|
| `yace_exporter_version` | 0.13.7 | yace exporter package version |
| `yace_exporter_web_listen_address` | 127.0.0.1:5000 | Address on which yace exporter will be listening |
| `yace_exporter_configuration` | {} | [yace exporter configuration](https://github.com/ivx/yet-another-cloudwatch-exporter#configuration) |
| `yace_exporter_debug` | false | Enable debug mode |

## Example Playbook

```yaml
- hosts: prometheus
  vars:
    yace_exporter_web_listen_address: 127.0.0.1:5000
    yace_exporter_configuration:
      discovery:
        exportedTagsOnMetrics:
          ebs:
            - VolumeId
        jobs:
        - type: "ebs"
          region: eu-west-1
          searchTags:
            - Key: type
              Value: public
          metrics:
            - name: BurstBalance
              statistics:
              - 'Minimum'
              period: 600
              length: 600
      static:
        - namespace: AWS/AutoScaling
          region: eu-west-1
          dimensions:
           - name: AutoScalingGroupName
             value: Test
          customTags:
            - Key: CustomTag
              Value: CustomValue
          metrics:
            - name: GroupInServiceInstances
              statistics:
              - 'Minimum'
              period: 60
              length: 300
  become: true
  roles:
    - marpada.yace-exporter
```

## Local Testing

Basic tests are included for a number of Linux distros using [molecule]https://github.com/metacloud/molecule)  with the docker provisioner.

```
molecule test
```

## License

This project is licensed under MIT License. See [LICENSE](/LICENSE) for more details.


## Acknowledgments

This role is heavily inspired on the [cloudalchemy/ansible-blackbox-exporter](https://github.com/cloudalchemy/ansible-blackbox-exporter) role
