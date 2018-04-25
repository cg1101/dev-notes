# netstat - usage and use cases

Usage one, display a list of active sockets for each protocol.
```
netstat [-AaLlnW] [-f address_family | -p protocol]
```

Usage two, presents the contents of one of the other network data structures.
```bash
netstat [-gilns] [-v] [-f address_family] [-I interface]
```

Usage three, used with a wait interval, continuously display information of an interface.
```bash
netstat -i | -I interface [-w wait] [-c queue] [-abdgqRtS]
```

Usage four, displays statistics for the specified protocol or address family.
```bash
netstat -s [-s] [-f address_family | -p protocol] [-w wait]
```

Usage five, displays per interface statistics for the specified protocol or address family.
```bash
netstat -i | -I interface -s [-f address_family | -p protocol]
```

Usage six, displays mbuf statistics.
```bash
netstat -m [-m]
```

Usage seven, displays routing table of the specified address family.
```bash
netstat -r [-Aaln] [-f address_family]
```

Usage eight, displays routing statistics.
```bash
netstat -rs [-s]
```
