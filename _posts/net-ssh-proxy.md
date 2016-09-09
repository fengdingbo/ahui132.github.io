# ssh proxy

## socks proxy
> socks4 不支持 udp 应用, 现在大家都用 socks5 了
建立一个 socks5

	ssh -D [0:]1080 hilo@remote-ip
	export http_proxy=socks5://127.0.0.1:1080 https_proxy=socks5://127.0.0.1:1080
	youtube-dl youtube.com/watch?V=3XjwiV-6_CA

## tcp port forwarding

### ssh over ssh
Tunnelling an ssh connection through an ssh connection:

    -L [bind_address:]port:host:hostport

    # if 2201 is not occupied
    me% ssh user@100.100.100.100 -L 2201:192.168.25.100:22

then

    me% ssh user2@localhost -p 2201


### ssh tunnel open to public
> http://superuser.com/questions/588591/how-to-make-ssh-tunnel-open-to-public

You can also reverse the direction and *create a reverse port forward*.
This can be useful if you want to connect to a machine remotely to allow connections back in.
For instance, I use this sometimes so that I can create a reverse port 22 (SSH) tunnel so that I can reconnect through SSH to a machine that is behind *a firewall* once I have gone away from that network.

	-R [bind_address:]port:host:hostport
    ssh -R 8022:localhost:22 username@my.home.ip.address

This will connect to my home machine and start listening on port 8022 there. Once I get home, I can then connect back to the machine I created the connection from using the following command:

    ssh -p 8022 username@localhost

Remember to use the right username for the machine that you started the tunnel from. It can get confusing. You also have to keep in mind that since you are connecting to the host called localhost, but its really a port going to a different SSH server, you may wind up with a different host key for localhost the next time you connect to localhost. In that case you would need to edit your .ssh/known_hosts file to remove the localhost line. You really should know more about SSH before doing this blindly.

As a final exercise, you can keep your reverse port forward open all the time by starting the connection with this loop:

    while true ; do ssh -R 8022:localhost:22 suso@my.home.ip.address ; sleep 60 ; done

When bind_address is omitted (as in your example), the port is bound on the loopback interface only. In order to make it bind to all interfaces, use

	-N Do not execute a remote command.
	#  binds to all interfaces individually
	ssh -R '\*:8080:localhost:80' -N root@example.com

	# a general IPv4-only bind
	ssh -R 0.0.0.0:8080:localhost:80 -N root@example.com

	# the port is accessible via IPv6 natively
	# and via IPv4 through IPv4-mapped IPv6 addresses (doesn't work on Windows, OpenBSD).
	ssh -R "[::]:8080:localhost:80" -N root@example.com

## ssh over socks
~/.ssh/config

	ProxyCommand /usr/bin/nc -X 5 -x 127.0.0.1:7777 %h %p
	ProxyCommand /usr/bin/nc -x 127.0.0.1:7777 %h %p

You are using 'connect' for HTTPS as your proxy version, this is from man nc:

	-X proxy_version Requests that nc should use the specified protocol when talking to the proxy server. Supported protocols are ''4'' (SOCKS v.4), ''5'' (SOCKS v.5) and 'connect' (HTTPS proxy). If the protocol is not specified, SOCKS version 5 is used.

Manual:

	ssh -o ProxyCommand='nc -X 5 -x 127.0.0.1:1080 %h %p' user@host 'curl http://1212.ip138.com/ic.asp'


# SSH through HTTP proxy

> http://www.zeitoun.net/articles/ssh-through-http-proxy/start

This article explains how to connect to a ssh server located on the internet from a local network protected by a firewall through a HTTPS proxy.

Requirement are :

1. Your firewall has to allow HTTPS connections through a proxy
2. You need to have root access to the server where ssh is listening

## Configure the ssh server
The ssh daemon need to listen on 443 port. To accomplish this, just edit this file (on debian system) `/etc/ssh/sshd_config` and add this line :

  Port 443

Then restart the daemon :

  sudo /etc/init.d/ssh restart

## Configure the client
I suppose you are on a Linux system (debian for example). First you have to compile the connect binary which will help your ssh client to use proxies (HTTPS in our case). Then you have to configure your ssh client to tell him to use HTTPS proxy when he tries to connect to your ssh server.

### Install the connect software :
On debian system, just install the connect-proxy package :

  sudo apt-get install connect-proxy

On other Linux systems, you have to compile it :

  cd /tmp/
  wget http://www.meadowy.org/~gotoh/ssh/connect.c
  gcc connect.c -o connect
  sudo cp connect /usr/local/bin/ ; chmod +x /usr/local/bin/connect

Configure your ssh client. Open or create your ~/.ssh/config file and add these lines :

Outside of the firewall, with HTTPS proxy

  Host my-ssh-server-host.net
    ProxyCommand connect -H proxy.free.fr:3128 %h 443

Inside the firewall (do not use proxy)

  Host *
     ProxyCommand connect %h %p

Then pray and test the connection :

  ssh my-ssh-server-host.net

SSH to another server through the tunnel
For example to connect to in ssh github.com :

  Host github.com
    ProxyCommand=ssh my-ssh-server-host.net "/bin/nc -w1 %h %p"
