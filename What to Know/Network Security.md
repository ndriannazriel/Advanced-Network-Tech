Networks are susceptible to the following types of attacks:

- Reconnaissance Attacks - Gathering information
- Access Attacks - Exploit vulnerabilities in auth services, FTP services and web services
- DoS Attacks - Creates disruption of network services to users, devices and applications.

## Recon Attacks
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736398332000kjf6tq.png)


![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736398610000ebzzdo.png)

After performing a ping sweep to see the active addresses, a threat actor performs port scans using NMAP for example to get the services that are active.

### Access Attacks
**Password Attacks**
In a password attack, the threat actor attempts to discover critical system passwords using various methods. Password attacks are very common and can be launched using a variety of password cracking tools.

**Spoofing Attacks**
In spoofing attacks, the threat actor device attempts to pose as another device by falsifying data. Common spoofing attacks include IP spoofing, MAC spoofing, and DHCP spoofing. These spoofing attacks will be discussed in more detail later in this module

Other Access attacks include:

- Trust exploitations
- Port redirections
- Man-in-the-middle attacks
- Buffer overflow attacks

## IP Vulnerabilities and Threats

### Amplification and Reflection Attacks
1. Send a packet to all devices in a network and the source ip being the targeted host.
2. When ICMP reply, the ping will come back to the targeted host from all the devices.
3. DoS attack happens.

