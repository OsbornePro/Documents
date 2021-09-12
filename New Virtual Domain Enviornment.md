# Notes When Creating a New Virtual Domain Environment
Sometimes the Windows Domain Controller does not know it is on a domain network after a fresh install and promtion. 
The reason for this is the Network Location Awareness service does not have the right dependicies. 
To fix that issue the below command
```cmd
sc config nlasvc depend=NSI/RpcSs/TcpIp/Dhcp/Eventlog/DNS/NTDS
```

Also when creating a CA on a server different from your DC you may get an RPC not available error. 
Typically the reason for this is you need to add the security groups "Domain Users", "Domain Computers", and "Domain Controllers" to members of the "Builtin" Active Directory group "Certificate Services DCOM Access"
Make sure you have your DC set as the DNS server for your CA server as well. This could also be the trouble.
