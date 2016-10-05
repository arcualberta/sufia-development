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

Happy coding!