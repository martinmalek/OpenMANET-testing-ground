# docker

Configures Docker storage driver and data directory on OpenWrt devices.

## Tasks

- Configures Docker to use overlay2 storage driver
- Sets up Docker data directory on USB storage or overlay
- Creates Docker storage image file
- Configures Docker daemon

## Variables

Variables are defined in `defaults/main.yml`:

- `docker_storage_driver`: Storage driver (default: `overlay2`)
- `docker_data_root`: Docker data directory (default: `/opt/docker`)
- `docker_image_path`: Path to Docker storage image (default: `/overlay/docker.ext4`)
- `docker_image_size_gb`: Size of Docker storage image in GB (default: `20`)
- `docker_usb_device`: USB device for Docker storage (default: `/dev/sda1`)

Override these in `group_vars/all.yml` or `host_vars/<hostname>.yml` if needed.

## Usage

```bash
ansible-playbook playbooks/site.yml --tags docker
```

## Notes

After deployment, restart Docker:
```bash
ansible openmanet_devices -m shell -a "/etc/init.d/dockerd restart"
```
