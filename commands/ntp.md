# NTP
---
Network Time Protocol Enumeration

NTP is a protocol designed to synchronise clocks of networked computers.  From a Vulnerability Analysis/ Penetration testing aspect, the data available when querying the ntp server can prove quite valuable and is usually available without any formal authentication being required.

The following commands can be used against an NTP server:

`ntpdate
ntptrace
ntpdc
ntpq`

Ntpdate - ntpdate can collect a number of time samples from a number of time sources (i.e., multiple NTP servers)

`ntpdate` [-bBdoqsuv] [-a key] [-e authdelay] [-k keyfile] [-o version] [-p samples] [-t timeout]  [server/IP_address]

`# ntpdate 192.168.0.1
27 Dec 11:50:49 ntpdate[627]: adjust time server 192.168.0.1 offset -0.005030 sec

Options

-a key Enable the authentication function/specify the key identifier to be used for authentication.
-B         Force the time to always be slewed.
-b        Force the time to be stepped.
-d        Enable debugging mode.
-e authdelay  Specify the processing delay.
-k keyfile  Specify the path for the authentication key file as the string keyfile. The default is /etc/ntp.keys.
-o version  Specify NTP version for outgoing packets as the integer version, can be 1 or 2. Default is 3.
-p samples  Specify # of samples to be acquired from each server, with values from 1-8. Default is 4.
-q       Query only - don't set the clock.
-s       Divert logging output from the standard output (default) to the system syslog facility.
-t timeout Specify the maximum time waiting for a server response. Default is 1 second.
-u       Use an unprivileged port or outgoing packets.
-v       Be verbose.`

Ntptrace - ntptrace determines where a NTP server gets its time from, and follows the chain of NTP servers back to its primary i.e. master, time source.  If you supply no argument ntptrace will start with the localhost, if a server is specified, the localhost will appear last.

`ntptrace` [-vdn] [-r retries ] [-t timeout] [servername/IP_address]

`# ntptrace
localhost: stratum 4, offset 0.0019529, synch distance 0.143235
192.168.0.1: stratum 2, offset 0.0114273, synch distance 0.115554
192.168.1.1: stratum 1, offset 0.0017698, synch distance 0.011193
Options
-d     Display debugging output.
-n     Does not print host names only IP addresses are shown. May be useful if a nameserver is down.
-r retries Sets the number of retransmission attempts for each host (default = 5).
-t timeout Sets the retransmission timeout (in seconds) (default = 2).
-v     Prints verbose information about the NTP servers.`

Ntpdc - ntpdc is used to query the ntpd daemon about its current state and to request changes in that state. The program may be run either in interactive mode or controlled using command line arguments.

`ntpdc` [-ilnps] [-c command] [hostname/IP_address]

`root@attacker]# ntpdc -c sysinfo 192.168.0.1
***Warning changing to older implementation
***Warning changing the request packet size from 160 to 48
system peer: 192.168.1.100
system peer mode: client
leap indicator: 00
stratum: 5
precision: -15
root distance: 0.00107 s
root dispersion: 0.02306 s
reference ID: [192.168.1.100]
reference time: f66s4f45.f633e130 Wed, Jun 28 2006 11:06:11.631
system flags: monitor ntp stats calibrate
jitter: 0.000000 s
stability: 4.256 ppm
broadcastdelay: 0.003875 s
authdelay: 0.000107 s`

`root@attacker]# ntpdc -c monlist 192.168.0.1
Warning changing to older implementation
Warning changing the request packet size from 160 to 48
remote address port local address count m ver code avgint lstint
192.168.0.222 32786 192.168.0.1       5 7 2      0 0       188
192.168.1.100 123   192.168.0.1    4299 3 2      0 22      1022110
192.168.2.133 32766 192.168.0.1      10 4 2      0 559     1110
192.168.2.133 123   192.168.0.1       6 3 6      0 1101    1502
attacker      32812 127.0.0.1         1 5 3      0 1022142 1022142
`

Options

-c command Following argument interpreted as an interactive format command. Multiple -c options may be given.
-i      Force ntpdc to operate in interactive mode.
-l      Obtain a list of peers which are known to the server(s). This switch is equivalent to -c listpeers
-n      Output all host addresses in dotted-quad numeric format rather than host names.
-p      Print a list of the peers as well as a summary of their state. This is equivalent to -c peers.
-s      Print a list of the peers as well as a summary of their state.  This is equivalent to -c dmpeers.

Available commands (abridged):

listpeers     Obtains and prints a brief list of the peers.
peers             Obtains a list of peers for which the server is maintaining state.
sysinfo         Print a variety of system state variables.
reslist         Obtain and print the server's restriction list.
monlist [version] Obtain and print traffic counts collected and maintained by the monitor facility.

Ntpq -ntpq is used to monitor NTP daemon ntpd operations and determine performance.

`ntpq` [-inp] [-c command] [host/IP_address]

`root@attacker]# ntpq 192.168.01
ntpq> lpeers
remote              refid st t when poll reach delay offset jitter
*192.168.1.100 LOCAL(0)    4 u   18   58   344 0.655 -0.039  0.029`

`ntpq> version
ntpq 4.2.0a@1.1196-r Mon May 07 14:14:14 EDT 2006 (1)`

`ntpq> host
current host is 192.168.0.1`

`ntpq> readlist
assID=0 status=0674 leap_none, sync_ntp, 7 events, event_peer/strat_chg,
system="SunOS", leap=00, stratum=5, rootdelay=0.655,
rootdispersion=20.080, peer=40852, refid=192.168.1.100,
reftime=c66b4f07.d732d455 Sat, Jul 01 2006 17:28:11.773, poll=6,
clock=0xc66b4f3b.595ed455, phase=-0.040, freq=80337.65, error=0.01`

Options

-c       Following argument is interpreted as an interactive format command. Multiple -c options may be given.
-d       Debugging mode.
-i      Force ntpq to operate in interactive mode.
-n      Output all host addresses in dotted-quad numeric format rather than host names.
-p      Print a list of the peers as well as a summary of their state.

Available Commands (abridged):

lpeers     A summary of all associations for which the server is maintaining state is printed.
peers       Obtains a current list peers of the server, along with a summary of each peer's state.
lpassociations      Print data for all associations
version    Everything you need to know about the software version and generation time.
system      The operating system version and release identifier.
hostname  The name of the host

As you can see, this is an awful lot of information that you can get from a target machine running ntp with no restrictions applied to it.

Restrictive Policies:

The restrict option in /etc/ntp.conf allows you to control which machines can access your server.  If you want to deny all machines from accessing your NTP server, add the following line to /etc/ntp.conf:

restrict default ignore
If you only want to allow machines within your own network to synchronize their clocks with your server, but ensure they are not allowed to configure the server or used as peers to synchronize against, add

restrict 192.168.0.1 mask 255.255.255.0 nomodify notrap
instead, where 192.168.0.1 is a local IP address with a netmask of 255.255.255.0.
