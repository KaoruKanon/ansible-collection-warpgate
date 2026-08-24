# install

Install warpgate

## Table of contents

- [Requirements](#requirements)
- [Default Variables](#default-variables)
  - [warpgate_admin_password](#warpgate_admin_password)
  - [warpgate_config_file_path](#warpgate_config_file_path)
  - [warpgate_data_path](#warpgate_data_path)
  - [warpgate_database_backend](#warpgate_database_backend)
  - [warpgate_database_postgres_database](#warpgate_database_postgres_database)
  - [warpgate_database_postgres_host](#warpgate_database_postgres_host)
  - [warpgate_database_postgres_password](#warpgate_database_postgres_password)
  - [warpgate_database_postgres_port](#warpgate_database_postgres_port)
  - [warpgate_database_postgres_sslmode](#warpgate_database_postgres_sslmode)
  - [warpgate_database_postgres_username](#warpgate_database_postgres_username)
  - [warpgate_database_url](#warpgate_database_url)
  - [warpgate_docker_container_name](#warpgate_docker_container_name)
  - [warpgate_docker_extra_env](#warpgate_docker_extra_env)
  - [warpgate_docker_extra_hosts](#warpgate_docker_extra_hosts)
  - [warpgate_docker_extra_ports](#warpgate_docker_extra_ports)
  - [warpgate_docker_extra_volumes](#warpgate_docker_extra_volumes)
  - [warpgate_docker_host_data_path](#warpgate_docker_host_data_path)
  - [warpgate_docker_image](#warpgate_docker_image)
  - [warpgate_docker_image_tag](#warpgate_docker_image_tag)
  - [warpgate_docker_log_driver](#warpgate_docker_log_driver)
  - [warpgate_docker_network_mode](#warpgate_docker_network_mode)
  - [warpgate_docker_publish_ports](#warpgate_docker_publish_ports)
  - [warpgate_docker_pull](#warpgate_docker_pull)
  - [warpgate_docker_restart_policy](#warpgate_docker_restart_policy)
  - [warpgate_external_host](#warpgate_external_host)
  - [warpgate_http_certificate](#warpgate_http_certificate)
  - [warpgate_http_cookie_max_age](#warpgate_http_cookie_max_age)
  - [warpgate_http_external_host](#warpgate_http_external_host)
  - [warpgate_http_external_port](#warpgate_http_external_port)
  - [warpgate_http_key](#warpgate_http_key)
  - [warpgate_http_port](#warpgate_http_port)
  - [warpgate_http_session_max_age](#warpgate_http_session_max_age)
  - [warpgate_http_sni_certificates](#warpgate_http_sni_certificates)
  - [warpgate_http_trust_x_forwarded_headers](#warpgate_http_trust_x_forwarded_headers)
  - [warpgate_install_method](#warpgate_install_method)
  - [warpgate_kubernetes_certificate](#warpgate_kubernetes_certificate)
  - [warpgate_kubernetes_enabled](#warpgate_kubernetes_enabled)
  - [warpgate_kubernetes_external_host](#warpgate_kubernetes_external_host)
  - [warpgate_kubernetes_external_port](#warpgate_kubernetes_external_port)
  - [warpgate_kubernetes_key](#warpgate_kubernetes_key)
  - [warpgate_kubernetes_port](#warpgate_kubernetes_port)
  - [warpgate_kubernetes_session_max_age](#warpgate_kubernetes_session_max_age)
  - [warpgate_log_audit_retention](#warpgate_log_audit_retention)
  - [warpgate_log_format](#warpgate_log_format)
  - [warpgate_log_retention](#warpgate_log_retention)
  - [warpgate_log_send_to](#warpgate_log_send_to)
  - [warpgate_mysql_certificate](#warpgate_mysql_certificate)
  - [warpgate_mysql_enabled](#warpgate_mysql_enabled)
  - [warpgate_mysql_external_host](#warpgate_mysql_external_host)
  - [warpgate_mysql_external_port](#warpgate_mysql_external_port)
  - [warpgate_mysql_key](#warpgate_mysql_key)
  - [warpgate_mysql_port](#warpgate_mysql_port)
  - [warpgate_postgres_certificate](#warpgate_postgres_certificate)
  - [warpgate_postgres_enabled](#warpgate_postgres_enabled)
  - [warpgate_postgres_external_host](#warpgate_postgres_external_host)
  - [warpgate_postgres_external_port](#warpgate_postgres_external_port)
  - [warpgate_postgres_key](#warpgate_postgres_key)
  - [warpgate_postgres_port](#warpgate_postgres_port)
  - [warpgate_record_sessions](#warpgate_record_sessions)
  - [warpgate_recordings_path](#warpgate_recordings_path)
  - [warpgate_service_enabled](#warpgate_service_enabled)
  - [warpgate_service_state](#warpgate_service_state)
  - [warpgate_ssh_enabled](#warpgate_ssh_enabled)
  - [warpgate_ssh_external_host](#warpgate_ssh_external_host)
  - [warpgate_ssh_external_port](#warpgate_ssh_external_port)
  - [warpgate_ssh_host_key_verification](#warpgate_ssh_host_key_verification)
  - [warpgate_ssh_inactivity_timeout](#warpgate_ssh_inactivity_timeout)
  - [warpgate_ssh_keepalive_interval](#warpgate_ssh_keepalive_interval)
  - [warpgate_ssh_keys_path](#warpgate_ssh_keys_path)
  - [warpgate_ssh_port](#warpgate_ssh_port)
  - [warpgate_sso_providers](#warpgate_sso_providers)
  - [warpgate_system_group](#warpgate_system_group)
  - [warpgate_system_user](#warpgate_system_user)
  - [warpgate_tls_certificate_path](#warpgate_tls_certificate_path)
  - [warpgate_tls_key_path](#warpgate_tls_key_path)
  - [warpgate_version](#warpgate_version)
- [Dependencies](#dependencies)
- [License](#license)
- [Author](#author)

---

## Requirements

- Minimum Ansible version: `2.1`
- When `warpgate_install_method` is `docker`: the `community.docker` collection (`>=3.0.0`), and Docker Engine already installed and running on the target host (this role does not install Docker itself).

## Default Variables

### warpgate_admin_password

Warpgate admin password

**_Type:_** string<br />

### warpgate_config_file_path

Warpgate config path

**_Type:_** string<br />

#### Default value

```YAML
warpgate_config_file_path: '{{ warpgate_config_file_directory }}/warpgate.yaml'
```

### warpgate_data_path

Warpgate data path: config file, SQLite database (if used), TLS
certificates, SSH host keys and session recordings all live under it.
When `warpgate_install_method` is `docker` this is the path *inside the
container* (fixed to `/data` by the official image) — use
`warpgate_docker_host_data_path` to control where that data actually
lives on the host.

**_Type:_** string<br />

#### Default value

```YAML
warpgate_data_path: "{{ '/data' if warpgate_install_method == 'docker' else '/var/lib/warpgate' }}"
```

### warpgate_database_backend

Backend datastore used by warpgate itself to store its own state (users,
roles, targets, sessions, ...): `sqlite` or `postgres`. This is unrelated
to [warpgate_postgres_enabled](#warpgate_postgres_enabled), which only
controls whether warpgate exposes a PostgreSQL-protocol *listener* used
to proxy connections to PostgreSQL targets.

**_Type:_** string<br />

#### Default value

```YAML
warpgate_database_backend: sqlite
```

### warpgate_database_postgres_database

Database name used as warpgate's own database

**_Type:_** string<br />

#### Default value

```YAML
warpgate_database_postgres_database: warpgate
```

### warpgate_database_postgres_host

Hostname of the PostgreSQL server used as warpgate's own database (only
used when `warpgate_database_backend` is `postgres`)

**_Type:_** string<br />

#### Default value

```YAML
warpgate_database_postgres_host: localhost
```

### warpgate_database_postgres_password

Password used to connect to warpgate's own PostgreSQL database (required
when `warpgate_database_backend` is `postgres`)

**_Type:_** string<br />

### warpgate_database_postgres_port

Port of the PostgreSQL server used as warpgate's own database

**_Type:_** int<br />

#### Default value

```YAML
warpgate_database_postgres_port: 5432
```

### warpgate_database_postgres_sslmode

`sslmode` query parameter appended to the generated database URL (e.g.
`disable`, `require`, `verify-full`). `null` omits the parameter.

**_Type:_** string<br />

#### Default value

```YAML
warpgate_database_postgres_sslmode:
```

### warpgate_database_postgres_username

Username used to connect to warpgate's own PostgreSQL database

**_Type:_** string<br />

#### Default value

```YAML
warpgate_database_postgres_username: warpgate
```

### warpgate_database_url

Warpgate database URL. Auto-generated from `warpgate_database_backend`
and, when set to `postgres`, the `warpgate_database_postgres_*`
variables above. Set this variable directly to bypass the generated
value entirely.

**_Type:_** string<br />

#### Default value

```YAML
warpgate_database_url: >-
  {{
    (
      'postgres://' ~ (warpgate_database_postgres_username | urlencode) ~ ':' ~ (warpgate_database_postgres_password | urlencode)
      ~ '@' ~ warpgate_database_postgres_host ~ ':' ~ warpgate_database_postgres_port ~ '/' ~ warpgate_database_postgres_database
      ~ (('?sslmode=' ~ warpgate_database_postgres_sslmode) if warpgate_database_postgres_sslmode else '')
    )
    if warpgate_database_backend == 'postgres'
    else ('sqlite:' ~ warpgate_data_path ~ '/warpgate.db')
  }}
```

### warpgate_docker_container_name

Name of the Warpgate Docker container

**_Type:_** string<br />

#### Default value

```YAML
warpgate_docker_container_name: warpgate
```

### warpgate_docker_extra_env

Additional environment variables passed to the container

**_Type:_** dict<br />

#### Default value

```YAML
warpgate_docker_extra_env: {}
```

### warpgate_docker_extra_hosts

Additional `/etc/hosts` entries for the container, e.g. `["db.internal:10.0.0.5"]`

**_Type:_** list<br />

#### Default value

```YAML
warpgate_docker_extra_hosts: []
```

### warpgate_docker_extra_ports

Additional Docker port mappings, e.g. `["8443:8443"]`

**_Type:_** list<br />

#### Default value

```YAML
warpgate_docker_extra_ports: []
```

### warpgate_docker_extra_volumes

Additional Docker volume mounts, e.g. `["/etc/ssl/extra-ca.pem:/etc/ssl/extra-ca.pem:ro"]`

**_Type:_** list<br />

#### Default value

```YAML
warpgate_docker_extra_volumes: []
```

### warpgate_docker_host_data_path

Host path bind-mounted to the container's `/data` directory (config
file, database, TLS material, recordings, SSH host keys)

**_Type:_** string<br />

#### Default value

```YAML
warpgate_docker_host_data_path: /var/lib/warpgate
```

### warpgate_docker_image

Warpgate Docker image name

**_Type:_** string<br />

#### Default value

```YAML
warpgate_docker_image: ghcr.io/warp-tech/warpgate
```

### warpgate_docker_image_tag

Warpgate Docker image tag

**_Type:_** string<br />

#### Default value

```YAML
warpgate_docker_image_tag: '{{ warpgate_version }}'
```

### warpgate_docker_log_driver

Docker logging driver for the container (`null` = Docker default)

**_Type:_** string<br />

#### Default value

```YAML
warpgate_docker_log_driver:
```

### warpgate_docker_network_mode

Docker network mode for the container (e.g. `bridge`, `host`). `null`
uses the Docker default.

**_Type:_** string<br />

#### Default value

```YAML
warpgate_docker_network_mode:
```

### warpgate_docker_publish_ports

Automatically publish the enabled listener ports (HTTP, and SSH / MySQL
/ PostgreSQL / Kubernetes when enabled) on the same host port number.
Disable to manage publishing yourself via `warpgate_docker_extra_ports`
(e.g. when `warpgate_docker_network_mode` is `host`).

**_Type:_** boolean<br />

#### Default value

```YAML
warpgate_docker_publish_ports: true
```

### warpgate_docker_pull

Always re-pull the image, even if a matching tag is already present
locally

**_Type:_** boolean<br />

#### Default value

```YAML
warpgate_docker_pull: true
```

### warpgate_docker_restart_policy

Docker restart policy applied while `warpgate_service_enabled` is true
(`no`, `always`, `on-failure`, `unless-stopped`)

**_Type:_** string<br />

#### Default value

```YAML
warpgate_docker_restart_policy: unless-stopped
```

### warpgate_external_host

Warpgate external host

**_Type:_** string<br />

#### Default value

```YAML
warpgate_external_host: localhost
```

### warpgate_http_certificate

TLS certificate path for the HTTP listener

**_Type:_** string<br />

#### Default value

```YAML
warpgate_http_certificate: '{{ warpgate_data_path }}/tls.certificate.pem'
```

### warpgate_http_cookie_max_age

Maximum lifetime of the auth cookie

**_Type:_** string<br />

#### Default value

```YAML
warpgate_http_cookie_max_age: 1day
```

### warpgate_http_external_host

Public hostname advertised for the HTTP listener (null = auto)

**_Type:_** string<br />

#### Default value

```YAML
warpgate_http_external_host:
```

### warpgate_http_external_port

Public port advertised for the HTTP listener (null = auto)

**_Type:_** int<br />

#### Default value

```YAML
warpgate_http_external_port:
```

### warpgate_http_key

TLS key path for the HTTP listener

**_Type:_** string<br />

#### Default value

```YAML
warpgate_http_key: '{{ warpgate_data_path }}/tls.key.pem'
```

### warpgate_http_port

Warpgate HTTP port

**_Type:_** int<br />

#### Default value

```YAML
warpgate_http_port: 8888
```

### warpgate_http_session_max_age

Maximum lifetime of an HTTP session

**_Type:_** string<br />

#### Default value

```YAML
warpgate_http_session_max_age: 30m
```

### warpgate_http_sni_certificates

Additional SNI certificates for the HTTP listener

**_Type:_** list<br />

#### Default value

```YAML
warpgate_http_sni_certificates: []
```

### warpgate_http_trust_x_forwarded_headers

Trust X-Forwarded-* headers (enable only when running behind a trusted reverse proxy)

**_Type:_** boolean<br />

#### Default value

```YAML
warpgate_http_trust_x_forwarded_headers: false
```

### warpgate_install_method

How warpgate is installed and run: `binary` installs the standalone
release binary and manages it via systemd, `docker` runs the official
Warpgate container image via the Docker Engine (see the `Docker`
variables below).

**_Type:_** string<br />

#### Default value

```YAML
warpgate_install_method: binary
```

### warpgate_kubernetes_certificate

TLS certificate path for the Kubernetes listener

**_Type:_** string<br />

#### Default value

```YAML
warpgate_kubernetes_certificate: '{{ warpgate_data_path }}/tls.certificate.pem'
```

### warpgate_kubernetes_enabled

Warpgate Kubernetes enabled

**_Type:_** boolean<br />

#### Default value

```YAML
warpgate_kubernetes_enabled: false
```

### warpgate_kubernetes_external_host

Public hostname advertised for the Kubernetes listener (null = auto)

**_Type:_** string<br />

#### Default value

```YAML
warpgate_kubernetes_external_host:
```

### warpgate_kubernetes_external_port

Public port advertised for the Kubernetes listener (null = auto)

**_Type:_** int<br />

#### Default value

```YAML
warpgate_kubernetes_external_port:
```

### warpgate_kubernetes_key

TLS key path for the Kubernetes listener

**_Type:_** string<br />

#### Default value

```YAML
warpgate_kubernetes_key: '{{ warpgate_data_path }}/tls.key.pem'
```

### warpgate_kubernetes_port

Warpgate Kubernetes port

**_Type:_** int<br />

#### Default value

```YAML
warpgate_kubernetes_port: 8443
```

### warpgate_kubernetes_session_max_age

Maximum lifetime of a Kubernetes session

**_Type:_** string<br />

#### Default value

```YAML
warpgate_kubernetes_session_max_age: 30m
```

### warpgate_log_audit_retention

Audit log retention duration

**_Type:_** string<br />

#### Default value

```YAML
warpgate_log_audit_retention: 11months 30days 3h 50m 24s
```

### warpgate_log_format

Log output format (text or json)

**_Type:_** string<br />

#### Default value

```YAML
warpgate_log_format: text
```

### warpgate_log_retention

Log retention duration

**_Type:_** string<br />

#### Default value

```YAML
warpgate_log_retention: 7days
```

### warpgate_log_send_to

Remote log endpoint (null = log locally only)

**_Type:_** string<br />

#### Default value

```YAML
warpgate_log_send_to:
```

### warpgate_mysql_certificate

TLS certificate path for the MySQL listener

**_Type:_** string<br />

#### Default value

```YAML
warpgate_mysql_certificate: '{{ warpgate_data_path }}/tls.certificate.pem'
```

### warpgate_mysql_enabled

Warpgate MySQL enabled

**_Type:_** boolean<br />

#### Default value

```YAML
warpgate_mysql_enabled: false
```

### warpgate_mysql_external_host

Public hostname advertised for the MySQL listener (null = auto)

**_Type:_** string<br />

#### Default value

```YAML
warpgate_mysql_external_host:
```

### warpgate_mysql_external_port

Public port advertised for the MySQL listener (null = auto)

**_Type:_** int<br />

#### Default value

```YAML
warpgate_mysql_external_port:
```

### warpgate_mysql_key

TLS key path for the MySQL listener

**_Type:_** string<br />

#### Default value

```YAML
warpgate_mysql_key: '{{ warpgate_data_path }}/tls.key.pem'
```

### warpgate_mysql_port

Warpgate MySQL port

**_Type:_** int<br />

#### Default value

```YAML
warpgate_mysql_port: 33306
```

### warpgate_postgres_certificate

TLS certificate path for the PostgreSQL listener

**_Type:_** string<br />

#### Default value

```YAML
warpgate_postgres_certificate: '{{ warpgate_data_path }}/tls.certificate.pem'
```

### warpgate_postgres_enabled

Enable warpgate's PostgreSQL-protocol *listener*, used to proxy client
connections through to PostgreSQL targets. Unrelated to
[warpgate_database_backend](#warpgate_database_backend), which selects
warpgate's own storage engine.

**_Type:_** boolean<br />

#### Default value

```YAML
warpgate_postgres_enabled: false
```

### warpgate_postgres_external_host

Public hostname advertised for the PostgreSQL listener (null = auto)

**_Type:_** string<br />

#### Default value

```YAML
warpgate_postgres_external_host:
```

### warpgate_postgres_external_port

Public port advertised for the PostgreSQL listener (null = auto)

**_Type:_** int<br />

#### Default value

```YAML
warpgate_postgres_external_port:
```

### warpgate_postgres_key

TLS key path for the PostgreSQL listener

**_Type:_** string<br />

#### Default value

```YAML
warpgate_postgres_key: '{{ warpgate_data_path }}/tls.key.pem'
```

### warpgate_postgres_port

Warpgate PostgreSQL listener port

**_Type:_** int<br />

#### Default value

```YAML
warpgate_postgres_port: 55432
```

### warpgate_record_sessions

Warpgate record sessions

**_Type:_** boolean<br />

#### Default value

```YAML
warpgate_record_sessions: true
```

### warpgate_recordings_path

Path where session recordings are stored

**_Type:_** string<br />

#### Default value

```YAML
warpgate_recordings_path: '{{ warpgate_data_path }}/recordings'
```

### warpgate_service_enabled

Enable warpgate service

**_Type:_** boolean<br />

#### Default value

```YAML
warpgate_service_enabled: true
```

### warpgate_service_state

warpgate service desired state

**_Type:_** string<br />

#### Default value

```YAML
warpgate_service_state: started
```

### warpgate_ssh_enabled

Warpgate SSH enabled

**_Type:_** boolean<br />

#### Default value

```YAML
warpgate_ssh_enabled: false
```

### warpgate_ssh_external_host

Public hostname advertised for the SSH listener (null = auto)

**_Type:_** string<br />

#### Default value

```YAML
warpgate_ssh_external_host:
```

### warpgate_ssh_external_port

Public port advertised for the SSH listener (null = auto)

**_Type:_** int<br />

#### Default value

```YAML
warpgate_ssh_external_port:
```

### warpgate_ssh_host_key_verification

SSH target host key verification mode (prompt, auto, strict)

**_Type:_** string<br />

#### Default value

```YAML
warpgate_ssh_host_key_verification: prompt
```

### warpgate_ssh_inactivity_timeout

Idle timeout before an SSH session is closed

**_Type:_** string<br />

#### Default value

```YAML
warpgate_ssh_inactivity_timeout: 5m
```

### warpgate_ssh_keepalive_interval

Keepalive interval for SSH sessions (null disables keepalive)

**_Type:_** string<br />

#### Default value

```YAML
warpgate_ssh_keepalive_interval:
```

### warpgate_ssh_keys_path

Directory holding Warpgate's SSH host keys

**_Type:_** string<br />

#### Default value

```YAML
warpgate_ssh_keys_path: '{{ warpgate_data_path }}/ssh-keys'
```

### warpgate_ssh_port

Warpgate SSH port

**_Type:_** int<br />

#### Default value

```YAML
warpgate_ssh_port: 2222
```

### warpgate_sso_providers

Warpgate SSO providers

**_Type:_** list<br />

#### Default value

```YAML
warpgate_sso_providers: []
```

### warpgate_system_group

System group name to create

**_Type:_** string<br />

#### Default value

```YAML
warpgate_system_group: warpgate
```

### warpgate_system_user

System user name to create

**_Type:_** string<br />

#### Default value

```YAML
warpgate_system_user: warpgate
```

### warpgate_tls_certificate_path

Default TLS certificate path used by HTTP / Kubernetes / MySQL / PostgreSQL listeners

**_Type:_** string<br />

#### Default value

```YAML
warpgate_tls_certificate_path: '{{ warpgate_data_path }}/tls.certificate.pem'
```

### warpgate_tls_key_path

Default TLS key path used by HTTP / Kubernetes / MySQL / PostgreSQL listeners

**_Type:_** string<br />

#### Default value

```YAML
warpgate_tls_key_path: '{{ warpgate_data_path }}/tls.key.pem'
```

### warpgate_version

warpgate version to install

**_Type:_** string<br />

#### Default value

```YAML
warpgate_version: 0.27.1
```

## Dependencies

None.

## License

MPL2

## Author

Clément Hubert
