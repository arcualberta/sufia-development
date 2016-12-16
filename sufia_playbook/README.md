# Setting up Sufia development environment

## First time setup

```bash
sudo easy_install pip
sudo pip install ansible
```

Download and install [Vagrant](https://www.vagrantup.com/ )

Download and install [VirtualBox](https://www.virtualbox.org/)

## After initial setup and for future deployments

Go to the directory where the Vagrantfile and provision.yml files are saved and start virtual machine

```bash
vagrant up
```

Log into the virtual machine

```bash
vagrant ssh
```

Go to project directory on the virtual machine and start services

```bash
cd ~/workspace/sufiaApp
rake hydra:server
```

After the server is up and running, you can access the application on [http://192.168.2.2:3000](http://192.168.2.2:3000). 

User accounts:

The ansible playbook will create two user accounts

L: user@ualberta.ca
P: user123

L: admin@ualberta.ca
P: admin123

The admin@ualberta.ca account has full access to the Administration dashboard.

Happy coding!