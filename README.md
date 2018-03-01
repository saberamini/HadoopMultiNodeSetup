# Hadoop Multi-Node Setup

After the clone, for each node click on settings, Network and change the adaptor to 
Check if the slave node and the main node can communicate with each other.  We will do this by "pinging" each using the ping command from the terminal.

IN each machine, type the following command:

> ifconfig

Take note of the output and jot down the ip address for both your original node (the master) and the slave node as shown in the figure below.


Now try to ping both the slave and the master from the other machine.

> ping ip_address

```
hduser@HadoopNode:~$ ping 10.101.243.183
PING 10.101.243.183 (10.101.243.183) 56(84) bytes of data.
64 bytes from 10.101.243.183: icmp_seq=1 ttl=64 time=0.414 ms
64 bytes from 10.101.243.183: icmp_seq=2 ttl=64 time=0.896 ms
64 bytes from 10.101.243.183: icmp_seq=3 ttl=64 time=0.512 ms
^C
```

The output should be something as above.  To stop the ping enter CTRL-C.

We need to now change the name of the machines.  Since we cloned our original Hadoop Node to get a master and slave, both nodes now have the same name (whatever you chose for your clone).  Remember, the machine neame is always displayed in the terminal prompt after user name and the @ sign.  (hduser@[machinename])

To change the machine name, execute the following command on both machines

> sudo gedit /etc/hostname

For the slave machine, change the machine name to "slave01".  For the master, change the machine name to "master"

We will now configure all the ip-addresses.  We need a way for the master and slave to know each other's ip address.

> sudo gedit /etc/hosts

You should see something like the following:

```
127.0.0.1	localhost
127.0.1.1	HadoopNode

# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```

Here HadoopNode is the machine name (that we just changed).  It may be different for your machine.  First delete or comment out the old address "127.0.1.1 [machine_name]" and place the ip address and name as follows:

```
10.131.248.81 slave01

10.101.243.183 master
```

Note that your ip addresses will be different.  

Do the same thing for your other node (slave or master depending on what you did first).

For the changes to take effect, we need to reboot our machines. For both master and slave, enter the following command:

> reboot

Log in to both machines and open a terminal.  YOu should see the hostname changes having taken effect (master is called hduser@master and slave is called hduser@slave).  If not, check again that the hostname file was changed and saved as outlined above.

For a sanity check though, run the following command on both master and slave:

> hostname

You should get master and slave as output for the particular machine.

# # Ping Master and Slave using host name

We will now try to ping each node using the host name rather than the IP address.  This will confirm that the IP addresses and names were changed correctly.  

> ping slave01

You should see something similar to the following:

```
hduser@master:~$ ping slave01
PING slave01 (10.131.248.81) 56(84) bytes of data.
64 bytes from slave01 (10.131.248.81): icmp_seq=1 ttl=64 time=0.313 ms
64 bytes from slave01 (10.131.248.81): icmp_seq=2 ttl=64 time=0.340 ms
64 bytes from slave01 (10.131.248.81): icmp_seq=3 ttl=64 time=0.340 ms
64 bytes from slave01 (10.131.248.81): icmp_seq=4 ttl=64 time=0.456 ms
64 bytes from slave01 (10.131.248.81): icmp_seq=5 ttl=64 time=0.722 ms
64 bytes from slave01 (10.131.248.81): icmp_seq=6 ttl=64 time=0.354 ms
^C
--- slave01 ping statistics ---
6 packets transmitted, 6 received, 0% packet loss, time 5106ms
rtt min/avg/max/mdev = 0.313/0.420/0.722/0.144 ms
hduser@master:~$ 
```

Similarly, perform the same test on the slave

> ping master

You should get a similar output as for the master case.  If not, then check your hosts files (sudo gedit /etc/hosts) and make sure the name of the machines and their ip addresses have correctly been entered.


# # Test SSH connectivity

From both the slaev and master, try to first ssh to your own machine and to the other machine.  

> ssh master

> ssh slave01

Type yes for any prompts.  You should be able to SSH without any password requests.

# # Update your configuration files

You need to update your core-site.xml <b>for master and slave(s)</b> and "localhost" to "master".  We will also remove the temporary directory.  Your core-site.xml file will therefore only have the following:

```
  <property>  
    <name>fs.defaultFS</name>
    <value>hdfs://master:9000</value>
    <description> localhost may be replaced with a DNS that points to the NameNode. </description>
  </property>
</configuration>
```



