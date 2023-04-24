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
You may also receive an error message like " *An error occured while enrolling for a certificate. The certificate request could not be submitted to the certificate authority. Error: The RPC server is unavailable. 0x800706ba (WIN32: 1722 RPC_S_SERVER_UNAVAILABLE* " when requesting a certificate. When this happens execute the below command on your Root CA server.

```powershell
# On Domain Controller test connectivity using
certutil -ping
# BAD RESULTS
#    CertUtil: No local Certification Authority: use -config option
#    CertUtil: -ping command FAILED: 0x80070103 (WIN32/HTTP: 259 ERROR_NO_MORE_ITEMS)
#    CertUtil: No more data is available.

# Test RPC Connection
Get-WmiObject -Class Win32_ComputerSystem -ComputerName ca01.domain.com

# On CA Server do
certutil -setreg SetupStatus -SETUP_DCOM_SECURITY_UPDATED_FLAG
Restart-Service -Name certsvc
```

On the CA server you may also need to enable the below firewall rules

- Remote Scheduled Tasks Management (RPC);
- Remote Scheduled Tasks Management (RPC-EPMAP);
- Windows Management Instrumentations (ASync-In);
- Windows Management Instrumentations (DCOM-In);
- Windows Management Instrumentations (WMI-In);
- Windows Management Instrumentations (DCOM-In);
- Windows Remote Management (HTTP-In).

If you have set up WMI Group Policy settings in your environment you may also need to look at the DCOM group policy permissions in **Computer Configuration > Policies > Windows Settings > Security Settings > Local Policies > Security Options > DCOM: Machine... **
