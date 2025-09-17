
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