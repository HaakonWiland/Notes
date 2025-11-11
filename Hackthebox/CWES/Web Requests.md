#hackthebox #CWES #web #networking #HTTP #HTTPS

Lingo:
- Fully Qualified Domain Name (FQDN)
- Uniform Resource Locator (URL)


### HTTP protocol 
- Webserver defined the port which http communicates through (default 80)
- Our browser stores records of DNS resolutions in /etc/hosts (unix), such that it dont need to contact the DNS servers with each request. 
![[Pasted image 20251104172823.png]]

- HTTP version 1.X: Requests as clear-text, new-line characters to separate different fields 
- HTTP version 2.X: Requests as binary data, in dictionary form. 

#### HTTP Headers:
- General 
![[Pasted image 20251108112259.png]]

- Entity
![[Pasted image 20251108112709.png]]

- Request (https://datatracker.ietf.org/doc/html/rfc7231#section-5)
![[Pasted image 20251108113014.png]]
- Response 
![[Pasted image 20251108113135.png]]

- Security 
![[Pasted image 20251108113515.png]]

- More details: https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers 

#### HTTP methods: 
- https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Methods 

**Get** 
- Parameters in the url 

**Post:** 
- Parameters in the request body 
- Ex. with curl `curl -X POST -d '{"search":"london"}' -b 'PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1' -H 'Content-Type: application/json' http://<SERVER_IP>:<PORT>/search.php`
- We spesify the data: `-d`, the cookie: `-b` and the content type. 


##### HTTPS:
- Encrypted version of http: Data is encrypted, but the url visited may still be visible (Can use encrypted DNS server or vpn to hide this traffic)
- Web servers usually redirects http requests into https requests(should) 


### Url:
![[Pasted image 20251104172357.png]]

### cURL(client URL) tool:
- `curl --help all` or `curl --help <category>` or `man curl`
- Handles https communication automatically, if the certification is invalid then curl would not proceed communicating with it. (Can ignore cert validation with `-k`)
- `-i`: Only send head request and response 

### Crud API:
- Create, Read, Upload and Delete