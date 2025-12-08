# govtak

Deploys GovTAK Server (based on [Cloud-RF/tak-server](https://github.com/Cloud-RF/tak-server)) installation scripts and Docker Compose configuration.

## Tasks

- Installs required packages (git, bash, unzip, etc.)
- Clones Cloud-RF/tak-server repository
- Copies modified setup scripts (`setup.sh`, `certDP.sh`, `shareCerts.sh`)
- Copies `docker-compose.arm.yml` configuration
- Transfers TAK Server ZIP file from local machine to device
- Optionally runs `setup.sh` automatically
- Verifies deployment

## Variables

Variables are defined in `defaults/main.yml`:

- `tak_server_dir`: TAK Server directory (default: `~/tak-server`)
- `tak_server_scripts_dir`: Scripts directory (default: `~/tak-server/scripts`)
- `tak_server_repo_url`: Repository URL (default: `https://github.com/Cloud-RF/tak-server.git`)
- `tak_server_zip_path`: Local path to TAK Server ZIP file (default: `~/Downloads/takserver-docker-5.6-RELEASE.zip`)
- `tak_server_zip_name`: Name of ZIP file on device (auto-detected from path if not set)
- `tak_server_run_setup`: Automatically run setup.sh after deployment (default: `false`)

## Usage

```bash
# Deploy scripts and repository (without ZIP file)
ansible-playbook playbooks/site.yml --tags govtak

# Deploy with TAK Server ZIP file (defaults to ~/Downloads/takserver-docker-5.6-RELEASE.zip)
ansible-playbook playbooks/site.yml --tags govtak

# Or specify a different path
ansible-playbook playbooks/site.yml --tags govtak \
  -e "tak_server_zip_path=~/Downloads/takserver-docker-5.6-RELEASE.zip"

# Deploy and automatically run setup.sh
ansible-playbook playbooks/site.yml --tags govtak \
  -e "tak_server_run_setup=true"
```

## Notes

The TAK Server ZIP file must be downloaded from [tak.gov/products/tak-server](https://tak.gov/products/tak-server) and placed on your local machine. The role will transfer it to the device automatically. If `tak_server_run_setup` is `false`, you'll need to run `setup.sh` manually on the device.
