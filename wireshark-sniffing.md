Useful video series:
- [WiFi Wireless Security Tutorials Playlist](https://www.youtube.com/watch?v=wd9KdsxTVa8&list=PL6gx4Cwl9DGC04FAovs0lswXTPXeomORn&index=1)

Useful commands
- ifconfig / iwconfig (view network/wireless interfaces)
- iw list (list physical wireless interfaces, ie. NIC)
- airmon-ng (change wireless card to monitor mode)

I wanted to sniff out the protocol being used to communicate between the Tello drone and the ipad app that controls it. However, by default, the wireless network card only listens to ip packets addressed to it, so running wireshark on my machine was not showing any of their packets.

Running `iwconfig wlan0` shows that by default, the wireless card is set to "Managed" mode. We can change it to monitor mode by running
```
sudo ifconfig wlan0 down
sudo iwconfig mode monitor
sudo ifconfig wlan0 up
```
However on my msi laptop, it switches back to managed mode immediately. In order to circumvent this, we need to use the more powerful `airmon-ng` tool, which is able to list and kill the daemon processes that are most likely switching back our mode to managed.
```
sudo airmon-ng check # lists the potentially conflicting processes
sudo airmon-ng check kill # kills them
sudo airmon-ng start wlan0 # starts the new interface wlan0mon
iwconfig wlan0mon # lists the new interface created
sudo iwconfig wlan0mon channel 149 # changes channel we are sniffing on
wireshark # profit
```