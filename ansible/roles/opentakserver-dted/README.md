# opentakserver-dted

Uploads DTED (Digital Terrain Elevation Data) files from GitHub releases to OpenTAKServer. Uses token authentication as documented in the [OpenTAKServer authentication guide](https://docs.opentakserver.io/authentication.html).

## Tasks

- Downloads DTED files from GitHub release
- Authenticates with OpenTAKServer using token authentication
- Uploads DTED files to OpenTAKServer via `/api/data_packages` API endpoint
- Cleans up temporary download files

## Variables

Variables are defined in `defaults/main.yml`:

- `opentakserver_host`: OpenTAKServer hostname or IP (default: `{{ ansible_host | default(inventory_hostname) }}`)
- `opentakserver_port`: OpenTAKServer port (default: `8880`)
- `opentakserver_username`: Username for authentication (default: `administrator`)
- `opentakserver_password`: Password for authentication (default: `password`)
- `dted_release_tag`: GitHub release tag (default: `atak-dted-scandinavia`)
- `dted_release_url`: GitHub release URL (default: constructed from release tag)
- `dted_files`: List of DTED files to upload (default: includes North DTED, large file commented out)
- `dted_temp_dir`: Temporary directory for downloads (default: `/tmp/dted_uploads`)

Override these in `group_vars/all.yml` or `host_vars/<hostname>.yml` if needed.

## Usage

```bash
# Upload DTED files to OpenTAKServer on gateway
ansible-playbook playbooks/site.yml --tags ots-dted --limit gateway
```

## Notes

- The role targets the gateway device (where OpenTAKServer runs) but all tasks are delegated to `localhost` (the control machine) since they need to download files from GitHub and make API calls. The `opentakserver_host` variable automatically uses the target device's `ansible_host` to connect to the correct OpenTAKServer instance.
- This role runs on `localhost` (the control machine) and requires `curl` and `python3` to be installed. It downloads files from GitHub and uploads them to OpenTAKServer via the API.
