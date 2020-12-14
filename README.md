# Digital Camera Photo Geotag : DevOps

# Manual Deployment

## Waypoint 1: Generate SSH Keys

You are going to deploy and run your RESTful API server to a [Digital Ocean droplet](https://www.digitalocean.com/) that Intek Curriculum team has prepared for you.

Intek Curriculum team has created a Unix account for your squad on the reserved droplet. You won't have an individual Unix account on this machine, but a Unix account that is shared with all the members of your squad.

Instead of connecting with a password to this droplet, you will use a [SSH public and private keys](https://www.youtube.com/watch?v=5Fcf-8LPvws). For example:

```bash
$ ssh avengers@intek.f4.mission
Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 4.15.0-66-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue May  5 02:35:45 UTC 2020

  System load:  0.08               Processes:           99
  Usage of /:   21.6% of 24.06GB   Users logged in:     0
  Memory usage: 24%                IP address for eth0: 68.183.182.223
  Swap usage:   0%

 * Canonical Livepatch is available for installation.
   - Reduce system reboots and improve kernel security. Activate at:
     https://ubuntu.com/livepatch

10 packages can be updated.
0 updates are security updates.


*** System restart required ***
Last login: Mon May  4 09:08:37 2020 from 103.7.39.121
avengers@intek:~$
```

**Each member** of you squad needs to [generate his own individual SSH keys](https://www.digitalocean.com/community/tutorials/ssh-essentials-working-with-ssh-servers-clients-and-keys) and to provide his **public key** to Intek Curriculum team. To [generate your SSH key](https://www.youtube.com/watch?v=du-nWMCRkqE), simply use the following command line instruction:

```batch
$ ssh-keygen -t rsa -C "{{your-student-username}}@f4.intek.edu.vn"
```

_Note: **NEVER EVER** provide your **private key** to anyone, even to Intek Curriculum team._

We strongly suggest you to edit your [SSH file `config`](https://linuxize.com/post/using-the-ssh-config-file/) that needs to be placed into your `~/.ssh` folder of your personal computer:

```text
HOST intek.f4.mission
  HostName 68.183.182.223
  User avengers
  IdentityFile ~/.ssh/id_rsa
```

_Note: Please contact Intek Curriculum team to get the correct IP address of the droplet machine. This IP address might have changed from a scholar year to another, and it might not be `68.183.182.223` anymore._

Intek Curriculum team is going to install your SSH **public** key to the Digital Ocean droplet, and once done, you will be able to connect to this droplet where you will start to deploy your RESTful API server application.

## Waypoint 2: Experimenting Manual Deployment

In this mission, you will deploy a version of your RESTful API server application to your droplet environment. First of all, you need to create a folder `digicam` in the root folder of your droplet account.

```bash
avengers@intek:~$ mkdir digicam
avengers@intek:~$ cd digicam
avengers@intek:~/digicam$
```

You are going to deploy your RESTful API server application to this folder `digicam`. But which exact version of your server application are you going to deploy? You MUST NOT deploy on a [staging or production environment](https://en.wikipedia.org/wiki/Deployment_environment) server a development branch that would evolve over the time: you won't be able to know which exact version of your code you have deployed to your droplet. You need to [tag a specific release](https://www.youtube.com/watch?v=govmXpDGLpo) that you will deploy on your droplet. This is the last part of the [worflow strategies](https://www.youtube.com/watch?v=aJnFGMclhU8).

Tags are generally named `vX.Y.Z`, with `X.Y.Z` following [Semantic Versioning 2.0.0](https://semver.org/spec/v2.0.0.html). The prefix `v` stands for _version_. This allows for branches `X.Y.Z` to co-exist with those tags.

Note that the tags for Git itself have recently been "adapted" for a surprising reason: see "Code version change “rules”".

[Tag a release](https://git-scm.com/book/en/v2/Git-Basics-Tagging), for instance `v1.0.0`, of your RESTful API server application. Create a folder `v1.0.0` under your `digivam` on your droplet. Deploy your release `v1.0.0` into the directory `~/digicam/v1.0.0` of your droplet using the [command line `scp`](https://www.youtube.com/watch?v=rm6pewTcSro) to [securely transfer file(s)](https://linuxize.com/post/how-to-use-scp-command-to-securely-transfer-files/) from/to a server.

For example:

```tree
.
└── digicam
    └── v1.0.0
        ├── CHANGELOG.md
        ├── INSTALL.md
        ├── Pipfile
        ├── README.md
        ├── conf
        │   └── nginx.conf
        ├── fabfile.py
        ├── intek
        │   ├── __init__.py
        │   └── digicam
        │       ├── __init__.py
        │       └── service
        │           ├── __init__.py
        │           └── route
        │               ├── __init__.py
        │               ├── route_service.py
        │               └── route_service_http_handler.py
        └── settings.py
```

_Note: This structure might be totally different from your project. It's totally okay!._

_Note: If your RESTful API server application depends on other software applications not already installed on your droplet, please contact Intek Curriculum team._

## Waypoint 3: Running your RESTful API Server Application (1)

At this point, you should be able to run your RESTful API server application on your droplet.

For example:

```bash
avengers@intek:~$ cd digicam/v1.0.0
avengers@intek:~/digicam/v1.0.0$ pipenv shell
(v1.0.0) devops@intek:~/digicam/v1.0.0$ pipenv install
(v1.0.0) devops@intek:~/digicam/v1.0.0$ fab start
2020-05-05 05:23:42,411 [INFO] Boot RESTful API server instance (80)
```

You need to make sure that your RESTful API server application is listening on a physical interface [all IP addresses](https://flask.palletsprojects.com/en/1.1.x/quickstart/#a-minimal-application) on the droplet machine, and not on [`127.0.0.1`](https://www.youtube.com/watch?v=my2vBYZ1RFw) only like you may have configured by default, when you were developing and testing your server application on your personal computer.

The IP `127.0.0.1`, also known as **localhost**, is bound to the [**loopback interface**](https://www.youtube.com/watch?v=my2vBYZ1RFw), that is a _logical interface_ only available on the local machine. No client applications, running on another machine than your droplet, would be able to connect to the loopback interface of your droplet machine.

Also, you may face an error when your try to run your RESTful API server application on the droplet:

```bash
OSError: [Errno 98] Address already in use
```

The reason is that the [TCP port number](<https://en.wikipedia.org/wiki/Port_(computer_networking)>) that your application listens at (probably `80`, by default) is already used by another server application running on this droplet.

If you use an apartment block analogy, the IP address corresponds to the street address. All of the apartments share the same street address. However each apartment also has an apartment number which corresponds to the port number.

Port numbers are divided into ranges as follows:

1. From `0` to `1023`: Well known ports. These are allocated to server services by the [Internet Assigned Numbers Authority (IANA)](https://en.wikipedia.org/wiki/Internet_Assigned_Numbers_Authority). For example, Web servers normally use port 80 and SMTP servers use port 25.

2. From `1024` to `49151`: Registered Port. These can be registered for services with the IANA and should be treated as semi-reserved. User written programs should not use these ports.

3. From `49152` to `65535`: These are used by client programs and you are free to use these in client programs. When a Web browser connects to a web server the browser will allocate itself a port in this range. Also known as ephemeral ports.

You need to choose another [TCP port number](https://www.youtube.com/watch?v=7HwtRrnPOVk) (avoiding [officially registered port numbers](https://en.wikipedia.org/wiki/List_of_TCP_and_UDP_port_numbers)) that is not used by any other server applications running on your droplet. Configure then your RESTful API server application to listen at that TCP port number.

For example:

```bash
(v1.0.0) devops@intek:~/digicam/v1.0.0$ fab start --ports=52209
2020-05-05 07:57:00,583 [INFO] Boot RESTful API server instance (52209)
```

\_Note: Intek Curriculum team will inform you about the port number you can use on the droplet.\_\_

Update the settings of your mobile and your command-line interface applications so that these client applications connect to your RESTful API server application on the TCP port number your server application is listening at.

You can try connecting to your RESTful API server application from your personal computer by sending an HTTP request using `curl`. For example:

```bash
$ curl -XGET http://68.183.182.223:52209/route/ajd980f
```

Your RESTful API server application processes this HTTP request, and more likely returns an [HTTP 404 status code](https://en.wikipedia.org/wiki/HTTP_404) if the specified route has not been found. For example:

```text
2020-05-05 07:57:00,583 [INFO] Boot RESTful API server instance (52209)
2020-05-05 08:30:26,684 [ERROR] 404 GET /route/ajd980f (68.183.182.223) 5.26ms
```

## Waypoint 4: Running your RESTful API Server Application (2)

You might wander how your RESTful API server application can still run once you disconnect from your SSH session to the droplet.

One of the solutions is to used a **terminal multiplexer**, such as [`screen`](https://en.wikipedia.org/wiki/GNU_Screen) or [`tmux`](https://en.wikipedia.org/wiki/Tmux), a kind of virtual background terminal window, that allows [to detach and reattach sessions from a terminal](https://www.youtube.com/watch?v=3txYaF_IVZQ).

For example:

```bash
# Connect from your personal computer to the droplet
$ ssh avengers@intek.f4.mission

# Run screen providing a session name
avengers@intek:~$ screen -S api.digicam.intek.edu.vn

# Run your RESTful API server application under screen
avengers@intek:~$ cd digicam/v1.0.0
avengers@intek:~/digicam/v1.0.0$ pipenv shell
(v1.0.0) devops@intek:~/digicam/v1.0.0$ fab start --ports=52209
2020-05-05 10:09:36,690 [INFO] Boot RESTful API server instance (52209)
```

Then press on `Ctrl-A` followed with `Ctrl-D` to detach your session from the terminal:

```text
[detached from 9910.api.digicam.intek.edu.vn]
```

You can now disconnect from the droplet. Your RESTful API server application is still running in your `screen` session. You can reconnect later to your droplet and reattach your `screen` session to your terminal.

For example:

```bash
# Connect from your personal computer to the droplet
$ ssh avengers@intek.f4.mission

# List all the existing screen sessions
avengers@intek:~$ screen -list
There is a screen on:
	9910.api.digicam.intek.edu.vn	(05/05/20 10:09:20)	(Detached)
1 Socket in /run/screen/S-avengers.

# Attach your terminal to an existing screen session
avengers@intek:~$ screen -dr 9910.api.digicam.intek.edu.vn
```

Create a new [`screen` session](https://www.rackaid.com/blog/linux-screen-tutorial-and-how-to/) in which you will run your RESTful API application server, and then detach from your screen session, and disconnect from the droplet. Your mobile and your command-line interface applications should still be able to communicated with your RESTful API application server.

_Note: You can also use [`tmux`](https://linuxhint.com/tmux_vs_screen/) which is [similar to `screen`](https://tmuxcheatsheet.com)._

## Waypoint 5: Using Domain Name & Web Proxy

### Domain Name

The IP address of the Digital Ocean droplet reserved for our Intekers was initially `68.183.182.223`. But this IP address may have changed since. One of the reason is that Intek Curriculum team may have had to change from the initial droplet machine to another machine.

Therefore, it's not a good idea to configure client applications to connect to a remote machine, where an application server is running on, by providing the IP address of this machine. The normal way to reference the remote machine of an application server is a custom [domain name](https://en.wikipedia.org/wiki/Domain_name), that is a unique branded name that identifies an application server (such as a website, an API server, etc.). This domain name is resolved to an IP address using a [Domain Name System (DNS)](https://www.youtube.com/watch?v=mpQZVYPuDGU).

Intek Curriculum team has declared a (sub)domain name for your squad. For example, `api.digicam.avengers.intek.edu.vn`. This subdomain name is resolved to the IP address of your droplet machine:

```bash
$ ping -c 1 api.digicam.avengers.intek.edu.vn
PING api.digicam.avengers.intek.edu.vn (68.183.182.223): 56 data bytes
64 bytes from 68.183.182.223: icmp_seq=0 ttl=56 time=40.365 ms

--- api.digicam.avengers.intek.edu.vn ping statistics ---
1 packets transmitted, 1 packets received, 0.0% packet loss
round-trip min/avg/max/stddev = 40.365/40.365/40.365/0.000 ms
```

Configure your mobile and command-line applications to connect to your RESTful API application server using the subdomain name that Intek Curriculum team has declared for your squad, and not the IP address of the droplet.

### Web Proxy

It's quite unusual for a RESTful API application server to require client application to connect to a port number other than `80` (standard HTTP port) or `443` (standard HTTPS port).

Any developers in the world would expect to call your RESTful API application server by sending an HTTP request such as:

```bash
$ curl -XGET http://api.digicam.avengers.intek.edu.vn/route/2zcbSKJ
```

while the current configuration of your RESTful API application server requires to specify a port number such as:

```bash
$ curl -XGET http://api.digicam.avengers.intek.edu.vn:52209/route/2zcbSKJ
```

How can we solve this issue? We can use a [reverse proxy](https://en.wikipedia.org/wiki/Reverse_proxy) such as [`nginx`](https://en.wikipedia.org/wiki/Nginx). This web proxy will handle all the communications from client applications and it will forward these communications to our RESTful API application server, or to the RESTful API application servers of other squads.

`nginx` listens on the network interface of our droplet at the port `80`. Your RESTful API application server still listens but on the loopback interface (**localhost**) this time, at its own port number (e.g., `52209`). But how does `nginx` know to which of the RESTful API application servers running on the droplet `nginx` needs to forward a particular HTTP request? `nginx` [decides which server application should process the request](http://nginx.org/en/docs/http/request_processing.html) based on the server name, that is the domain name identified in the HTTP request (for instance, `api.digicam.avengers.intek.edu.vn`).

So now the question is how `nginx` knows which server name goes with which server applications? You need to write a [configuration file to inform `nginx`](https://www.youtube.com/watch?v=4xGQS8Pv4io) about the (sub)domain name your server application is responsible for processing request to, and which port number your server application is listening at.

Create a file `nginx.conf` in the folder `conf` that needs to be located at the root folder of your RESTful API server application.

```bash
avengers@intek:~$ cd digicam/1.0.1
avengers@intek:~/digicam/1.0.1$ ls -la conf/
total 12
drwxr-xr-x 2 avengers avengers 4096 May  5 13:36 ./
drwxrwxr-x 4 avengers avengers 4096 May  5 08:16 ../
-rw-r--r-- 1 avengers avengers 1338 May  5 08:29 nginx.conf
```

_Note: Intek Curriculum team will reference your configuration file so `nginx` loads it._

## Waypoint 6: Deploying Releases

Each time you implement new features or fix some bugs, you need to release and to deploy a new version of your server application on your droplet.

You could also face a disastrous situation where a last version has a major issue that you just discovered after having deployed this version on your droplet. You need to rapidly rollback to the previous version of your server application on your droplet.

One deployment strategy is to create a folder for each release deployed to the droplet and to use a [symbolic link](https://en.wikipedia.org/wiki/Symbolic_link) – let's name it `current` – that [points to](https://medium.com/@TimSeverien/symlink-is-the-answer-ab13379e342f) the folder of the version that is currently active. You create a symbolic link on a Unix-based operating system using the bash [command `ln`](https://www.youtube.com/watch?v=mA08E59-zo8).

For example:

```bash
avengers@intek:~$ cd digicam
avengers@intek:~/digicam$ ln -s v1.0.1 current
avengers@intek:~/digicam$ tree -L 1
.
├── current -> v1.0.1
├── v1.0.0
└── v1.0.1
```

If you needed to rollback from the last version to a previous version, you just need to delete your symbolic link and to recreate it to point to the previous version:

```bash
avengers@intek:~/digicam$ rm current && ln -s v1.0.0 current
avengers@intek:~/digicam$ tree -L 1
.
├── current -> v1.0.0
├── v1.0.0
└── v1.0.1
```

Create a symbolic link `current` to point to the current active version of your RESTful API server application. Remember to manually recreate this symbolic link when you deploy a new version of the server application to your droplet.

## Waypoint 7: Process Monitoring

The execution of your RESTful API server application may fail abruptly on the droplet. This could happen when your application faces an unexpected and quite rare condition such as an abnormal memory consumption by other processes, a network failure that your server application's code would have not managed properly, etc. Your server application suddenly fails.

We are going to use [`supervisor`](http://supervisord.org/index.html), a client/server system that allows its users [to monitor and control](https://www.youtube.com/watch?v=8ZVf0T1I2vw) a number of processes on UNIX-like operating systems. Intek Curriculum team has already install `supervisor` on your droplet.

You need to create a [configuration file](http://supervisord.org/configuration.html) `supervisor.conf` in your `conf` folder. Your [configuration file](https://www.youtube.com/watch?v=eX7D40y9qv8) MUST contain a [section `program`](http://supervisord.org/configuration.html#program-x-section-settings) in order for `supervisord` to know which program it should start and control.

```bash
avengers@intek:~$ cd digicam/1.0.2
avengers@intek:~/digicam/1.0.2$ ls -la conf/
total 12
drwxr-xr-x 2 avengers avengers 4096 May  5 13:36 ./
drwxrwxr-x 4 avengers avengers 4096 May  5 08:16 ../
-rw-r--r-- 1 avengers avengers 1338 May  5 08:29 nginx.conf
-rw-r--r-- 1 avengers avengers 1139 May  6 10:03 supervisor.conf
```

_Note: Intek Curriculum team will reference your configuration file so `supervisord` loads it:_

```bash
devops@intek$ sudo supervisorctl reread
api.avengers.digicam.intek.edu.vn: available

devops@intek$ sudo supervisorctl update
api.avengers.digicam.intek.edu.vn: added process group

devops@intek$ sudo supervisorctl
api.avengers.digicam.intek.edu.vn                STARTING
```

## Waypoint 8: Deployment Automation Tool

Each time you need to deploy a new version of your RESTful API server application, you basically follow to these steps:

1. Download the GitHub tar gzipped file corresponding to the release you want to deploy
2. Copy this tar gzipped file on a temporary folder of the droplet (e.g., `/tmp`)
3. Decompress the tar gzipped file to a new folder named against the version of your release (under `~/digicam`)
4. Delete and create the symbolic link `current` to point to the new folder
5. Delete the tar gzipped file from the temporary folder

Instead of conducting this manual deployment each time you need to deploy a new version of your server application, write a deployment automation tool, using [`fabric`](https://www.youtube.com/watch?v=ZpZkKbZwPoA) or [`ansible`](https://www.youtube.com/watch?v=wgQ3rHFTM4E), that will dutifully connect to your droplet via SSH and executes the steps necessary to deploy your server application.


## Waypoint 9: Containerization

[Docker](https://docs.docker.com/) is a tool designed to [create, deploy, and run applications](https://www.youtube.com/watch?v=_dfLOzuIg2o) by using **containers**. Containers allow a developer to package up an application with all of the parts it needs, such as libraries and other dependencies, and deploy it as one package.

A [**container**](https://www.youtube.com/watch?v=EnJ7qX9fkcU) is an active (or inactive if exited) stateful instantiation of an **image**. Using an object-oriented programming analogy, the difference between a Docker image and a Docker container is the same as that of the difference between a class and an object. An object is the runtime instance of a class.

Create a docker file to instruct how your RESTful API server needs to be built in a Docker image. Run your RESTful API server in a Docker container.



## Waypoint 10: Continuous Integration & Continuous Development

How to deliver code faster with CI/CD : https://www.youtube.com/watch?v=Rq5TQlPyr8g

What is Jenkins? https://www.youtube.com/watch?v=LFDrDnKPOTg

[Install and Jenkins](https://www.jenkins.io/doc/book/installing/)


The Web application ARchive (WAR) file version of Jenkins can be installed on any operating system or platform that supports Java.

To download and run the WAR file version of Jenkins:

1. Download the [latest stable Jenkins WAR file](http://mirrors.jenkins.io/war-stable/latest/jenkins.war) to an appropriate directory on your machine.
1. Open up a terminal/command prompt window to the download directory.
1. Run the command `java -jar jenkins.war`.
1. Browse to [http://localhost:8080](http://localhost:8080) and wait until the Unlock Jenkins page appears.

Continue on with the Post-installation setup wizard below.

```bash
$ wget http://mirrors.jenkins.io/war-stable/latest/jenkins.war
$ java -jar jenkins.war
```


Continuous Delivery Pipeline Using Jenkins: https://www.youtube.com/watch?v=_fmX31VFbL8