# ansible-demo
demo for ansible using containers

***This is an old version of Ansible(2.7.2) in the controler, so it is missing updates and community plugs. Run ansbile locally to get all of the best features.***

[based on this how to article](https://www.dbi-services.com/blog/using-docker-containers-for-ansible-testing/)

# to test we need docker desktop and this setup

```bash
# first pull the container image
docker pull jcpowermac/alpine-ansible-ssh

# now start it 3 different times. One controller and 2 targets	
% docker run --name=controller --platform linux/amd64 -d --rm -v ${PWD}:/tmp/ansible_demo jcpowermac/alpine-ansible-ssh
% docker run --name=target1 --platform linux/amd64 -d --rm jcpowermac/alpine-ansible-ssh
% docker run --name=target2 --platform linux/amd64 -d --rm jcpowermac/alpine-ansible-ssh
```

You should see 3 containers running with the docker ps command.

docker exec into the controller container

```bash
docker exec -it controller sh
cd /tmp/ansible_demo
```

## Errors?

May be tied to the inventory file. Check that in your current directory and update it to the running containers. It is mounted, so changes locally will be seen in the container. You can also ssh back and forth from the controler container to the two targets.

```bash
ssh 172.17*.*
```


# test out ansible

We are in the controller container, so lets ping the other containers. You should get some pings and pongs.



```bash
ansible target* -m ping -i inventory.txt
```

Lets make a test file

- it goes to each host/ target
- creates the file
- ensures it has the content of "test"
- path is /tmp/test.txt



```bash
ansible-playbook -i inventory.txt make-file.yml

```

ssh to the other containers in another terminal. make a change

rerun, fix config frift

change owner on target

```bash
ansible-playbook -i inventory.txt change-file-target1.yml

```

Rerun ansible-playbook -i inventory.txt make-file.yml after uncomment
- evolve playbooks to meet needs