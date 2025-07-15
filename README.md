# check_hmc
nagios check for IBM Hardware Management Console 

This script will allow you to monitor various aspects of an HMC, including:  
•	HMC cpu utilization  
•	HMC memory utilization  
•	HMC remote access settings  
•	HMC time synchronization settings  
•	HMC authentication to managed systems  
•	HMC connectivity to service processors on managed systems  
•	Status of system attention light on managed systems  
•	Open serviceable events on managed systems  





# Usage 
1) It is assumed that this script is run on the nagios server as the "nagios" userid  

2) You will need to setup preshared SSH key pairs between the nagios server and each HMC you want to monitor.  
On nagios server:  
```
su - nagios
ssh-keygen -t rsa
cat $HOME/.ssh/id_rsa.pub
```
Create a low-privilege read-only user account on  each HMC:
```
ssh hscroot@hmc mkhmcusr -u nagios -a hmcviewer -d monitoring --passwd StrongPasswordGoesHere
ssh nagios@hmc "mkauthkeys --add 'public_key_string_goes_here'"
```

3) You will need to manually ssh from the nagios server to each monitored HMC to update the known_hosts file on the nagios server.  Example shown below:
```
$ ssh nagios@hmc01
RSA key fingerprint is ea:a1:05:58:8d:4e:4e:c4:82:db:cf:87:75:a6:7c:7f.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'unix07,10.2.2.62' (RSA) to the list of known hosts.
```

4) You will need a section similar to the following in the services.cfg file on the nagios server.
```
      # Check IBM HMC to monitor IBM Power servers
      define service{
              use                             generic-24x7-service
              host_name                       hmc1.example.com
              service_description             HMC checks
              check_command                   check_hmc
      }
```


5) You will need a section similar to the following in the commands.cfg file on the nagios server.
```
 # 'check_hmc' command definition
 define command{
    command_name    check_hmc
    command_line    $USER1$/check_hmc $HOSTADDRESS$
   }
```

6) These checks can sometimes take longer than 60 seconds to run, which can cause nagios timeouts.  Schedule the check to run every 15 minutes from the nagios user crontab, and cached output will be used.  For example:
```
 $ whoami
nagios

$ crontab -l
1,16,31,46 * * * * /usr/local/nagios/libexec/check_hmc hmc01.example.com  1>/dev/null 2>/dev/null  #nagios helper script
2,17,32,47 * * * * /usr/local/nagios/libexec/check_hmc hmc02.example.com 1>/dev/null 2>/dev/null  #nagios helper script
```

# Sample Output
```
HMC checks OK - all 2 managed systems are running normally.  9009-22A*7803XXX  9009-22A*7803YYY
```
```
HMC checks WARN - managed system 9009-22A*7803XXX has open serviceable events, please investigate.
```
```
HMC checks WARN - system attention light illuminated on managed system 9009-22A*7803XXX , please investigate.
```
