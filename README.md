# Ansible Collection - sfey.hierarch

This collection is comprised of 13 ansible roles used in the [main hierarch repository](https://github.com/starshine-bcit/hierarch).

For details on arguments and examples, see either the `argument_specs.yml` for each role or consult the repo linked above for an example.

## Role Descriptions

Some care has been taken to write these in a generic and reusable way. That said, the various services are all designed to work with keycloak, or perhaps another OIDC-compatible IDP.

### borgmatic

[borgmatic](https://torsion.org/borgmatic/) is a utility which is used to define and automate [borg backups](https://borgbackup.readthedocs.io/en/stable/index.html) as configuration. Borg itself is a deduplicating backup program which supports compression and encryption.

This role allows you define backup locations, remote repos, how many daily backups you want to keep, etc. It also allows you to directly backup local postgres databases. For this feature to work, the root user should also have a superuser account with local authentication. You can also define custom before & after backup scripts in the event anything needs to be stopped or you need to run other backup scripts.

### cryptpad

[CryptPad](https://docs.cryptpad.org/en/) is an open source webapp-based collaboration suite focused on security and privacy.

The hierarch cryptpad role will set you up with a reasonably secure instance suitable for a small organization. Most of the configuration is done after the role is configured, through the Web UI as an admin.

In order to add an admin user, follow the directions [here](https://docs.cryptpad.org/en/admin_guide/installation.html#configure-administrators). There is also an ansible variable you can specify with the admin keys, then re-apply the configuration.

You will want to be sure to close registration here, but not close sso registration. You may also want to check the telemetry options under the administration -> network tab.

### forgejo

[Forgejo](https://forgejo.org/) is an open source alternative to Github, Gitlab, Gitea, and Bitbucket. It was initially a soft fork of Gitea after some controversy, but has now moved to be a hard fork.

This role sets you up with an instance using local file storage with LFS enabled. An admin user is provisioned. The instance is configured to only access to authenticated users in the appropriate group from the IDP. There is also a separate group which will make a user an instance admin.

The separate `runner` role install and configures an instance of forgejo-runner, allowing CI/CD.

### homepage

[homepage](https://gethomepage.dev/latest/) is described as a "A modern, fully static, fast, secure fully proxied, highly customizable application dashboard with integrations for over 100 services and translations into multiple languages".

It was chosen for this project for sake of simplicity, it's probably not appropriate for a production hierarch deployment. In the event you do want to use it, you will want to modify the services template file, as the values are hardcoded for the POC systems.

### keycloak

[keycloak](https://www.keycloak.org/) is a well know Open Source Identity and Access Management (IAM) suite.

It is the most important part of hierarch, acting as an identity provider via OpenID-Connect to all the other services. This role does not setup any of the providers and groups, etc., that you will need. However, there is a separate series of tasks imported into the main hierarch playbook which do this.

Once keycloak is configured, the realm admin (not the "master" admin) can login to provision users, or perhaps configure and grant another user permission to do this.

### letsencryptnginx

[nginx](https://nginx.org/en/) is a web server as well as generic proxy server. [Certbot](https://certbot.eff.org/) is a tool which can grant you free TLS certificates to use on your domains.

This role sets up certbot with automatic renewals for one domain (with various SANs), along with a mostly sane nginx config which supports an arbitrary number of sites via configuration. If you need help in configuring the variables for this one, refer to the documentation for the official [nginxinc ansible configuration role](https://galaxy.ansible.com/ui/standalone/roles/nginxinc/nginx_config/documentation/).

### mailcow

[mailcow](https://mailcow.email/) is a collection of 19 docker containers which serve as a fully contained, self-hosted mail suite.

This role should be deployed on a standalone VPS which does not block outgoing email traffic (even if you are intending to use an external SMTP service). Once it is started, the admin user can login and configure the server as needed. Notably, they will need to configure the SSO via the WebUI admin panel and configure the default sending domain before anyone can login.

### orangehrm

[OrangeHRM](https://www.orangehrm.com/) is an open source Human Resource Management System (HRMS). It is deployed on docker due to it's obscure and outdated system requirements.

After giving this role, you will need to login and complete the "guided" setup guide via the WebUI. During this process you will create the initial admin user. Once that is done, then you can go ahead and configure "Social Login", which is actually OIDC.

Unlike all the other user-facing services in this collection, OrangeHRM will not automatically provision a new user from "Social Login". Instead, an employee must be created inside the software first, with their email matching the email from the IDP.

### postgres

[PostgreSQL](https://www.postgresql.org/) is billed as the world's most advanced open source relational database.

Since it is used as the database of choice in hierarch, this role allows one to easily deploy an instance with a variety of databases and users.

### runner

[Forgejo Runner](https://code.forgejo.org/forgejo/runner) is an alpha-quality runner which depends on a fork of a local runner for github actions. It is mostly designed to be a drop-in replacement for github actions. Should you need something more stable or ready for a production workload, there are other options available for Forgejo or Gitea that should work.

This role is meant to be deployed alongside an instance of Forgejo, thought they do not need to exist on the same server. It works by periodically polling the Forgejo API for jobs to work on.

### synapse

[matrix](https://matrix.org/) is an open network for sercure, decentralised communication. [Synapse](https://matrix-org.github.io/synapse/latest/welcome_and_overview.html) is a open source and feature-rich server for the matrix network. [Element Web](https://web-docs.element.dev/Element%20Web/index.html) is a webapp which allows user interaction with the network and Synapse server(s). [coturn](https://github.com/coturn/coturn) is a free open source implementation of a TURN and STUN server. It is used by Synapse/Element to facilitate 1-1 video/audio communications.

This role deploys all the above services, configured to work in tandem. The Synapse server is not federated and will only allow regular users to login via the IDP. Encryption is not enforced by default, but it's trivial to enable.

If one wanted fully self-hosted group meetings, then a [Jitsi Meet](https://meet.jit.si/) server will need to be setup and configured. Depending on how much many meetings and how many users you have, you may want to deploy this on a dedicated VPS/server.

### ufw

[ufw](https://launchpad.net/ufw) is the uncomplicated firewall, which aims to be an easy to configure netfilter firewall.

Configuration for this role is provided by a simple allow in and limit in rules for tcp and udp. Outgoing traffic is be default allowed and incoming by default denied.

### wikijs

[Wiki.js](https://js.wiki/) is an open source Wiki software, which unsuprisingly features a lot of javascript.

This role deploys an instance which will require additional configuration by the admin user via the WebUI. Notably, self-registration should be disabled and the IDP configured. With that done, an admin can setup roles, default permissions, etc.

## Credits

Thanks to all contributers who of the roles/collections in the `requirements.yml`.

Special thanks to [enough](https://lab.enough.community/main/infrastructure) and [ansible-middleware](https://github.com/ansible-middleware/keycloak), as some of the roles here are based on their work.

## License

AGPLv3.0 or later