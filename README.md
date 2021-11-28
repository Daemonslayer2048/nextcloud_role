# Ansible Server NextCloud Role  
## Summary
__Graph:__
``` mermaid
graph LR;
  subgraph Roles
    common_role-->nextcloud_role;
    common_role-.->caddy_role;
  end
  subgraph Hosts
    nextcloud_role-->NextCloud;
    caddy_role-.->Caddy;
    Router-.Ports 80,443.->Caddy;
    Caddy-.Port 80.->NextCloud;
  end
```
This roles setups up a [NextCloud](https://nextcloud.com/) server. This role setup and installs mariadb, nextcloud and the required php files. When a Caddy reverse proxy is provided in the ansible inventory file the NextCloud hosts firewall will be opened allowing caddy to access NextCloud on port 80, create a Caddy proxy file, etc.

## Requirements
### ansible-galaxy
__Collections:__
  - [community.general](https://docs.ansible.com/ansible/latest/collections/community/general/index.html)

__Roles:__
  - [daemonslayer2048.common](https://github.com/Daemonslayer2048/common_role)

## Variables
| Variable | Type | Required | Default | Example |
|    -     |   -  |     -    |    -    |    -    |
| hostname | string | True | N/A | cloud |
| host_public_domain | string | True | N/A | null.com |
| nc_base_url | string | True | https://download.nextcloud.com/server/releases/ | https://download.nextcloud.com/server/releases/|
| nc_version | string | True | latest | nextcloud-9.0.53 |
| nc_data_path | string | True | /var/www/nextcloud/data | /var/www/nextcloud/data |
| nc_admin_user | string | True | N/A | admin |
| nc_admin_password | string | True | N/A | Password1! |
| php_mem_limit | int | True | 2048 | 2048 |
| mysql_encoding | string | True | utf8mb4 | utf8mb4 |
| mysql_application_database | string | True | nextcloud | nextcloud |
| mysql_application_user | string | True | nextcloud | nextcloud |
| mysql_root_password | string | True | N/A | Password1! |
| mysql_application_password | string | True | N/A | Password1! |


### Variable Summaries:
#### hostname
This is used in multiple locations to set the FQDN of the server and to be used in setting the public URL for the server when the [Caddy reverse proxy role](https://github.com/Daemonslayer2048/caddy_role) is deployed.

#### host_public_domain
Completes the domain portion of the public URL with the help of the variable above.

#### nc_base_url
This is the base URL of the the nextcloud download directory, unless you are pulling from a mirror you should not need to change this.

#### nc_version
The version of zip file to download, by default this is set to the latest package available.

#### nc_data_path
Where NextCloud should store its data, changing this is not recommended as it will most likely cause SELinux permission errors at the very least and this role (as of now) will not correct for this.

#### nc_admin_user
The default administrator to create for the instance. When using this role the install screen will not appear as the admin user and database setup is already handled. You will need to use this user to login.

#### nc_admin_password
The admin users password.
__Note:__ The password is piped into the php occ command so special characters can cause problems, be aware of this and avoid certain special characters.

#### php_mem_limit
The memory limit to set for php fpm.

#### mysql_encoding
The mysql database encoding to set, this should most likely not be changed.

#### mysql_application_database
The name of the database NextCloud should use.

#### mysql_application_user
The user Nextcloud will use to authenticate to the database.

#### mysql_root_password
The root password to set for the Mariadb service.

#### mysql_application_password
The application user NextCloud will use for authentication to the server.


## Notes:
### Testing Issues
  - Selinux Modules can not be tested in podman so these tests are performed outside of the stesting suite :(
