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
---



*To be continued*
