# Zabbix_TCP_Ports_and_Systemd_Service_Template

## Macros used

### For TCP Port Template
Zabbix will discover all tcp listening ports. You can filter by using `marco` value as per following.
|Name|Description|Value(Example)|Type|
|----|-----------|-------|----|
|{$TCP.PORT.MATCHES}|<p>-</p>|(80\|443\|22\|3306)|Regex|

in this example, only create item and trigger for port 80, 443, 22 and 3306.

### For Systemd Services Template
Zabbix will discover all systemd services. You can filter by using `marco` value as per following.
value should be service name without ".service". for example, sshd.service => sshd.
|Name|Description|Value(Example)|Type|
|----|-----------|-------|----|
|{$SERVICES}|<p>-</p>|(sshd\|mysqld\|nginx\|apache2)|Regex|

in this example, only create item and trigger for service sshd, mysqld, nginx and apache2.

## Template links

There are no template links in this template.

## Discovery rules
### Linux
#### For TCP Ports Template
|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|TCP Ports Discovery|<p>Discovers TCP within a single instance specified by Users Parameter.</p>|`Zabbix agent`|netstat.tcp.listen <p>Update Interval 1h</p>|

#### For Systemd Services Template
|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Service Discovery|<p>Discovers Systemd Services within a single instance specified by Users Parameter.</p>|`Zabbix agent`|check_services.all <p>Update Interval 1h</p>|

### Windoes
#### For TCP Ports Template
|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Windows TCP Ports Discovery|<p>Discovers TCP within a single instance specified by Users Parameter.</p>|`Zabbix agent`|windows.netstat.tcp.listen <p>Update Interval 1h</p>|

## Items collected
### Linux
#### For TCP Ports Template
|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|TCP Port {#PORT}|check tcp connection with built-in net.tcp.service[tcp,,{#PORT}]|Zabbix Agent|net.tcp.service|
|OS Version|Get Operating System Version|Zabbix Agent|os.get.ver|
|Crond Job Status|check crond job processes|Zabbix Agent|cron.job.status|
|Java Job Stats|check java job processes|Zabbix Agent|java.job.status|

#### For Systemd Services Template
|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|{#SERVICE_NAME} service|check service status by using system.run|Zabbix Agent|system.run|

### Windows
#### For TCP Ports Template
|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|OS Version|Get Operating System Version|Zabbix Agent|os.get.ver|


## Zabbix Agent Configuration

add following line in #zabbix-agentd.conf for Discovery.
### Linux
#### For TCP Port Template
```sh
UserParameter=netstat.tcp.listen,netstat -tln | grep -oP '(?<=([\:]{3})|([0-9]:))(?<!(::1:))(?<!(127.0.0.1:))[0-9]+'
UserParameter=os.get.ver,cat /etc/os-release | grep -P -o '(?<=^VERSION=).*|((?<=^NAME=).*)'
```
add following line in #zabbix-agentd.conf (if needed) for Crond.
```sh
UserParameter=cron.job.status,pgrep -c crond
```

add following line in #zabbix-agentd.conf (if needed) for Java.
```sh
UserParameter=java.job.status,pgrep -c java
```

#### For Systemd Service Template
```sh
UserParameter=check_services.all,systemctl list-units --type=service --all | grep -oP '^  [\S]+(?=(\.service))' | awk '{$1=$1};1'
```
you have to allow item key (system.run) in zabbix agent conf file for Remote Command. add following line in zabbix-agentd.conf
```sh
AllowKey=system.run[systemctl is-active *]
```


### Windows
#### For TCP Port Template
```sh
UserParameter=windows.netstat.tcp.listen,netstat -anp tcp | FINDSTR LISTENING
UserParameter=os.get.ver,systeminfo | FINDSTR /C:"OS Name"
```

## Contributing

If you have suggestions for improvements or would like to contribute to this project, please fork the repository and submit a pull request. For major changes, please open an issue first to discuss what you would like to change.

## Author

[Thaw Thu Han](https://github.com/ThawThuHan)
