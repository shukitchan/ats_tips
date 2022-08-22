Forwarding requests to another host using iptables
==================================================

First we need ip_forward
```
sudo sysctl -w net.ipv4.ip_forward=1
sudo sysctl -p
sudo sysctl -a | grep "ip_forward"
```

Then we add entries in iptables for the forwarding 
```
# forwarding requests from source address (source_ip) / local address (local_add) / local port (local_port) to remote address (remote_add) / remote port (remote_port)
sudo iptables -t nat -A PREROUTING -p tcp -s <source_ip> --dport <dest_port> -j DNAT --to-destination <remote_add>:<remote_port>
sudo iptables -t nat -A POSTROUTING -p tcp -d <remote_add> --dport <remote_port> -j SNAT --to-source <local_add>
```

References
* https://en.wikipedia.org/wiki/Iptables#/media/File:Netfilter-packet-flow.svg
* https://serverfault.com/questions/586486/how-to-do-the-port-forwarding-from-one-ip-to-another-ip-in-same-network
