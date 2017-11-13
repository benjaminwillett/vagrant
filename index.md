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

To actually connect to the box use ssh and you will be in the home of the VM box
`vagrant ssh`

Whilst in your Vagrant box your starting directory point is `/home/vagrant` – it is the vagrant directory at the root `/vagrant` which shares the same content as your initial project directory. This is a key thing, it essentially mirrors the 2 directories so the content in your project folder will also be in the `/vagrant` directory on the Vagrant Box.

When you are done fiddling around with the machine, run `vagrant destroy` back on your host machine, and Vagrant will terminate the use of any resources by the virtual machine.The vagrant destroy command does not actually remove the downloaded box file. To completely remove the box file, you can use the `vagrant box remove` command.

`vagrant@precise64:~$ ls /vagrantVagrantfile`

Believe it or not, that Vagrantfile you see inside the virtual machine is actually the same Vagrantfile that is on your actual host machine. Go ahead and touch a file to prove it to yourself:

`vagrant@precise64:~$ touch /vagrant/foo`

`vagrant@precise64:~$ exit`

`$ ls`

`foo Vagrantfile`

Whoa! "foo" is now on your host machine. As you can see, Vagrant kept the folders in sync.
With synced folders, you can continue to use your own editor on your host machine and have the files sync into the guest machine.

## Install Apache Web Server

Create a bootup script that will upgrade the OS and install Apache when the Vagrant box is booted up, so still in your initial project directory on macOS or in `/vagrant` on the Vagrant Box. You need to create a script and also edit the VagrantFile to include the script, if you use the Vagrant Box to edit the file you’ll need to use vi or nano , but install it first.
