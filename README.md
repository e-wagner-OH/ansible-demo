# ansible-demo
demo for ansible using containers

***This is an old version of Ansible(2.7.2) in the controler, so it is missing updates and community plugs. Run ansbile locally to get all of the best features.***

[based on this how to article](https://www.dbi-services.com/blog/using-docker-containers-for-ansible-testing/)

# setup

*** need docker desktop install ***
*** may need to change paths if on windows ***

We pull the image, start 3 containers with it, and put the current directory in the controller container to use playbooks and inventory file with ansible.

```bash
# first pull the container image
docker pull jcpowermac/alpine-ansible-ssh

# now start it 3 different times. One controller and 2 targets
% docker run --name=controller --platform linux/amd64 -d --rm -v ${PWD}:/tmp/ansible_demo jcpowermac/alpine-ansible-ssh
% docker run --name=target1 --platform linux/amd64 -d --rm jcpowermac/alpine-ansible-ssh
% docker run --name=target2 --platform linux/amd64 -d --rm jcpowermac/alpine-ansible-ssh
```

You should see 3 containers running with the docker ps command.

```bash
docker ps
```


```bash
docker exec -it controller sh
cd /tmp/ansible_demo
```

## Errors?

May be tied to the inventory file. Check that in your current directory and update it to the running containers. It is mounted, so changes locally will be seen in the container. 

### Check the container ipv4 addys

They are running in the local bridge network

```bash
#returns all the names and ipv4
docker network inspect bridge | grep 'Name\|IPv4'  

```

You can also ssh back and forth from the controler container to the two targets after confirming.

```bash
ssh 172.17.0.3
```



# test out ansible

We are in the controller container, so lets ping the other containers. You should get some pings and pongs.


```bash
ansible target* -m ping -i inventory.txt
```

## make some changes

Lets make a test file

- it goes to each host/ target
- creates the file
- ensures it has the content of "test"
- path is /tmp/test.txt


```bash
ansible-playbook -i inventory.txt make-file.yml

```

ssh to the other containers in another terminal. make a change

rerun, fix config drift

change owner on target

```bash
ansible-playbook -i inventory.txt change-file-target1.yml

```

Rerun ansible-playbook -i inventory.txt make-file.yml after uncomment

- evolving playbooks to meet changes needed in infra