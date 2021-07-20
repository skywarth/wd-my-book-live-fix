# Steps to secure your device



## Initial Containment

1. Disconnect from the router immediately.
2. Disable UPnP from your router interface.
3. Connect your WD My Book Live to a PC through RJ-45. 
4. Access the UI interface using the ip of the NAS.
  - If you previously set some static ip on the NAS,use that ip to connect to it's interface.
  - If you didn't set any static ip and left it at default (automatic dhcp) then it used some available ip given by your PC. Do an ARP (yeah kinda lazy, bringing in the big gun lmao) and find it's ip.
  - If you can't really establish connection; try to "use link-local only" as your wired connection IPv4 method from settings. Find it per your OS
  
5. Disable these from the UI:
  - Remote access
  - FTP
  - Twonky

6. Make sure you didn't receive no email regarding system restart or network link down from your NAS. Because you mighta setup a bridge on your PC's ethernet and therefore could've shared internet to the NAS.

---

## Taking matters into your own hands



8. Go to http://(your.nas.ip)/UI/ssh
9. Enable ssh, default credentials.
10. SSH into your NAS using this command ```ssh root@(your.nas.ip)``` . Enter the default password.
11. Edit ```/var/www/Admin/webapp/includes/languageConfiguration.php``` using your favorite editor. E.g: ```sudo nano /var/www/Admin/webapp/includes/languageConfiguration.php```
12. Find this 
```exec("sudo bash -c '(echo \"language {$changes["language"]}\">/etc/language.conf)'", $output, $retVal);``` 

and replace it with 

```
if (!preg_match('/^[a-z]{2}_[A-Z]{2}$/', $changes["language"], $dummy)) return 'BAD_REQUEST';
exec("sudo bash -c '(echo '\"'\"".escapeshellarg("language {$changes["language"]}")."\"'\"'>/etc/language.conf)'", $output, $retVal);
```


12. In the same file, find this ```exec("sudo bash -c '(echo \"language {$lang["language"]}\">/etc/language.conf)'", $output, $retVal);```

and replace it with

```
if (!preg_match('/^[a-z]{2}_[A-Z]{2}$/', $lang["language"], $dummy)) return 'BAD_REQUEST';
exec("sudo bash -c '(echo '\"'\"".escapeshellarg("language {$lang["language"]}")."\"'\"'>/etc/language.conf)'", $output, $retVal);
```
13. Now after you are done with languageConfiguration.php file, navigate to edit ```/usr/local/sbin/factoryRestore.sh```.
14. Add ```exit``` statement to second or third line where it's empty. Like so:
```
#!/bin/bash

#---------------------

exit

PATH=/.......
```
15. Repeat the "Step 14" for the ```wipeFactoryRestore.sh``` file which is in the same directory.
16. Edit ```/var/www/Admin/webapp/classes/api/1.0/rest/system_configuration/system_factory_restore.php``` with an editor. File path may vary but filename is same.
17. Find the method with signature ```function post($urlPath,....) {```. Inside this method, you'll see some lines that are commented (//). Uncomment them. (Sorry i can't share much detail about it since it's a proprietary software.


## Disabling any remote connection
18. Run this command ```sudo route del default gw <your-router-ip>```. For example if your gateway/router is 192.168.1.1 then ```sudo route del default gw 192.168.1.1```. In essence, this removes the default gateway route to your router so unknown or foreign connections will never be established. Even though UFW is far easier and better, it is not available in WD My Book Live. Since you can't (and shouldn't) expose it to internet, you can't install UFW.
19. If anything goes wrong on step 18, head over to my question on here :[Question](https://unix.stackexchange.com/questions/656892/disallow-any-internet-connection/656897#656897). It has a useful answer which you can use as alternative.



## Finalizing
19. Go to: http://(your-nas-ip)/UI/ssh and disable SSH.
20. From the settings on UI, open 'Remote Access' tab. Do these:
    - Uncheck 'Remote Access' checkbox.
    - Connection options: Manual
    - WD 2go Port 1: 65535 (or any other port that is blocked by your router)
    - WD 2go Port 2: 65354 (or any other port that is blocked by your router)
22. Navigate to the UI panel. Enter the network tab from settings. Set network mode to 'static'. Now this part is tricky and you should be super careful. One mistake and you might not be able to connect to the device again unless you reset it. If you are not confident with your network knowledge, Skip this step. After this step, IP address of NAS will change and you won't be able to access it by connecting it to PC. So be careful.
    - **Network mode:** static
    - **IP Address:** set this to some ip in your modem/router's outer range. E.g your router acts as a DHCP server for 192.168.1.0 to 192.168.1.240 with 192.168.1.0/24. In this case you can set your NAS's IP to anything ranging from 192.168.1.241 to 192.168.1.254. 
    - **Netmask:** According to your router's netmask/subnet mask. E.g 255.255.255.0
    - **DNS server:** Your router's IP. E.g 192.168.1.1
23. Hook the NAS to the router.

## Testing
22. If you receive any email or notification of some sort, DISCONNECT IT FROM THE ROUTER IMMEDIATELY.
23. After running the command on step 18 or 19, try to ping an external host. E.g: run ```ping google.com```. It should fail.
24. Open up your UI panel, check internet connection status. It should say "no internet connection".
25. Connect to your NAS how you would normally do. FTP, AFP, SMB ... See everything is in order and you can Read/Write.




---
These are all the things i did to secure my device as far as i remember. Hope it helps you. I learned some steps from some user at WD forum (thanks).
