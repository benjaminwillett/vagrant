## Welcome to my Vagrant Pages

Vagrant is an open-source software product for building and maintaining portable virtual software development environments, e.g. for VirtualBox, Hyper-V, Docker, VMware, and AWS.

Vagrant builds virtual images from references (boxes) of a type of operating system with applications and dependent software, this can be referenced in a template file that is given to users on a group project to ensure that their development environment is the same as everybody elses as it is using the same virtual image. Sort of like making sure everyone is singing from the same hymn book.It is also great for checking a development that is built on a different environment to which it is being deployed, so you can set up the target environment in a virtual instance to test.

## Vagrant Install
Download the latest Vagrant installer, for macOS there is a point and click .dmg installer.

Expand the .dmg and install the Vagrant.pkg package file. The binary gets installed in the Applications folder with a link to the /usr/bin so it is added to the shell path.

The user data for Vagrant is filed in the directory from which vagrant was used and is stored in an invisible directory named .vagrant.d

You can check the version of vagrant you have by typing:`vagrant -v`

If you want to upgrade to a later version just download the latest installer and drag and drop the uninstall.tool onto an open Terminal window and follow the process, then install the Vagrant.pkg file or simply just install the new version over the top of the older one  e.g 1.9.4 -> 2.0.0

Along with Vagrant you need to use a VM solution to store the image or box, some solutions include VMWare and AWS but Virtual Box is a free and popular solution. This needs to be downloaded and installed on your local machine.

Change directory to where you want to store the Vagrant project and run`vagrant init`
This will place a set up file named Vagrantfile in that top level. After the Vagrantfile has been created the next step is to pull the image or box that will be used.

You can see a bunch of Vagrant boxes on the Vagrant Cloud website that you can download and use.

To add a box using one of the examples:
`vagrant box add chef/centos-6.5`

This will download the Centos 6.5 box to the current directory, if asked select the relevant VM solution such as Virtual Box. The original box is always left intact and can be used across multiple projects, the Vagrant image built is based from the box.

After the box has downloaded we need to tell Vagrant which box to use in the current project, this is done in the Vagrantfile created earlier, using you preferred Terminal editor:`nano Vagrantfile`

Find and change```# Every Vagrant virtual environment requires a box to build off of.  config.vm.box = "base"```

To the box name, so in this example:```# Every Vagrant virtual environment requires a box to build off of.  config.vm.box = "chef/centos-6.5"```

The next step is to boot the box up which is done with the most popular vagrant command:```vagrant up```
Which will run through a series of start up steps

To actually conn`vagrant@precise64:~$ touch /vagrant/foo`

`vagrant@precise64:~$ exit`

`$ ls`

`foo Vagrantfile`

Whoa! "foo" is now on your host machine. As you can see, Vagrant kept the folders in sync.
With synced folders, you can continue to use your own editor on your host machine and have the files sync into the guest machine.

## Install Apache Web Server

### Provisioning 
Alright, so we have a virtual machine running a basic copy of Ubuntu and we can edit files from our machine and have them synced into the virtual machine. Let us now serve those files using a webserver.

We could just SSH in and install a webserver and be on our way, but then every person who used Vagrant would have to do the same thing. Instead, Vagrant has built-in support for automated provisioning. Using this feature, Vagrant will automatically install software when you `vagrant up` so that the guest machine can be repeatably created and ready-to-use.

#### Installing Apache 
We will just setup Apache for our basic project, and we will do so using a shell script. Create the following shell script and save it as `bootstrap.sh` in the same directory as your Vagrantfile:

```
#!/usr/bin/env bash
apt-get update
apt-get install -y apache2
  if ! [ -L /var/www ]; then  
  rm -rf /var/www  
  ln -fs /vagrant /var/www
  fi
  ```
  
Next, we configure Vagrant to run this shell script when setting up our machine. We do this by editing the Vagrantfile, which should now look like this:

Next, we configure Vagrant to run this shell script when setting up our machine. We do this by editing the Vagrantfile, which should now look like this:

```
Vagrant.configure("2") do |config|  
config.vm.box = "hashicorp/precise64"  
config.vm.provision :shell, path: "bootstrap.sh"
end
```

The "provision" line is new, and tells Vagrant to use the shell provisioner to setup the machine, with the `bootstrap.sh` file. The file path is relative to the location of the project root (where the Vagrantfile is).

#### Provision! 
After everything is configured, just run `vagrant up` to create your machine and Vagrant will automatically provision it. You should see the output from the shell script appear in your terminal. If the guest machine is already running from a previous step, run `vagrant reload --provision`, which will quickly restart your virtual machine, skipping the initial import step. The provision flag on the reload command instructs Vagrant to run the provisioners, since usually Vagrant will only do this on the first `vagrant up`.
After Vagrant completes running, the web server will be up and running. You cannot see the website from your own browser (yet), but you can verify that the provisioning works by loading a file from SSH within the machine:

```
$ vagrant ssh
...
vagrant@precise64:~$ wget -qO- 127.0.0.1
```

This works because in the shell script above we installed Apache and setup the default DocumentRoot of Apache to point to our `/vagrant` directory, which is the default synced folder setup by Vagrant.
ect to the box use ssh and you will be in the home of the VM box
`vagrant ssh`

Whilst in your Vagrant box your starting directory point is `/home/vagrant` – it is the vagrant directory at the root `/vagrant` which shares the same content as your initial project directory. This is a key thing, it essentially mirrors the 2 directories so the content in your project folder will also be in the `/vagrant` directory on the Vagrant Box.

When you are done fiddling around with the machine, run `vagrant destroy` back on your host machine, and Vagrant will terminate the use of any resources by the virtual machine.The vagrant destroy command does not actually remove the downloaded box file. To completely remove the box file, you can use the `vagrant box remove` command.

`vagrant@precise64:~$ ls /vagrantVagrantfile`

Believe it or not, that Vagrantfile you see inside the virtual machine is actually the same Vagrantfile that is on your actual host machine. Go ahead and touch a file to prove it to yourself:

### Networking 
At this point we have a web server up and running with the ability to modify files from our host and have them automatically synced to the guest. However, accessing the web pages simply from the terminal from inside the machine is not very satisfying. In this step, we will use Vagrant's networking features to give us additional options for accessing the machine from our host machine.

#### Port Forwarding 
One option is to use port forwarding. Port forwarding allows you to specify ports on the guest machine to share via a port on the host machine. This allows you to access a port on your own machine, but actually have all the network traffic forwarded to a specific port on the guest machine.

Let us setup a forwarded port so we can access Apache in our guest. Doing so is a simple edit to the Vagrantfile, which now looks like this:

```
Vagrant.configure("2") do |config|  
config.vm.box = "hashicorp/precise64"  
config.vm.provision :shell, path: "bootstrap.sh"  
config.vm.network :forwarded_port, guest: 80, host: 4567
end
```

Run a vagrant `reload` or `vagrant up` (depending on if the machine is already running) so that these changes can take effect.
Once the machine is running again, load `http://127.0.0.1:4567` in your browser. You should see a web page that is being served from the virtual machine that was automatically setup by Vagrant.
