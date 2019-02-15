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
On each HMC:
```
hscroot@hmc "mkauthkeys --add 'public_key_string_goes_here'"
```

3) You will need to manually ssh from the nagios server to each monitored HMC to update the known_hosts file on the nagios server.  Example shown below:
```
$ ssh hscroot@hmc01
RSA key fingerprint is ea:a1:05:58:8d:4e:4e:c4:82:db:cf:87:75:a6:7c:7f.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'unix07,10.2.2.62' (RSA) to the list of known hosts.
```

4) You will need a section similar to the following in the commands.cfg file on the nagios server.
```
 # 'check_hmc' command definition
 define command{
    command_name    check_hmc
    command_line    $USER1$/check_hmc $HOSTADDRESS$
   }
```

