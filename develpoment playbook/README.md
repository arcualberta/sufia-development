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
rails hydra:server
```

After the server is up and running, you can access the application on [http://192.168.2.2:3000](http://192.168.2.2:3000). 

In order to get administrator privileges you will need to create a regular account first through the sign up process.

After this is completed access the application console on the virtual machine

```bash
rails c
```

On this console execute the following commands using your own information:

```
admin = Role.create(name: "admin")
admin.users << User.find_by_user_key( "YOUR.EMAIL@ualberta.ca" )
admin.save
```

This account will now have full access to the Administrator panel.

Happy coding!