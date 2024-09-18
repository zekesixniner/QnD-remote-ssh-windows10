#### This guide intends to shortly describe how to setup a poor man's VPN on windows10 calling home using SSH client/server only.

Install OpenSSH-server as optional feature in win10

Download<br> 
https://www.nirsoft.net/utils/nircmd.zip @ https://www.nirsoft.net/utils/nircmd.html<br>
https://stackoverflow.com/questions/42391319/run-command-as-system-user-in-powershell<br> 
`nircmdc.exe elevatecmd runassystem c:\windows\System32\cmd.exe`<br> 
create keys (priv&pub) with `ssh-keygen` and upload pub key to <br>
`user@remote.host.on.internet`<br>
and open corresponding port towards this server.

Install a service with on win10 end target<br> 
https://nssm.cc/download<br> 
`C:\Windows\System32\OpenSSH\ssh.exe -i C:\Windows\System32\config\systemprofile\.ssh\id_rsa -p 22 -N -R 2222:127.0.0.1:22 user@remote.host.on.internet -o ExitOnForwardFailure=yes -o StrictHostKeyChecking=no -o ConnectTimeout=60`<br> 
as .bat or with switches.

On your LAN ssh towards the remote ssh connection in this example with one jump host (`user@server01.host.arpa`)<br>
`ssh -L 127.0.0.2:3390:127.0.0.1:3389 -J user@server01.host.arpa,user@server02.host.arpa administrator@localhost -p 2222`<br>
where `user@server02.host.arpa` is the equivalent internal LAN adress for `user@remote.host.on.internet`<br>
and as in this example start remote desktop to<br>
`127.0.0.2:3390`<br> 


The following should work for VNC(`127.0.0.2:5901`):<br>
`ssh -L 127.0.0.2:5901:127.0.0.1:5900 -J user@server01.host.arpa,user@server02.host.arpa administrator@localhost -p 2222`<br><br>
 
On  `user@remote.host.on.internet` which is the equivalent `user@server02.host.arpa` on the internal LAN view the remote connection with<br>
`netstat -tunpl | awk '{print , }' | grep ':2222'`<br>
Close connection on `user@remote.host.on.internet` with<br>
``for pid in `netstat -tunpl | awk '{print , }' | grep ':2222' | awk '{print }' | cut -d '/' -f1`; do kill -9 $pid; done``
