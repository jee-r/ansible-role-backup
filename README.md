# ansible-role-backup

## Preps

```
mkdir ~/ansible
cd ansible
python -m venv venv
source venv/bin/activate
pip install ansible
mkdir roles 
```

## Install 
```
ansible-galaxy role install git+https://github.com/jee-r/ansible-role-backup.git,main -p ./roles/
```

### create a playbook

```yaml
- name: Install and Setup Rclone, Restic and Resticprofile
  hosts: all
  become: true

  roles:
    - roles/ansible-role-backup
```

## Usage


```
ansible-playbook my_playbook.yml --limit=<my_host> tags=<tags> -K
```


for tags see [tasks/main.yml](tasks/main.yml)

### Env

See [ansible-role-backup/defaults/main.yml](ansible-role-backup/defaults/main.yml)

example for my Arch laptop (locahost):

`host_vars/localhost.yaml`

```yaml
# Resticprofile

backends:
  - name: myNas
    repository: rclone:MyNAs:/Backup/myLaptop
    password_file: password_MyNas.txt
  - name: storagebox
    repository: rclone:storagebox:myLaptop
    password_file: password_storagebox.txt

# copy_backend:
#   repository: rclone:storagebox:myLaptop
#   password_file: password_storagebox.txt

sources:
  - name: home
    paths: 
      - /home
  - name: root
    paths: 
      - "/root"
  - name: sys
    paths: 
      - "/"
    exclude_add:
      - "/home"
      - "/root"
  - name: pkg-list
    paths: 
      - "/tmp/packages_list.txt"
    runbefore: 
      - sh -c "pacman -Q > /tmp/packages_list.txt"

schedule: true
schedule_backup: '*-*-* 12:15:00'
schedule_copy: '*-*-* 12:30:00'

exclude_base: 
  - "/var/cache/*"
  - "/var/tmp/*"
  - "/proc/*"
  - "/sys/*"
  - "/dev/*"
  - "/run/*"
  - "/tmp/*"
  - "/mnt/*"
  - "/swapfile"
  - "/root/.cache"
  - "/home/**/.cache"
  - "/home/*/.local/share/gnome-boxes/images/*"
  - "/home/*/.local/share/Trash/*"

main_config:
  default_command: snapshots
  nice: 15
  ionice: true
  ionice_class: 3
  min_memory: 500
  priority:  background
  restic_arguments_filter: true
  initialize: false
  restic_lock_retry_after: 1m
  restic_stale_lock_age: 3h
    
# Healthcheck

healthcheck_url: "https://healthchecks.io"
healthcheck_ping_key: "Your Ping Key"
healthcheck_api_key: "Your API Key"
healthcheck_hostname: "MyHostname
```