# ASSESSMENT WEEK 4

In this week we will configure SSH to accept key-based authentication, and make changes on the firewall to allow ssh from particular ip address. 

## Configuration of SSH with key-based authentication.


### Step 1 : 
we need to generate key from our workstation to do this we will type the following command: 

```bash 

ssh-keygen -t ed25519 -C "ram@10.0.2.3"

# this will generate ssh key based on ed25519 and add a comment.
```

after generating the key we will need to copy it into our server ; 

``` bash 



#copy key into our server 

ssh-copy-id ram@10.0.2.3 
```



proof : 

![](/images/ssh-key-gen.png)



After these steps we can login without using password. 

![](/images/login_proof.png)

we can modify the sshd_config to acccept only key to truly verify key based authentication. 

## Firewall configuration Documentation

``` bash 

# enable the firewall
sudo ufw enable

# allow ssh from one ip only.

sudo ufw allow from 10.0.2.3 to any port 22 proto tcp

#view the firewall status

sudo ufw status numbered


```


## Configuration File comparison ( before vs after)

### sshd_config file before: 

![](/images/sshd(1).png)


![](/images/sshd(2).png)


### Firewall before. 

![](/images/firewall(before).png)



### sshd_config after 

![](/images/sshd_after(1).png)


![](/images/sshd_after(2).png)


### Firewall after

![](/images/firewall(after).png)



## User and privilege Management

``` bash 

## creating a new user

sudo adduser ramadmin

## giving user sudo privileges

sudo usermod -aG sudo ramaadmin

## loggin in via ssh usign the newly created admin 

sudo ramadmin@10.0.2.3


```


proof of login: 

![](/images/ramadmin.png)





