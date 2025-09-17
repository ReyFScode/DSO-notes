
linux --    [command] | tee somefile.txt    - streams output to console and file simultaneously 

:- in docker (fallback vars)


docker-compose logs -f [service]
docker-compose config
docker-compose ps [service list from compose file, quick summary vs docker ps -a ]
docker-compose pull > just pulls images


add lsblk - devices

#sourceVbash : [linux - What is the difference between executing a Bash script vs sourcing it? - Super User](https://superuser.com/questions/176783/what-is-the-difference-between-executing-a-bash-script-vs-sourcing-it)
add eval command (runs something as if it were a command typed in)




**kube**
you must load images on a node by node basis (if using pull_policy: never) to containerds k8s namespace

w crictl:


w nerdctl:
must be gunzipped first
to list: nerdctl images ls --namespace k8s.io
to load: nerdctl load -i sofa-api.tar --namespace=k8s.io


w conteinerd (ctr): 
must be gunzipped first
to list: ctr --namespace k8s.io image ls

to load: ctr -n=k8s.io image import ./core-image-stack.tar


label node
kubectl label node/node_name labelKey=Value

remove: kubectl label node/node_name labelKey-

add -
nodeSelector:
    labelKey: value
to container spec section


kubectl config set-context --current --namespace=sofa

--previous for logs from container that has died and is restarted

kubectl config set-context --current --namespace=sofa - changes context







---
more stuff from any

docker networking  random facts
docs - https://docs.docker.com/engine/tutorials/networkingcontainers/#:~:text=You%20can%20add%20containers%20to%20a%20network%20when,your%20container%20to%20see%20where%20it%20is%20connected%3A
- by default docker assigns the bridge network to containers, we can use an overlay network to achieve multi-host networking

docker windows networking: we see different networs that appear when we use windows - 
we can use these to help with certain problems for instance if we created a virtual switch that operates a hyper-v vm we can mount this switch in docker run with the flag --net="default switch" to allow the container to communicate with the hyper-v instance

docker networking  random facts
docs - https://docs.docker.com/engine/tutorials/networkingcontainers/#:~:text=You%20can%20add%20containers%20to%20a%20network%20when,your%20container%20to%20see%20where%20it%20is%20connected%3A
- by default docker assigns the bridge network to containers
-----------------------------------------------------------------------------mounting stuff

when mounting a file/directroy you can add the :ro , :rw, :Z, or :ro,Z, this define conditions for the volume (read-only, read-write.......
e.g) /a/b/c/file.file:/z/x/y/file.file:ro   > specifies read only
:Z states that the volume can be shared among other containers using -z command > to access with another container use the --volumes-from flag >docker run  --volumes-from testing-z -it nginx sh (this command says that this new container is gonna use a volume called testing-z specified in another container with :z

----------------------------------------------------------------------------- how to create networks / assign them

docker network create name-here   - creates a custom network 
docker run ......  -h hostname .......     -  the -h flag assigns the container a hostname so it can be ID'd on the network
docker run ......  - -network my-net.......     -  the - - net flag assigns a network to a container

-----------------------------------------------------------------------------  networks in docker-compose

to put a network in compose: (already created)
include a section  in the container build section:
                             networks:
                                    - my-net
and on the bottom:

networks:
   my-net:
       external: true     > indicates that the network already exists and doesnt need to be created

random comms--------------------------------------------
docker-compose logs -f   - check docker compose logs while containers spin up
docker container logs ____   -displays docker  container logs
ping -a 10.1.20.81  - pings an ip address and gets hostname



-----------------------------------------------------------------------------mounting stuff
-v "$(pwd)/ansible:/etc/ansible   - use pwd for mount

when mounting a file/directroy you can add the :ro , :rw, :Z, or :ro,Z, this define conditions for the volume (read-only, read-write.......
e.g) /a/b/c/file.file:/z/x/y/file.file:ro   > specifies read only
:Z states that the volume can be shared among other containers using -z command > to access with another container use the --volumes-from flag >docker run  --volumes-from testing-z -it nginx sh (this command says that this new container is gonna use a volume called testing-z specified in another container with :z

----------------------------------------------------------------------------- how to create networks / assign them

docker network create name-here   - creates a custom network 
docker run ......  -h hostname .......     -  the -h flag assigns the container a hostname so it can be ID'd on the network
docker run ......  - -network my-net.......     -  the - - net flag assigns a network to a container
`
-----------------------------------------------------------------------------  networks in docker-compose

to put a network in compose: (already created)
include a section  in the container build section:
                             networks:
                                    - my-net
and on the bottom:

networks:
   my-net:
       external: true     > indicates that the network already exists and doesnt need to be created

random comms--------------------------------------------
docker-compose logs -f   - check docker compose logs while containers spin up
docker container logs ____   -displays docker  container logs
ping -a 10.1.20.81  - pings an ip address and gets hostname

docker-compose alwasys creates a network by default unless network_mode: bridge is set in the docker-compse.yml

linux command to only display active IP uses regex (-E flag) and the -o flag which indicates that-
you just want the matched data not the all the output with the data highlighted:    hostname -i | grep -o -E '^\S*

ADD dockerfile command can un-tar a folder and put in the place you want it )) ADD blah.gz  /blahdir



wsl -l - info

uname -a  -kernel info

wsl1 microsoft linux kernel // wsl2 tiny vm (Service vm)

hostname -I  core  ip addresses on linux




wsl commands -------

wsl --version  -displays version, only works with v 2 and up
wsl -l -v displays wsl instances and their version
wsl --update  - updates wsl
wsl --shutdown -shuts down and updates wsl

if you have private network issues on WSL, steps to resolve below:
1. check wsl2 version and updates are adequate (wsl --version and wsl2 over wsl1)2. write /etc/wsl.conf (systemd = true and generateResolvConf = false)3. restart wsl4. write /etc/resolv.conf (nameservers)5. restart wsl6. try and talk with network resources via DNS


wsl.conf with-- 

[boot]
systemd = true

[network]
generateResolvConf = false


resolv.conf (defines dns servers to use)--
nameserver dns.server.here


--
pip list -v   - shows all installed wheels and their location

find . -ctime -30 -finds files made 30 days ago
-cmin -30 - 30 mins ago

git switch branch_name switches the branch
git rev-parse --abbrev-ref HEAD - checks current branch youre on

windows env vars = 
 $env:computer = $(hostname)  // echo $env:computer //Remove-Item Env:yourVarHere

Windows alias commns =
set-alias -name newAlias -value "test"   // Get-alias (gets all aliases) // Remove-Item Alias:newAlias

net use: Connects a computer to or disconnects a windows computer from a shared resource (often used to add/remove shared drives), or displays information about computer connections. The command also controls persistent net connections. Used without parameters, net use retrieves a list of network connections.


pip freeze - creates a list from a folder of py-packages


readlink -f .exe/filename   - finds path (symlinks and real path) to dictated file


git branch -d name - deletes branch locally



bash notes - - -- - -
read -p "enter your gitlab URL (e.g. http://gitlab.test.local.com): " GLURL - read -p stands for read prompt.....takes user input as var and displays prompt to user







things to add to notes:

you can use $ to pass a commands output to another command (e.g> chmod +777 $(ls -a)

docker pull images from another repository...kubernetes example : https://computingforgeeks.com/manually-pull-container-images-used-by-kubernetes-kubeadm/


date +%b%d_%Y


crontab - l =lists cronjobs
crontab -e =edits cronjobs
cronjob resource = https://kvz.io/schedule-tasks-on-linux-using-crontab.html


Unless you need other commands in the script to run as a non-root user, the simplest option is to remove the sudo commands from inside the script, and run the whole thing as root either by adding it to root's crontab file (sudo crontab -e) or via the system-wide /etc/crontab file specifying root in the user field.

If you do need to run some commands as a non-root user, then you can do the same but drop privileges for those specific commands using sudo -u <otheruser>

which bash = shows bash path

lsof -i:port  - checks services on a specified port

in a bash script if you need to access another users shell and pass commands to it you need to add the -c flag (Example: sudo -u bob -i bash -c 'cd /etc && ls'), bash will run commands in its own shell, you have to pass a command flag to export the commands to the new shell.

adds a secondary IP (alias) (temporary) to an interface -  sudo ip addr add 192.168.0.166/24 broadcast 192.168.0.255 dev ens33

 $ sudo apt - -download-only   install         --only downloads the .deb for a package

apt-get remove <package>     --removes package

systemctl restart gitlab-runsvdir.service         -- fixes gitlab logrotate hang
netstat -tulpn | grep "listen"     > shows ports actively listening





------------------------------ random stuff
deltas = changes in a system, comes from delta encoding which is: a way of storing or transmitting data in the form of 
differences (deltas) between sequential data rather than complete files (e.g. git deltas are the changes to the repo, 
this is how git can update only the changes to a repo)

