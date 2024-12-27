# README: Ansible Playbook for ClickHouse, Vector, and LightHouse Setup

## Overview
This Ansible playbook automates the installation and configuration of the following services:
- **ClickHouse**: A columnar database designed for analytics.
- **Vector**: A log aggregation tool for collecting, processing, and shipping data.
- **LightHouse**: A static web server for displaying analytical data.

The playbook handles tasks such as downloading and installing specific versions, configuring services, and deploying necessary configurations. It supports customization through variables and tagging.

---

## Playbook Details

### 1. ClickHouse Configuration
#### Playbook Name: `Install and configure Clickhouse`

#### Description:
This playbook:
- Downloads and installs the specified version of ClickHouse.
- Installs ClickHouse components: `clickhouse-common-static`, `clickhouse-client`, and `clickhouse-server`.
- Configures the `clickhouse-server` service as a systemd service.
- Creates a database named `logs`.

#### Variables:
| Variable             | Description                             | Default Value      |
|----------------------|-----------------------------------------|--------------------|
| `clickhouse_version` | Version of ClickHouse to be installed. | `22.8.11.15`       |
| `clickhouse_packages`| List of ClickHouse packages to install.| `clickhouse-common-static`, `clickhouse-client`, `clickhouse-server` |

#### Tags:
- `clickhouse`: Applies to all ClickHouse-related tasks.
- `database`: Applies to the database creation task.

#### Handlers:
- **Restart Clickhouse**: Restarts the `clickhouse-server` service.

#### Files and Templates:
- `clickhouse-client.service.j2`: Template for the `clickhouse-client` systemd unit file.

---

### 2. Vector Configuration
#### Playbook Name: `Install and configure Vector`

#### Description:
This playbook:
- Downloads and installs the specified version of Vector.
- Configures Vector as a systemd service.
- Deploys a configuration file for Vector.

#### Variables:
| Variable            | Description                              | Default Value |
|---------------------|------------------------------------------|---------------|
| `vector_version`    | Version of Vector to be installed.       | `0.22.1`      |
| `vector_config_file`| Path to the Vector configuration file.   | `/etc/vector/vector.toml` |
| `vector_template_src` | Path to the Vector configuration template.| `vector.toml.j2`|

#### Tags:
- `vector`: Applies to all Vector-related tasks.
- `config`: Applies to configuration deployment tasks.

#### Handlers:
- **Restart Vector**: Restarts the `vector` service.

#### Files and Templates:
- `vector.service.j2`: Template for the `vector` systemd unit file.
- `vector.toml.j2`: Template for the Vector configuration file.

---

### 3. LightHouse Configuration
#### Playbook Name: `Install and configure LightHouse`

#### Description:
This playbook:
- Installs necessary packages for LightHouse.
- Downloads and extracts LightHouse static files.
- Configures Nginx to serve LightHouse.
- Starts and enables the Nginx service.

#### Tasks:
- Install required packages (e.g., `nginx`).
- Download and extract LightHouse files.
- Deploy Nginx configuration for LightHouse.
- Start and enable Nginx service.

---

## Usage

### Prerequisites:
- Ansible 2.9 or later installed.
- Managed nodes with `yum` package manager and `systemd` support.

### Inventory File Example:
```ini
[clickhouse]
clickhouse_host ansible_host=192.168.1.10

[vector]
vector_host ansible_host=192.168.1.20

[lighthouse]
lighthouse_host ansible_host=192.168.1.30

[all]
all_hosts ansible_host=192.168.1.40
```

### Run the Playbook:
To apply the entire playbook:
```bash
ansible-playbook site.yml
```

To apply only ClickHouse-related tasks:
```bash
ansible-playbook site.yml --tags "clickhouse"
```

To apply only Vector-related tasks:
```bash
ansible-playbook site.yml --tags "vector"
```

To apply only LightHouse-related tasks:
```bash
ansible-playbook site.yml --tags "lighthouse"
```

---

## Troubleshooting

1. **Service Fails to Start**:
   - Check system logs using `journalctl -u <service_name>`.
   - Verify the configuration file syntax.

2. **Package Download Issues**:
   - Ensure the managed nodes have internet access.
   - Verify the URLs specified in the `vars` section.

3. **Database Creation Fails**:
   - Ensure the `clickhouse-server` service is running.
   - Check port `9000` is accessible.

---

## License
This playbook is open-source and available under the MIT license.