Virtually Walled
================

Internet Access
---------------

RMB99 per month for unlimited Internet access.

Unstable Internet connection, I can prove it by using "ping" monitoring.

- Unstability applies strictly to outside the wall websites, such as US sites?

- How to monitor network connectivity?

Internet Connectivity or website monitoring tools.

- Internet Connectivity, connect to where? A specific website?

Monitoring Tools
----------------

- ping, traceroute, netstat -l -tcp
speedtest.net

ping -i 9 -q -c 100 host

1. Server in Amazon us-east-1 region.
2. Google.com (specific server)
3. Google.com.hk
4. GitHub.com
5. Apple.com (ping is using a PING protocol not website monitoring tool, which is always port 80)


Inconvenience
-------------

Let's talk about stability instead of accessibility.

Why GitHub is accessible, but not BitBucket. BitBucket is in the shadow of the social coding. Maybe that's the reason, it is much bigger, and it has the resource to deploy accelerator globally. I am so happy that I am away from the US, because this is gives me a perspective on how to design a global accelerated architecture, instead of everything just work in the US.

Unwalled
--------

Inconvenience sucks, slow Internet connection sucks.

1. Network Proxy
2. VPN (OpenVPN, PPTP, L2TP)
3. Poor man's VPN Sshuttle
4. https://github.com/goagent/goagent
5. Custom VPN

- Network proxy (MySSH.cc)
- Private Tunnel (VPN providers are gotten blocked)
- sshuttle (poor man's VPN)
- Roll your own via Amazon, luckily Amazon AWS is not blocked.


Current plan:

1. Use latest crawler instance.
1. Use sshuttle via ap-southeast-1 Amazon AWS EC2 micro instance.
1. Use sshuttle via us-east-1 Amazon AWS EC2 micro instance.
2. Try out in multiple regions.

sshuttle via ap-southeast-1 does not enable me to access nytimes.com and google. poor man's vpn does not work. That's because you need to restart your browser, or clear cache? Another problem is that when you're downloading a large size file such as an image, all other connections will become slow, sounds like a bottleneck. How about the affect of Amazon instance speed? Watching YouTube video is bad.

After shutting down sshuttle, I need to restart my router in order to make it work. Otherwise I can only ping, but not routing.

another VPN recommendation by Sheng-Yan Zhang: http://www.shayunet.net/, but cannot be accessed from China.

MySSH
-----

Using MySSH.cc I can connect multiple devices to it. How do you determine VPN speed? They have a remote in Singapore: phone.28.gs for VPN PPTP access (need username and password).

Ability to allow multiple devices connecting.

Sha Yu
------

https://www.shayunet.net/index.php

Server list:
免费美国01  严禁BT下载  205.164.0.10  支持  PPTP / L2TP 拥挤
免费美国02  严禁BT下载  205.164.0.11  支持  PPTP / L2TP 拥挤
免费美国03  严禁BT下载  205.164.0.12  支持  PPTP / L2TP 拥挤

L2TP密钥为shayu


Ubuntu L2TP VPN
---------------

TODO: Use Zhan Zhenzhen's L2TP account.

When you are starting looking, there are a number of free solutions in Android. Just go to Play store. I just need to test the VPN connections.


Browsers and Tools
------------------

How to use browsers and tools (curl) to connect via proxy?

- Chrome proxy
- Firefox proxy
- Lynx


Speed
-----

Switch to mirrors: Ubuntu repos for apt-get, if you dont' switch, this is the latency. If you do, then this is the latency.


MySSH
-----

1. Just for browser to be proxied, not terminal command? Will all browsers work? What about Lynx? What about Dropbox?

But after using for network proxy, once it fails, my network is screwed. I cannot connect to Internet at all.


Others
------

Border Gateway Protocol, and China to US?
Default router provided sucks, you can't do much configuration

Ubuntu用L2TP很麻烦，它built-in只能用PPTP，但是安全性不够，而且有些网还是会被封。我用的是SSTP，能上的网更多。参见这个：
http://www.strongvpn.com/setup_ubuntu_sstp.shtml

不过这个被封了，你把com换为asia就可以了：
http://www.strongvpn.asia/setup_ubuntu_sstp.shtml

关于PPTP, L2TP, SSTP的安全性，参见这个：
http://technet.microsoft.com/zh-cn/library/cc771298(v=ws.10).aspx
