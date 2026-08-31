# Ansible roles and playbooks for installing Apache, MySQL, and WordPress

This repository contains Ansible roles and playbooks for automating the installation and configuration of Apache, MySQL/MariaDB, and WordPress on separate web and database servers. The roles are designed to be reusable and can be easily integrated into larger Ansible projects.

The roles support both Ubuntu and AlmaLinux by loading OS-family-specific variables for package names, service names, paths, and web server users.

## Directory Structure
```
.
├── ansible.cfg
├── collections
│   └── requirements.yml
├── inventory.ini.example
├── README.md
├── playbooks
│   ├── install_apache.yml
│   ├── install_mysql.yml
│   ├── install_wordpress.yml
│   └── vars
│       ├── apache.yml
│       ├── mysql.yml
│       └── wordpress.yml
├── roles
│   ├── apache
│   │   ├── defaults
│   │   │   └── main.yml
│   │   ├── handlers
│   │   │   └── main.yml
│   │   ├── tasks
│   │   │   └── main.yml
│   │   ├── templates
│   │   │   └── apache.conf.j2
│   │   └── vars
│   │       ├── Debian.yml
│   │       └── RedHat.yml
│   ├── mysql
│   │   ├── defaults
│   │   │   └── main.yml
│   │   ├── handlers
│   │   │   └── main.yml
│   │   ├── tasks
│   │   │   └── main.yml
│   │   ├── templates
│   │   │   └── my.cnf.j2
│   │   └── vars
│   │       ├── Debian.yml
│   │       └── RedHat.yml
│   └── wordpress
│       ├── defaults
│       │   └── main.yml
│       ├── tasks
│       │   └── main.yml
│       ├── templates
│       │   └── wp-config.php.j2
│       └── vars
│           ├── Debian.yml
│           └── RedHat.yml
```

## General Idea

1. **Apache Role**: This role installs and configures the Apache web server. It includes tasks for installing Apache, setting up virtual hosts, and applying custom configurations using templates.
2. **MySQL Role**: This role installs and configures the database server. It uses MySQL packages on Ubuntu and MariaDB packages on AlmaLinux, creates databases and users, and applies custom configurations using templates.
3. **WordPress Role**: This role installs and configures WordPress on the web server. It downloads WordPress, writes the configuration file, installs WP-CLI, runs the WordPress installer, and creates the initial admin account.

## Supported Operating Systems

- Ubuntu, via Ansible's `Debian` OS family.
- AlmaLinux, via Ansible's `RedHat` OS family.

## Usage

Install the required Ansible collection:

```bash
ansible-galaxy collection install -r collections/requirements.yml
```

Create an inventory from the example:

```bash
cp inventory.ini.example inventory.ini
```

The example inventory uses `web1` as the web server and `db2` as the database server:

```ini
[webservers]
web1

[dbservers]
db2
```

Run the full WordPress installation:

```bash
ansible-playbook playbooks/install_wordpress.yml
```

This playbook installs MySQL/MariaDB on `db2`, then installs Apache and WordPress on `web1`. Deployment-specific variables are set in `playbooks/vars/` so the roles stay reusable.

After the playbook has completed, WordPress is installed and the admin user can log in at:

```text
http://web1/wp-admin/
```

The full playbook loads deployment variables from:

- `playbooks/vars/apache.yml`
- `playbooks/vars/mysql.yml`
- `playbooks/vars/wordpress.yml`

These files set values such as:

```yaml
mysql_database_name: wordpress
mysql_database_user: wordpress
mysql_database_password: change-me
mysql_database_user_host: web1
mysql_bind_address: 0.0.0.0
wordpress_db_host: db2
wordpress_site_url: http://web1
wordpress_site_title: My WordPress Site
wordpress_admin_user: admin
wordpress_admin_password: change-me-now
wordpress_admin_email: admin@example.com
```

For a real environment, replace passwords and admin email before running the playbook. These variables can also be moved to inventory or `group_vars` if preferred.
