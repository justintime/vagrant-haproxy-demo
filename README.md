vagrant-haproxy-demo
====================

Demo of HAProxy using Vagrant

This is the toolset I used to present on load balancers at University of Nebraska at Kearney on 2/19/14.

# Prerequisites
1.  Install [Vagrant](http://www.vagrantup.com/downloads.html)
2.  Install [Virtualbox](https://www.virtualbox.org/wiki/Downloads)
3.  Either clone this repo with ``` git clone https://github.com/justintime/vagrant-haproxy-demo.git ``` or just download the [current zip file](https://github.com/justintime/vagrant-haproxy-demo/archive/master.zip) and extract it in an empty directory.

# What does the Vagrantfile do?
* It sets up a 3 VM mini-network inside Virtualbox.  The three hosts are haproxy (172.28.33.1), web1 (172.28.33.11), and web2 (172.28.33.12)
* It sets up the following port forwards between your host's external interface and the internal VM's:

| Host port | Guest machine | Guest port | Notes
------------|---------------|------------|---
| 8080 | haproxy | 8080 | HAProxy Admin Interface
| 8081 | haproxy | 80 | Load Balanced Apache
* It installs Apache on the two web servers, and configures it with a index page that identifies which host you're viewing the page on.
* It installs HAProxy on the haproxy host, and drops a configuration file in place with the two webservers pre-configured.  It doesn't require HAProxy to be the default gateway because it NAT's the source IP as well as the destination IP.


# Getting started
1.  Open 3 terminal windows -- one for each host.  Change to the directory containing the Vagrantfile from step 3 above.
2.  In terminal #1, run ``` vagrant up haproxy && vagrant ssh haproxy ```
3.  In terminal #2, run ``` vagrant up web1 && vagrant ssh web1 ```
4.  In terminal #3, run ``` vagrant up web2 && vagrant ssh web2 ```
5.  Open up http://localhost:8080/haproxy?stats in your host's browser.  This is the HAProxy admin interface.
6.  Open up http://localhost:8083/ in your host's browser.  This is the load balanced interface to the two web servers.
5.  To see the Apache access logs on web1 and web2, run ``` sudo tail -f /var/log/apache2/access.log ```  If you'd like to filter out the "pings" from the load balancer, run ``` sudo tail -f /var/log/apache2/access.log | grep -v OPTIONS ```
6.  To stop Apache on one of the webservers to simulate an outage, run ``` sudo service apache2 stop ```  To start it again, run ``` sudo service apache2 start ```
7.  To make changes to haproxy, edit the config file with ``` sudo nano /etc/haproxy/haproxy.cfg ```  When you want to apply the changes, run ``` sudo service haproxy reload ```  If you break things and want to reset back, just run ``` sudo cp /etc/haproxy/haproxy.cfg.orig /etc/haproxy/haproxy.cfg && sudo service haproxy reload ```
8.  When you're all done, type ``` exit `` at the shell to get back to your local terminal.
9.  To shut down the VM's, run ``` vagrant halt web1 web2 haproxy ```
10.  To remove the VM's from your hard drive, run ``` vagrant destroy web1 web2 haproxy ```
11.  If you wish to remove the cached image file from which these machines were created, run ``` vagrant box remove precise32 ```




http://localhost:8080/haproxy?stats

# Reference material
* [Vagrant](http://vagrantup.com)
* [VirtualBox](http://www.virtualbox.org)
* [HAProxy](http://haproxy.1wt.eu/)
* [How to run HAProxy with TProxy for half-NAT  setups](http://blog.loadbalancer.org/configure-haproxy-with-tproxy-kernel-for-full-transparent-proxy/)

