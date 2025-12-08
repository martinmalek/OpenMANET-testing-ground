# opentakserver-users

Creates users in OpenTAKServer using the API. Uses token authentication as documented in the [OpenTAKServer authentication guide](https://docs.opentakserver.io/authentication.html). Requires administrator role.

## Tasks

- Waits for OpenTAKServer API to be ready
- Authenticates with OpenTAKServer using token authentication
- Creates users via `/api/user/add` API endpoint
- Handles existing users gracefully (treats as success)

## Variables

Variables are defined in `defaults/main.yml`:

- `opentakserver_host`: OpenTAKServer hostname or IP (default: `{{ ansible_host | default(inventory_hostname) }}`)
- `opentakserver_port`: OpenTAKServer port (default: `8880`)
- `opentakserver_username`: Username for authentication (default: `administrator`)
- `opentakserver_password`: Password for authentication (default: `password`)
- `opentakserver_users`: List of users to create (default: includes `martin` and `leon` with password `atakatak`)

Each user entry can include:
- `username`: Username
- `password`: Password
- `email`: Email address (optional)
- `roles`: User roles (default: `["user"]`)

Override these in `group_vars/all.yml` or `host_vars/<hostname>.yml` if needed.

## Usage

```bash
# Create users in OpenTAKServer on gateway
ansible-playbook playbooks/site.yml --tags ots-users --limit gateway
```

## Notes

- The role targets the gateway device (where OpenTAKServer runs) but all tasks are delegated to `localhost` (the control machine) since they need to make API calls. The `opentakserver_host` variable automatically uses the target device's `ansible_host` to connect to the correct OpenTAKServer instance.
- This role requires administrator privileges. If a user already exists, the role will treat it as a successful outcome and continue.

## Reference

[OpenTAKServer User API](https://github.com/brian7704/OpenTAKServer/blob/master/opentakserver/blueprints/ots_api/user_api.py)
