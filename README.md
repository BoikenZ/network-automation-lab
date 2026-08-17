# Cisco Network Automation Lab — AWX & Ansible

This repository contains an educational network automation lab built around **AWX (Ansible Tower/AWX)**, **Ansible**, and **Cisco IOS**. The project demonstrates how a centralized automation controller can use Ansible playbooks stored in GitHub to push configuration changes to Cisco routers and verify device state.

The lab focuses on basic automation tasks including changing router hostnames, configuring Layer 3 interfaces, saving modified configurations, and retrieving interface information.

## Project Objectives

- Use AWX as a centralized platform for network automation.
- Store and manage Ansible playbooks in GitHub.
- Target Cisco IOS routers through an AWX inventory.
- Automate router configuration with Ansible.
- Apply interface IP addressing and administrative state changes.
- Save configuration changes to startup configuration.
- Retrieve router information for configuration verification.
- Use the `cisco.ios` Ansible collection for Cisco IOS automation.

## Technologies Used

- **AWX / Ansible Tower**
- **Ansible**
- **Cisco IOS**
- **Cisco IOS Ansible Collection (`cisco.ios`)**
- **YAML**
- **SSH / Ansible `network_cli`**
- **Git / GitHub**

## Repository Contents

| File | Purpose |
| --- | --- |
| `change_hostname.yml` | Changes the hostname of `R1` to `testing`, saves the configuration, and prints the Ansible task result. |
| `TESTING.yml` | Configures `GigabitEthernet2/0` on `R1` with `10.0.1.1/30`, enables the interface, saves the configuration, and prints the result. |
| `configure_interfaces.yml` | Intended to configure `GigabitEthernet0/1` on all inventory hosts. |
| `playbook.yml` | An early/general router configuration playbook intended to set a hostname and configure an interface on the `routers` inventory group. |
| `verify_interfaces.yml` | Intended to retrieve `show ip interface brief` output from `R1` for verification. |
| `requirements.yml` | Declares the `cisco.ios` Ansible collection dependency. |
| `SDN_Assignment_3_awx_tower.pdf` | Original assignment handout included with the lab. |

## How the Lab Was Designed

The GitHub repository acts as the source-controlled Ansible project. In an AWX deployment, the repository would be added as a **Project**, while the Cisco routers, credentials, and device-specific connection variables would be configured through AWX.

A typical workflow for the lab would be:

1. Add this GitHub repository to AWX as a source-controlled project.
2. Create an AWX inventory containing the Cisco routers.
3. Create groups such as `routers` and hosts such as `R1`.
4. Add credentials that allow AWX to SSH to the routers.
5. Configure Cisco IOS connection variables.
6. Create AWX Job Templates that reference the playbooks in this repository.
7. Launch the Job Templates to apply or verify router configuration.

Typical Cisco IOS inventory variables for a current Ansible environment are:

```yaml
ansible_connection: ansible.netcommon.network_cli
ansible_network_os: cisco.ios.ios
ansible_become: true
ansible_become_method: enable
```

Credentials and device IP addresses are intentionally not included in this repository and would normally be managed through AWX inventory and credential objects.

## Example Automation

### Hostname Configuration

`change_hostname.yml` targets `R1` and applies a Cisco IOS hostname change:

```yaml
ios_config:
  lines:
    - "hostname testing"
  save_when: modified
```

The task registers the result and prints it with Ansible's `debug` module.

### Interface Configuration

`TESTING.yml` configures an interface on `R1`:

```text
interface GigabitEthernet2/0
 ip address 10.0.1.1 255.255.255.252
 no shutdown
```

The playbook then saves the modified running configuration.

### Verification

`verify_interfaces.yml` was intended to run:

```text
show ip interface brief
```

to verify the status and addressing of router interfaces after automation jobs were executed.

## Compatibility Note

This repository is a **legacy educational lab** and is preserved largely in its original form. The YAML files are structurally valid, but several playbooks use syntax or module patterns that should be updated before running them with a current Ansible/AWX environment.

Notable items include:

- `configure_interfaces.yml` uses `line:` instead of the current `lines:` argument expected by `ios_config`.
- `playbook.yml` uses older `authorize`/`config` patterns that do not match the current `cisco.ios.ios_config` interface.
- `verify_interfaces.yml` uses `ios_config` for a `show` command; current playbooks should use `cisco.ios.ios_command`.
- Some playbooks use the short module name `ios_config` rather than the fully qualified `cisco.ios.ios_config`.
- The repository does not contain an Ansible inventory, router credentials, or AWX configuration exports, so those pieces must be recreated externally.
- Several configurations contain hard-coded hostnames and IP addresses. They are appropriate for a small lab but should be converted to inventory variables or host variables for reusable automation.
- Abbreviated Cisco IOS commands such as `int` and `no shut` should be expanded when idempotent Ansible behavior is important.

For a current AWX project, the Cisco collection requirements would typically be stored at:

```text
collections/requirements.yml
```

rather than only as a root-level `requirements.yml`.

## What This Project Demonstrates

This lab demonstrates practical experience with:

- Network automation concepts
- Ansible playbook development
- AWX project and job-template workflows
- Cisco IOS configuration automation
- Automated configuration persistence
- Inventory-based device targeting
- Network configuration verification
- Git-based infrastructure automation workflows
- YAML configuration management

## Status

**Educational / archived project.**

The repository documents the original AWX and Ansible lab work, but it should be treated as a historical lab rather than a production-ready automation framework. The core automation concepts remain valid, while several playbooks require minor modernization for current Ansible and AWX releases.
