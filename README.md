# ansible-scaleway

Ansible playbook to deploy VM on scaleway IaaS

## Create a VM on Scaleway

### Prerequisites for Scaleway VM

* Create an account in Scaleway
* Generate an SSH key pair (called admin.key and admin.pub)
* Install prerequisites system packages

```bash
sudo apt-get install python3 python3-dev python3-pip build-essential libssl-dev libffi-dev jq
```

* Install Python packages on the local machine

```bash
pip3 install jinja2 PyYAML paramiko cryptography packaging
```

* Install Ansible (ideally from sources, as it evolves really fast)

If you don't want to install it from sources, you can use the PPA (see Ansible documentation)

```bash
sudo apt update
sudo apt install software-properties-common
sudo apt-add-repository --yes --update ppa:ansible/ansible
sudo apt install ansible
```

### Scaleway token

Create a token in Scaleway to authenticate Ansible access to the Scaleway API. In this repo, an empty environment file has been create with the name `scaleway_token`. Add you token in it and should look like this:

```bash
export SCW_API_KEY='aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
```

Source the file

```bash
source scaleway_token
```

### Create a VM

Use scaleway\_create\_vm.yaml to:

* get organisation id of the Scaleway account
* find a compatible Ubuntu image for the VM
* add the admin SSH key if necessary
* create the VM itself

```bash
ansible-playbook scaleway_create_vm.yml
```

If everything works, you should have a new VM in about 2 minutes in your Scaleway console.

Try to initiate a connection to this VM (don't forget to add your SSH key in agent or as an argument in `ssh` call)

```
ssh root@IP_found_in_console
[...]
Welcome on Ubuntu Bionic Beaver (18.04 LTS) (GNU/Linux 4.15.0-58-generic x86_64 )
```

### Install python and sudo on Scaleway instance

One issue that comes with Scaleway clean images is that they don't have neither sudo nor python, which will prevent us from using ansible. You can either install them manually, or use the following command to install them from your local machine:

```bash
ansible-playbook -i dynamic_inventory.yml -u root scaleway_install_prerequisites.yml
```

