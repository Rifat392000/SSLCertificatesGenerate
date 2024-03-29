# Bind9 DNS Server

```bash
sudo apt update
sudo apt install bind9
```

```bash
cd /etc/bind
```
```bash
tree
```

> [!TIP]
> Back up original configuration files (recommended)
```bash
sudo cp named.conf.options named.conf.options.original
sudo cp named.conf.local named.conf.local.original
```

> [!TIP]
>Create copies of zone files
```bash
sudo cp db.local db.ewubdserver.com
sudo cp db.127 db.56.168.192
```

> Edit global options file
```bash
sudo gedit named.conf.options
```

> [!IMPORTANT]
>named.conf.options
```bash
options {
	directory "/var/cache/bind";

	// If there is a firewall between you and nameservers you want
	// to talk to, you may need to fix the firewall to allow multiple
	// ports to talk.  See http://www.kb.cert.org/vuls/id/800113

	// If your ISP provided one or more IP addresses for stable 
	// nameservers, you probably want to use them as forwarders.  
	// Uncomment the following block, and insert the addresses replacing 
	// the all-0's placeholder.

	// forwarders {
	// 	0.0.0.0;
	// };

	//========================================================================
	// If BIND logs error messages about the root key being expired,
	// you will need to update your keys.  See https://www.isc.org/bind-keys
	//========================================================================
	dnssec-validation auto;

	listen-on-v6 { any; };
	recursion yes;
	listen-on{192.168.56.5;};
	allow-transfer {none;};
	
	forwarders {
		192.168.56.0;
	 };
	
};
```

>Edit local zone definitions file
```bash
sudo gedit named.conf.local
```
> [!IMPORTANT]
>named.conf.local
```bash
zone "ewubdserver.com" IN {
    type master;
    file "/etc/bind/db.ewubdserver.com";
};

zone "56.168.192.in-addr.arpa" IN {
    type master;
    file "/etc/bind/db.56.168.192";
};
```

>Edit forward zone file
```bash
sudo gedit db.ewubdserver.com
```
> [!IMPORTANT]
>db.ewubdserver.com
```bash
;
; BIND data file for local loopback interface
;
$TTL	604800
@	IN	SOA	ns1.ewubdserver.com. root.ewubdserver.com. (
			      2		; Serial
			 604800		; Refresh
			  86400		; Retry
			2419200		; Expire
			 604800 )	; Negative Cache TTL
;
@	IN	NS	ns1.ewubdserver.com.
ns1	IN	A	192.168.56.5
www	IN	A	192.168.56.5
ftp	IN	A	192.168.56.5
@       IN      MX      10	mail
mail    IN      A       192.168.56.5
@	IN	AAAA	::1
```


```bash
named-checkzone ewubdserver.com db.ewubdserver.com
```

>Edit reverse zone file
```bash
sudo gedit db.56.168.192
```

> [!IMPORTANT]
>db.56.168.192
```bash
;
; BIND reverse data file for local loopback interface
;
$TTL	604800
@	IN	SOA	ns1.ewubdserver.com. root.ewubdserver.com. (
			      1		; Serial
			 604800		; Refresh
			  86400		; Retry
			2419200		; Expire
			 604800 )	; Negative Cache TTL
;
@	IN	NS	ns1.ewubdserver.com.
24	IN	PTR	ns1.ewubdserver.com.
24	IN	PTR	www.ewubdserver.com.
24	IN	PTR	ftp.ewubdserver.com.
24	IN	PTR	mail.ewubdserver.com.
```

```bash
named-checkzone 56.168.192.in-addr.arpa db.56.168.192
```

>Manage the BIND service
```bash
sudo systemctl status named
```

```bash
sudo systemctl start named
```

```bash
sudo systemctl enable named
```

```bash
sudo systemctl status named
```
>Configure local resolver
```bash
sudo gedit /etc/resolv.conf
```
> [!IMPORTANT]
>resolv.conf
```bash
nameserver 192.168.56.5
```

```bash
sudo systemctl restart named
```

> [!TIP]
>Test DNS resolution
```bash
nslookup www.ewubdserver.com
```
