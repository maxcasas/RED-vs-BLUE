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

-Looking at the image above we can see that port 80 is open on 192.168.1.105. Which is the machine that we are looking for.


