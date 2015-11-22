# Exhaust Collector

Exhaust Collector is one component of the [Exhaust Proof-of-Concept](https://github.com/ThatLarryPearson/exhaust-PoC).

The exhaust collector implementation is a message queue provided by [RabbitMQ](http://www.rabbitmq.com/).  

## Installation Overview

The PoC doesn't generate high data volumes so the implementation is going to be done using RabbitMQ running in a Docker container.

To get all this stuff to run, you need to install some software packages.
- [RabbitMQ](http://www.rabbitmq.com/)
- [Docker](http://docker.io/)
- [AMQP Tools](https://github.com/rmt/amqptools)

### Installing Docker

wget -qO- https://get.docker.com/ | sh

### Installing RabbitMQ in Docker Container

[RabbitMQ](http://www.rabbitmq.com) can be installed in a [Docker](http://www.docker.com) container by following these [instructions](https://registry.hub.docker.com/_/rabbitmq/).

To get the documentation for the RabbitMQ Docker container, you can either use this [link](https://github.com/docker-library/docs/tree/master/rabbitmq) on [github](https://github.com) or use git.  The git command will retrieve the container build instructions.

```
sudo apt-get install git-core
git clone https://github.com/docker-library/docs.git
cd docs/rabbitmq
ls -l
```

## amqp port number default 5672

Launch the RabbitMQ container:

```
export RMQ=$(sudo docker run -d --restart=always -e RABBITMQ_NODENAME=rmq --name RabbitMQ -p 15672:15672 -p 5672:5672 rabbitmq:3-management)
```

Your RabbitMQ container's identifier is now in the RMQ environment variable.

You can point your browser to [http://localhost:15672](http://localhost:15672) where you will get the login for the RabbitMQ management console.  The username/password is guest/guest.

You need your RabbitMQ container local host's external IP address in order to configure external publishers and consumers:

```
hostname -I
```
This IP address will need to be added to **all of your systems**' `/etc/hosts` files.  For example, get the output from `hostname` as shown:
```
$ hostname -I
192.168.1.51 172.17.42.1 192.168.122.1 
$ 
```
Take the **first IP address** (for example - `192.168.1.51`) in the list (it is common for there to be one or more) and add an entry (for example - `192.168.1.51 rabbitmq`) to the `/etc/hosts` file as root:
```
echo <your-ip-address-goes-here> rabbitmq >> /etc/hosts
```



## Testing

The first thing to test is RabbitMQ.  If you have a browser on the same host as your RabbitMQ container, click this link [RabbitMQ Administrative Interface](http://localhost:15672/) and login with username `guest` and password `guest`.


At least one of these emitters need to be installed to generate data that is published into RabbitMQ:
- [Performance Monitoring](https://github.com/ThatLarryPearson/exhaust-emitter-performance-monitoring)
- [Process Accounting](https://github.com/ThatLarryPearson/exhaust-emitter-process-accounting)
- [Syslog](https://github.com/ThatLarryPearson/exhaust-emitter-syslog)

Once the emitters are up and running, you can test them using:
- AMQP tools
- `/opt/exhaust/bin/syslogtopic.py`

### AMQP Tools
```
sudo apt-get -y install amqp-tools

/usr/bin/amqp-publish --help
/usr/bin/amqp-consume --help
/usr/bin/amqp-get --help

amqp-consume -e syslog -r syslog cat
```

### /opt/exhaust/bin/syslogtopic.py

This program is installed with the emitters listed above.

## Licensing

The MIT License (MIT)

Copyright &copy; 2015 Lawrence Bennett Pearson

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.


