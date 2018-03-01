# Hadoop Multi-Node Setup

After the clone, for each node click on settings, Network and change the adaptor to 
Check if the slave node and the main node can communicate with each other.  We will do this by "pinging" each using the ping command from the terminal.

IN each machine, type the following command:

> ifconfig

Take note of the output and jot down the ip address for both your original node (the master) and the slave node as shown in the figure below.
