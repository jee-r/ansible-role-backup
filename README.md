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

