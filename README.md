# About

This repository shows examples for [my post on Qiita](), a Japanese technical knowledge sharing platform.
In the post, I described benefits to have your own proxy server outside your company especially when your company has a strictly configured proxy/firewall.

The followings are some benefits I came up with.

* You can access any sites even if the sites are blocked by a proxy of your company.
* You can ssh to any machines outside your company even if a firewall of your company only allows outgoing traffic to 80 and 443 port.
* You can securely use the Internet at a cafe or a hotel even if your computer accesses the Internet through an untrustworthy wifi network.
* You can access sites that are not accessible from your country.(if you use VPS in other country to build your server.)

I put a Vagrantfile with Ansible provisioner in this repository for those who do not want to take much time to build such a server.

# Basis

A proxy/firewall in a company usually blocks access to some sites which are not related to the business.
It also does not allow outgoing traffic to any port except 80 and 443 port.

![proxy01](https://raw.github.com/wiki/daikikohara/vagrant-do-privoxy/images/proxy01.png)
![proxy02](https://raw.github.com/wiki/daikikohara/vagrant-do-privoxy/images/proxy02.png)

However, if you have a server outside your company listening on 443 port, you can ssh into the server.

The configuration is very easy.
You just need to configure `/etc/ssh/sshd_config` to listen on 443 port.
Then, use [corkscrew](http://www.agroman.net/corkscrew/) when you ssh to the server from your company. Below is an example of `~/.ssh/config`.

```
Host external-server
  Port      443
  HostName  XXX.XXX.XXX.XXX
  ProxyCommand /usr/local/bin/corkscrew <IP for company's proxy> <port for company's proxy> %h %p
```

Now, you can ssh to any server via your server configured above.

![proxy03](https://raw.github.com/wiki/daikikohara/vagrant-do-privoxy/images/proxy03.png)

Next, you would like to access any sites on your web browser.
Steps to do this is a bit more complex but still easy.

First, install a proxy software on your external server.
I used [privoxy](http://www.privoxy.org/), but any proxy software should be fine.

![proxy04](https://raw.github.com/wiki/daikikohara/vagrant-do-privoxy/images/proxy04.png)

Then establish an ssh tunnel to the privoxy port.(defaults to 8118)

```
ssh -L 0.0.0.0:8888:localhost:8118 <IP of your external server>
```

You also need to add some options like -p port and/or -l user if you don't configure `~/.ssh/config` properly.

![proxy05](https://raw.github.com/wiki/daikikohara/vagrant-do-privoxy/images/proxy05.png)

Finally, set proxy settings of your browser to ssh tunnel.(in this example local:8888)

Here, you'll see any sites on your browser even if the sites are blocked by a proxy of your company.

![proxy06](https://raw.github.com/wiki/daikikohara/vagrant-do-privoxy/images/proxy06.png)

# Usage

Vagrantfile in this repository creates a VM(droplet) on digitalocean and configures sshd and privoxy using Ansible provisioner.
So, if you don't have [Vagrant](https://www.vagrantup.com/), [Ansible](http://www.ansible.com/home) and [vagrant-digitalocean plugin](https://github.com/smdahlen/vagrant-digitalocean/), you need to install them first.

1. Install required packages.
```
sudo apt-get install vagrant ansible
sudo vagrant plugin install vagrant-digitalocean
```
2. Clone this repository
```
git clone https://github.com/daikikohara/vagrant-do-privoxy.git
```
3. Configure some part of files according to your configuration.
4. Run the Vagrantfile
```
sudo vagrant up --provider=digital_ocean
```

If you would like to ssh to the server using `vagrant ssh`, you'll need to add `config.ssh.port = '443'` in your Vagrantfile after step 4.
Also, if you use the same computer inside and outside your office, you'll add `override.ssh.proxy_command = "/usr/local/bin/corkscrew <proxy_ip> <proxy_port> %h %p"` in order to access your external server from your office.

# Disclaimer

I do not suggest you to break the rule of your company.
I do not assume any responsibility for any consequence of using the method described in this repository.

