# MR6500-SMS-Server
Netgear Nighthawk M6 Pro / MR6500 SMS HTTP Server

Additional documentation can be found here:  
https://www.tarball.ca/posts/netgear-nighthawk-m6-pro-sms-server/

# The Problem
Netgear decided to remove the ability to send SMS messages on the Nighthawk M6. Having SMS sending capability is handy, especially for alerting on events, such as Internet outages. This workaround implements a simple HTTP server using Perl that can be used to send messages through the device. Perl was chosen begrudgingly because it was the only capable scripting language that comes by default with the device.

# Prerequisite
You must have access to the root telnet shell of the device. You can find details at the URL below how to do this. This is outside of the scope of this tool and no assistance will be provided.  
https://github.com/0xBAADF0OD/netgear_mr6400/tree/main/netgear-AT-commands#enable-root-telnet-port-23

# Disclaimer
Note: This tool was tested on an AT&T branded Nighthawk M6 Pro / MR6500 running the following software versions:  
Firmware Version: NTGX65_10.04.22.01  
Modem Version: MPSS.DE.1.0-02593-OLYMPIC_GENALL_PACK-1  
GUI Version: MR6500-1A1NAS_05.00.63.00  

**There is no warranty included with this software so use at your own risk. If you brick your device it's on you.**

# How to install
1. Telnet to the root shell of the M6.
```
$ telnet 192.168.1.1
Trying 192.168.1.1...
Connected to 192.168.1.1.
Escape character is '^]'.

QTI Linux reference nogplv3 distro targeting performance builds. fatal: not a git repository (or any of the parent directories): .git sdxlemur

/ #
```
2. Paste the contents of sms_server to /mnt/userrw/sms_server. The vi editor is available in the root shell. Update the $password and $phone_number variables to reflect your desired password and phone number that messages will be sent to.

3. Make the sms_server file executable:
```
/ # chmod +x /mnt/userrw/sms_server
/ # ls -l /mnt/userrw/sms_server
-rwxr-xr-x    1 root     root          3351 Dec 19 17:12 /mnt/userrw/sms_server
```
4. Add the following to the bottom of /etc/init/early_init.sh:
```
/mnt/userrw/sms_server &
```
Make sure to add the & so the server will launch and the init script can exit.

5. Reboot the device.

6. Once it reboots you can go to the following address:
http://[ip of your M6 Pro]:767

Enter the password you provided in the sms_server script and a message (limited to 160 characters) and click submit. You should receive a SMS on the phone number you defined in the script. If not, by default the log of the communication with the modem built into the M6 will show and provide insight into why it didn't work.

Example using curl:
```
$ curl "http://192.168.1.1:767/action?password=password&message=hello+world"
Debug log:
AT+CMGF=1
OK
AT+CMGS="+12223334567"
> hello world

+CMGS: 70
Status: Message sent.
```
