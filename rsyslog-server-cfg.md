## RSYSLOG
#### Server setting (HOST PC)
1. Download rsyslog  
`  
sudo apt-get install rsyslog  
`  
Following modifications has to be added in the `/etc/rsyslog.conf` file  
1. Modify rsyslog.conf file to enable remote logging using network, we need to set protocol(TCP/UDP) to used for communication.  

For UDP connection, search and uncomment following two lines.  
`  
$ModLoad imudp 
$UDPServerRun 514 
`  
NOTE: u can use any port than 514 as per your need.  

For TCP connection, search and uncomment following two lines. (We are using TCP connection now)  
`
$ModLoad imtcp  
$InputTCPServerRun 514  
`

NOTE: to use both connection, uncomment all the above four lines  

2. Define rule set for processing remote logs in the following format  
`
facility.severity_level	destination (where to store log)  
`
where   

- facility: is type of process/application generating message, they include auth, cron, daemon, kernel, local0..local7. Using * means all facilities.  

- severity_level: is type of log message: emerg-0, alert-1, crit-2, err-3, warn-4, notice-5, info-6, debug-7. Using * means all severity levels and none implies no severity level.   
- destination: is either local file or remote rsyslog server (defined in the form IP:port).  

You can use `RemoteLogs` template to gather and write all the received remote messages to distinct logs under `/var/log/` based on the HOST and application/process.    
`
$template RemoteLogs,"/var/log/%HOSTNAME%/%PROGRAMNAME%.log"  
*.* ?RemoteLogs  
& ~  
` 
NOTE: Note that these rules must come before any rules for processing local messages    
The second line `*.* ?RemoteLogs` means record messages from all facilities at all severity levels using the RemoteLogs template configuration.  
The final line `& ~` instructs rsyslog to stop processing the messages once it is written to a file. If you don’t include `& ~`,  messages will instead be written to the local files.  
4. After adding above two changes, restart the `rsyslog` daemon.  
`
sudo systemctl restart rsyslog  
`
5. Verify the rsyslog network sockets using following command  
`
sudo ss -tulnp | grep "rsyslog"  
`
You will observe something like below on console,  
`
tcp        0      0 0.0.0.0:514             0.0.0.0:*               LISTEN      9207/rsyslogd       
tcp6       0      0 :::514                  :::*                    LISTEN      9207/rsyslogd  
`

PS:  
For sama5d27 board, rsyslog will create `sama5d27-som1-ek-sd` folder under `/var/log/` and you can see all the logs are stored under this directory.  

