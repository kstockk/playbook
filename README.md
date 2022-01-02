# playbook

This Anisble Playbook is heavily adopted from [notthebee/infra](https://github.com/notthebee/infra).

Ansible playbook for my Raspberry Pi 4.

## Preconfig

1. Create a bootable SSD using the latest version of Raspberry Pi Imager (Raspberry Pi OS Lite (32-bit). Ensure the following customisation options are configured using the Advanced Menu (Ctrl+Shift+X)

-   Disable overscan
-   Set the hostname
-   Enable SSH
-   Set a new password for the 'pi' user
-   Set the locale settings
-   Disable telemetry

2. Configure a static ip address `sudo nano /etc/dhcpcd.conf`

3. Check drive details and ensure all drives are labelled
```bash
sudo lsblk -o UUID,NAME,FSTYPE,SIZE,MOUNTPOINT,LABEL,MODEL
sudo e2label /dev/sdX1 label_name
```

4. If necessary, manually mount the drive to restore from timemachine backup
```bash
sudo mount -L timemachine /mnt/timemachine
sudo rsync

# to restore docker_data
sudo rsync -aAXv --delete /mnt/timemachine/docker_data /opt/docker/data
```

5. Configure dot files
6. Restore rclone config file `~/.config/rclone/rclone.conf`
7. Install [PiVPN](https://www.pivpn.io)
8. Run the Ansible Playbook
	- Ensure that the group_vars and host_vars are all configured before running the Ansible Playbook

## Usage

Install Ansible (macOS):
```
brew install ansible
```

Clone the repository:
```
git clone https://github.com/notthebee/infra
```

Create a host varialbe file and adjust the variables:
```
cd infra/ansible
mkdir -p host_vars/YOUR_HOSTNAME
vi host_vars/YOUR_HOSTNAME/vars.yml
```

Create a Keychain item for your Ansible Vault password (on macOS):
```
security add-generic-password \
               -a YOUR_USERNAME \
               -s ansible-vault-password \
               -w
```

The `pass.sh` script will extract the Ansible Vault password from your Keychain automatically each time Ansible requests it.

Create an encrypted `secret.yml` file and adjust the variables:
```
touch host_vars/YOUR_HOSTNAME/secret.yml
ansible-vault encrypt host_vars/YOUR_HOSTNAME/secret.yml
ansible-vault edit host_vars/YOUR_HOSTNAME/secret.yml
```

Add your custom inventory file to `hosts`:
```
cp hosts_example hosts
vi hosts
```

Install the dependencies:
```
ansible-galaxy install -r requirements.yml
```

Finally, run the playbook:
```
ansible-playbook run.yml -l your-host-here -K
```
The "-K" parameter is only necessary for the first run, since the playbook configures passwordless sudo for the main login user

For consecutive runs, if you only want to update the Docker containers, you can run the playbook like this:
```
ansible-playbook run.yml --tags="port,containers"
```
