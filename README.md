# ssher
Let's SSH!

## Question 1

Normally we can just jump through bastion host with single SSH command, eg for **server1**
```
 ssh ubuntu@192.168.0.1 -o "ProxyCommand=ssh -W %h:%p -x 212.186.105.48"
```

We could also edit our local SSH config, to make it manageable, eg.

```
Host bastion*
    User ubuntu
    IdentityFile ~/.ssh/id_rsa
    ForwardAgent yes

Host bastion-212-186-105-45
    Hostname 212.186.105.45

Host bastion-212-186-105-48
    Hostname 212.186.105.48

Host server1
    Hostname 192.168.0.1
    ProxyCommand  ssh -W %h:%p bastion-212-186-105-48

Host server3
    Hostname 192.168.0.1
    ProxyCommand  ssh -W %h:%p bastion-212-186-105-48

```

and then just running 
```
ssh server1
```
should do the job

## Question 2

Unfortunately there might be thousands of servers without assumptions like common CIDRs etc, 
those inventory also might be dynamic (in cloud) so keeping ssh config file up to date might be cumbersome.
But we have yaml file with server IP/bastion mapping, so we can utilize that. 
ssher is actually bash alias combining jq/yq agility with SSH command

Add to your existing file .bash_aliases (or create new of if does not exists) content of file .bash_aliases, start new shell and just launch 

```
ssher server1
```
This can me combined with SSH static config to have *ubuntu* user configured globally etc.

### Considerations
With static inventory it could be acceptable to have shared SSH config within team.

For dynamic inventories (let's say its AWS EC2 fleet) eg. ansible allows to run command(s) based on Cloud API's 
(```ansible -i ~/bin/lib/ec2.py -m command -a 'uptime' tag_Name_server1```)
but I guess this kind of cases was out of scope here.