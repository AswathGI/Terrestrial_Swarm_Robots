# Enabling AD-Hoc Network on your RPi

## Update the RPi
```shell
$ sudo apt-get update
$ sudo apt-get upgrade
```

## WLan ip_addresss
### Check the WLan ip_address that is connected to the RPi

```shell
$ ifconfig
```

<img src="https://github.com/AswathGI/Ad-hoc-RaspberryPi/blob/master/Screenshot%202020-06-22%20at%2015.04.02.png">

#### In my case WiFi ip_address is set to 
> 100.89.173.31

here the subnet of your ip_address is "100.89.173"

#### And the WiFi port name is 
> wlan0

Remember these. 

## Configure Network Interface

### Go to network setting Directory
```shell
$ cd /etc/network/
```
#### Then type check the list of files available
```shell
$ ls
```
#### You should find a file name called 
> interfaces

We have to edit this file now. 

#### Go to editing mode by typing in:
```shell
$ sudo nano interfaces
```
### You should see something like this
<img src="https://github.com/AswathGI/Ad-hoc-RaspberryPi/blob/master/Screenshot%202020-06-22%20at%2015.17.26.png">

#### Now add the following lines into this file: 
```shell
auto wlan0
iface wlan0 inet static
 address 100.89.171.51
 netmask 255.255.255.0
 wireless-channel 1
 wireless-essid Raspberry Pi
 wireless-mode ad-hoc
```

#### Makes changes to these following, according to your wish

##### Replace wlan0 to your WiFi port name in the following lines: 
> auto wlan0

> iface wlan0 inet static

This sets your WiFi address to be Static

##### Next change the subnet. 

> address 100.89.171.51


Note: Here I have entered " 100.89.171 " which is different from my original WiFi router subnet (100.89.173) that we checked ealier. Please make sure that these subnets are different from you original router. In the finall line I have given 51, you can use any number from 00 - 999. Finall remember this address. 

##### Replace SSID
> wireless-essid Raspberry Pi

This will be your SSID of this Ad-Hoc network. You can change "Raspberry Pi" to any name here. And that will be set as your SSID. 

#### Finally keep the rest as same as it is, Save the file and Exit. 


## Creating DHCP Server
#### While being there in the /etc/network/ directory install DHCP server package by typing in: 
```shell
$ sudo apt-get install isc-dhcp-server
```

Once this is installed, we need to change few setting in the following files. 

### Make changes in isc-dhcp-server file

Open isc-dhcp-server file by typing in: 
```shell
$ sudo nano /etc/default/isc-dhcp-server
```

##### isc-dhcp-server file
<img src="https://github.com/AswathGI/Ad-hoc-RaspberryPi/blob/master/Screenshot%202020-06-22%20at%2015.28.40.png" >

Inside this file, you should see something like this. 
Here, Enter the WiFi port name in any of these interfaces. 
I have used interfaces v4 as my interface and it looks like this: 
> INTERFACESv4="wlan0"

#### Finally, Save and Exit.

### Next, make changes in dhcpd.conf file

Enter into the file by typing this command: 
```shell
$ sudo nano /etc/dhcp/dhcpd.conf
```
##### The file should look something like this. 
<img src="https://github.com/AswathGI/Ad-hoc-RaspberryPi/blob/master/Screenshot%202020-06-25%20at%2001.06.30.png" >

#### Now make following changes in this file.
##### Set Domain name
> option domain-name "example.org";

This is completely an optional step. If you don't have any domain name to be set, you can skip this step, but follow from the next step.
If you have a Domain name, replace "example.org" with your domain name. 

##### Set the Domain-name Server
Type this line below the Domain name.
> option domain-name-servers 8.8.8.8

I'm setting my server as "8.8.8.8" if you have set a server already, you can use that name here, else follow the same.

##### Enable DHCP Server
Just uncomment "authoritative" as shown in this figure. Once this done, it will enable DHCP server to run on RPi.
<img src="https://github.com/AswathGI/Ad-hoc-RaspberryPi/blob/master/Screenshot%202020-06-22%20at%2015.31.33.png" >

##### Configuration for internal subnet
Uncomment this section and make following changes as shown in this figure. 
<img src="https://github.com/AswathGI/Ad-hoc-RaspberryPi/blob/master/Screenshot%202020-06-25%20at%2001.07.53.png" >
##### Here..
In the first line, the enter the same subnet that you gave in interfaces. In my case it was "100.89.171" and leave the netmask as it is if you haven't changed it in the interfaces file.
After completing, this line should look like this:
> subnet 100.89.171.0 netmask 255.255.255.0 

Next give the Range for your subnet. This allows to designate a ip_address to other devices that are connected to this network. 
I'm giving a range for 20 ip_address, and it should look like this: 
> range 100.89.171.60 100.89.171.80;

Change the domain and server name if you have replaced it earlier. 
In my case it looks likes this: 
> option domain-name-servers 8.8.8.8;

> option domain-name "internal.example.org";

Now type in the static ip_address that you gave in interfaces file.
In my case it is: 
> option routers 100.89.171.51;

Use the same subnet for the broadcast-address.
In my case it is: 
> option broadcast-address 100.89.171.255;

Finally, change the lease time, if you wish to. Else please skip and leave it as it is. 

### Finally, Save & Exit file. 

## Start the Ad-Hoc Network
Once you have done all these steps in the /etc/network/ directory, exit from that directory by typing command: 
```shell
$ cd
```
### Restart the RPi by typing in command
```shell
$ sudo reboot
```
Your Pi should start the Ad-Hoc network and it should be visible on other devices under available WiFi signals. 


##                   -----------x-x-x-x-----------
