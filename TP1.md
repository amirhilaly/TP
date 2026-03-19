hello, j'ai laissé les fichiers de confs utilisés, pas demander

## 1. Install
**🌞 Installer Docker votre machine Azure**
```
➜  ~ sudo systemctl status docker | head -n 10
● docker.service - Docker Application Container Engine
     Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled; preset: disabled)
     Active: active (running) since Thu 2026-03-19 07:57:46 CET; 1h 19min ago
 Invocation: c0a4909947be4c598807c9abc08e6de4
TriggeredBy: ● docker.socket
       Docs: https://docs.docker.com
   Main PID: 1312 (dockerd)
      Tasks: 17
     Memory: 101.5M (peak: 111.9M)
        CPU: 1.490s
➜  ~ sudo usermod -aG docker amir
[amir@bomboclat ~]$ groups
amir vboxusers docker video wheel
```

```
[amir@bomboclat ~]$ docker info | head -n 10
Client:
 Version:    29.2.1
 Context:    desktop-linux
 Debug Mode: false
 Plugins:
  agent: create or run AI agents (Docker Inc.)
    Version:  v1.29.0
    Path:     /home/amir/.docker/cli-plugins/docker-agent
  ai: Docker AI Agent - Ask Gordon (Docker Inc.)
    Version:  v1.19.0

```


**🌞 Utiliser la commande docker run**
```
➜  ~ docker run -p 9999:80 nginx
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf


[amir@bomboclat ~]$ curl localhost:9999 | head -n 15
  % Total    % Received % Xferd  Average Speed  Time    Time    Time   Current
                                 Dload  Upload  Total   Spent   Left   Speed
100    896 100    896   0      0 443.7k      0                              0
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, nginx is successfully installed and working.
Further configuration is required for the web server, reverse proxy, 
API gateway, load balancer, content cache, or other features.</p>


```

**🌞 Rendre le service dispo sur internet**
```
j'suis pas sur azure moi
```

**🌞 Custom un peu le lancement du conteneur**

```
docker run --name web --memory=512MB -d -v /home/amir/tp_docker/index.html:/var/www/tp_docker/index.html -v /home/amir/tp_docker/web_nginx.conf:/etc/nginx/conf.d/nginx.conf -p 7777:80 
```

**🌞 Call me**
```
braquer une banque, est-ce si dur?
```


## II. Images

**🌞 Construire votre propre image**

dockerfile direct ici! wooooo trop cool
```
[amir@bomboclat tp_docker]$ docker run --name web -p 7777:80 my_apache
AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 172.17.0.2. Set the 'ServerName' directive globally to suppress this message
```

d'un autre terminal:
```
[amir@bomboclat TP_DOCKER]$ curl localhost:7777
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>gros chat</title>
</head>
<body>
    si je braque une banque, ils me donnent une alternance?
</body>
```

## III. docker-compose

**🌞 Installez un WikiJS en utilisant Docker**

```
[amir@bomboclat tp_docker]$ docker compose up
Attaching to db-1, wiki-1
db-1  | 
db-1  | PostgreSQL Database directory appears to contain a database; Skipping initialization
db-1  | 
...


[amir@bomboclat tp_docker]$ docker ps
CONTAINER ID   IMAGE                     COMMAND                  CREATED         STATUS         PORTS                                     NAMES
e9517114c328   ghcr.io/requarks/wiki:2   "docker-entrypoint.s…"   7 minutes ago   Up 2 minutes   0.0.0.0:80->3000/tcp, [::]:80->3000/tcp   tp_docker-wiki-1
d75afa3c9f09   postgres:15-alpine        "docker-entrypoint.s…"   7 minutes ago   Up 2 minutes   5432/tcp                                  tp_docker-db-1
[amir@bomboclat tp_docker]$ docker exec -it tp_docker-wiki-1 bash
e9517114c328:/wiki$ ping db
PING db (172.18.0.2): 56 data bytes
64 bytes from 172.18.0.2: seq=0 ttl=42 time=0.128 ms
64 bytes from 172.18.0.2: seq=1 ttl=42 time=0.138 ms
^C
--- db ping statistics ---
2 packets transmitted, 2 packets received, 0% packet loss
round-trip min/avg/max = 0.128/0.133/0.138 ms
e9517114c328:/wiki$ 
```

**🌞 Call me when it's done**

oep

**🌞 Vous devez :**
j'ai suivi la doc de docker sur compose, ca fait exactement ca, je laisse tout dans le dossier ``python_app``

```
[amir@bomboclat TP_DOCKER]$ docker ps
CONTAINER ID   IMAGE            COMMAND                  CREATED         STATUS         PORTS                                         NAMES
67766d449469   redis:alpine     "docker-entrypoint.s…"   3 minutes ago   Up 3 minutes   6379/tcp                                      python_app-redis-1
6a89e8c63a73   python_app-web   "flask run --debug"      3 minutes ago   Up 3 minutes   0.0.0.0:8000->5000/tcp, [::]:8000->5000/tcp   python_app-web-1
[amir@bomboclat TP_DOCKER]$ curl localhost:8000
Hello from Docker! I have been seen 7 time(s).
```

## IV. Docker security

**🌞 Prouvez que vous pouvez devenir root**
```
[amir@bomboclat tp_docker]$ docker run -v /:/mnt --rm -it alpine chroot /mnt sh
Unable to find image 'alpine:latest' locally
latest: Pulling from library/alpine
9e595aac14e0: Download complete 
caa817ad3aea: Download complete 
Digest: sha256:25109184c71bdad752c8312a8623239686a9a2071e8825f20acb8f2198c3f659
Status: Downloaded newer image for alpine:latest
sh-5.2# whoami
root
sh-5.2# cat /etc/shadow | head -n 1
root:x
```

**🌞 Utilisez Trivy**

sur l'image wikijs:

```
[amir@bomboclat tp_docker]$ trivy image ghcr.io/requarks/wiki:2 | grep HIGH
2026-03-19T12:28:03+01:00       INFO    [vuln] Vulnerability scanning is enabled
2026-03-19T12:28:03+01:00       INFO    [secret] Secret scanning is enabled
2026-03-19T12:28:03+01:00       INFO    [secret] If your scanning is slow, please try '--scanners vuln' to disable secret scanning
2026-03-19T12:28:03+01:00       INFO    [secret] Please see https://trivy.dev/docs/v0.69/guide/scanner/secret#recommendation for faster secret detection
2026-03-19T12:28:03+01:00       INFO    Detected OS     family="alpine" version="3.23.3"
2026-03-19T12:28:03+01:00       INFO    [alpine] Detecting vulnerabilities...   os_version="3.23" repository="3.23" pkg_num=73
2026-03-19T12:28:03+01:00       INFO    Number of language-specific files       num=1
2026-03-19T12:28:03+01:00       INFO    [node-pkg] Detecting vulnerabilities...
2026-03-19T12:28:03+01:00       WARN    Using severities from other vendors for some vulnerabilities. Read https://trivy.dev/docs/v0.69/guide/scanner/vulnerability#severity-selection for details.
2026-03-19T12:28:03+01:00       INFO    Table result includes only package filenames. Use '--format json' option to get the full path to the package file.
Total: 7 (UNKNOWN: 0, LOW: 0, MEDIUM: 4, HIGH: 2, CRITICAL: 1)
│ gnutls   │ CVE-2026-1584  │ HIGH     │ fixed  │ 3.8.11-r0         │ 3.8.12-r0     │ gnutls: gnutls: Remote Denial of Service via crafted        │
│ zlib     │ CVE-2026-22184 │ HIGH     │        │ 1.3.1-r2          │ 1.3.2-r0      │ zlib: zlib: Arbitrary code execution via buffer overflow in │
Total: 131 (UNKNOWN: 0, LOW: 14, MEDIUM: 41, HIGH: 68, CRITICAL: 8)
│ @isaacs/brace-expansion (package.json) │ CVE-2026-25547      │ HIGH     │          │ 5.0.0             │ 5.0.1                                                   │ brace-expansion: brace-expansion: Denial of Service via      │
│ apollo-server (package.json)           │ CVE-2026-23897      │ HIGH     │ affected │ 2.25.2            │                                                         │ Apollo Serve vulnerable to Denial of Service with            │
│ axios (package.json)                   │ CVE-2025-27152      │ HIGH     │ fixed    │ 0.21.4            │ 1.8.2, 0.30.0                                           │ axios: Possible SSRF and Credential Leakage via Absolute URL │
│ body-parser (package.json)             │ CVE-2024-45590      │ HIGH     │          │ 1.20.1            │ 1.20.3                                                  │ body-parser: Denial of Service Vulnerability in body-parser  │
│ css-what (package.json)                │ CVE-2021-33587      │ HIGH     │          │ 4.0.0             │ 5.0.1                                                   │ nodejs-css-what: does not ensure that attribute parsing has  │
│ dicer (package.json)                   │ CVE-2022-24434      │ HIGH     │ affected │ 0.2.5             │                                                         │ dicer: nodejs service crash by sending a crafted payload     │
│ fast-xml-parser (package.json)         │ CVE-2026-26278      │ HIGH     │ fixed    │ 5.3.5             │ 4.5.4, 5.3.6                                            │ fast-xml-parser: fast-xml-parser: Denial of Service via      │
│ file-type (package.json)               │ CVE-2022-36313      │ HIGH     │          │ 15.0.1            │ 16.5.4, 17.1.3 
```
je cut, y'en a bcp...

sur sa db:

```
[amir@bomboclat tp_docker]$ trivy image postgres:15-alpine | grep HIGH
2026-03-19T12:29:04+01:00       INFO    [vuln] Vulnerability scanning is enabled
2026-03-19T12:29:04+01:00       INFO    [secret] Secret scanning is enabled
2026-03-19T12:29:04+01:00       INFO    [secret] If your scanning is slow, please try '--scanners vuln' to disable secret scanning
2026-03-19T12:29:04+01:00       INFO    [secret] Please see https://trivy.dev/docs/v0.69/guide/scanner/secret#recommendation for faster secret detection
2026-03-19T12:29:06+01:00       INFO    Detected OS     family="alpine" version="3.23.3"
2026-03-19T12:29:06+01:00       INFO    [alpine] Detecting vulnerabilities...   os_version="3.23" repository="3.23" pkg_num=45
2026-03-19T12:29:06+01:00       INFO    Number of language-specific files       num=1
2026-03-19T12:29:06+01:00       INFO    [gobinary] Detecting vulnerabilities...
2026-03-19T12:29:06+01:00       WARN    Using severities from other vendors for some vulnerabilities. Read https://trivy.dev/docs/v0.69/guide/scanner/vulnerability#severity-selection for details.
Total: 2 (UNKNOWN: 0, LOW: 0, MEDIUM: 1, HIGH: 1, CRITICAL: 0)
│ zlib    │ CVE-2026-22184 │ HIGH     │ fixed  │ 1.3.1-r2          │ 1.3.2-r0      │ zlib: zlib: Arbitrary code execution via buffer overflow in │
Total: 19 (UNKNOWN: 0, LOW: 1, MEDIUM: 12, HIGH: 5, CRITICAL: 1)
│         │ CVE-2025-58183 │ HIGH     │        │                   │ 1.24.8, 1.25.2               │ golang: archive/tar: Unbounded allocation when parsing GNU   │
```

sur my_apache:

```
[amir@bomboclat tp_docker]$ trivy image my_apache:latest | grep HIGH
2026-03-19T12:29:55+01:00       INFO    [vuln] Vulnerability scanning is enabled
2026-03-19T12:29:55+01:00       INFO    [secret] Secret scanning is enabled
2026-03-19T12:29:55+01:00       INFO    [secret] If your scanning is slow, please try '--scanners vuln' to disable secret scanning
2026-03-19T12:29:55+01:00       INFO    [secret] Please see https://trivy.dev/docs/v0.69/guide/scanner/secret#recommendation for faster secret detection
2026-03-19T12:29:57+01:00       INFO    Detected OS     family="ubuntu" version="24.04"
2026-03-19T12:29:57+01:00       INFO    [ubuntu] Detecting vulnerabilities...   os_version="24.04" pkg_num=135
2026-03-19T12:29:57+01:00       INFO    Number of language-specific files       num=0
Total: 13 (UNKNOWN: 0, LOW: 6, MEDIUM: 7, HIGH: 0, CRITICAL: 0)
Total: 1 (UNKNOWN: 0, LOW: 0, MEDIUM: 0, HIGH: 1, CRITICAL: 0)
HIGH: AsymmetricPrivateKey (private-key)
```

et sur nginx:

```
[amir@bomboclat tp_docker]$ trivy image nginx:latest | grep HIGH
2026-03-19T12:30:47+01:00       INFO    [vuln] Vulnerability scanning is enabled
2026-03-19T12:30:47+01:00       INFO    [secret] Secret scanning is enabled
2026-03-19T12:30:47+01:00       INFO    [secret] If your scanning is slow, please try '--scanners vuln' to disable secret scanning
2026-03-19T12:30:47+01:00       INFO    [secret] Please see https://trivy.dev/docs/v0.69/guide/scanner/secret#recommendation for faster secret detection
2026-03-19T12:30:48+01:00       INFO    [javadb] Downloading Java DB...
2026-03-19T12:30:48+01:00       INFO    [javadb] Downloading artifact...        repo="mirror.gcr.io/aquasec/trivy-java-db:1"
840.50 MiB / 840.50 MiB [------------------------------------------------------------------------------------------------------------------------------------------------------------] 100.00% 16.07 MiB p/s 53s
2026-03-19T12:31:41+01:00       INFO    [javadb] Artifact successfully downloaded       repo="mirror.gcr.io/aquasec/trivy-java-db:1"
2026-03-19T12:31:41+01:00       INFO    [javadb] Java DB is cached for 3 days. If you want to update the database more frequently, "trivy clean --java-db" command clears the DB cache.
2026-03-19T12:31:41+01:00       INFO    Detected OS     family="debian" version="13.4"
2026-03-19T12:31:41+01:00       INFO    [debian] Detecting vulnerabilities...   os_version="13" pkg_num=151
2026-03-19T12:31:42+01:00       INFO    Number of language-specific files       num=0
2026-03-19T12:31:42+01:00       WARN    Using severities from other vendors for some vulnerabilities. Read https://trivy.dev/docs/v0.69/guide/scanner/vulnerability#severity-selection for details.
Total: 156 (UNKNOWN: 2, LOW: 126, MEDIUM: 24, HIGH: 4, CRITICAL: 0)
│ libexpat1               │ CVE-2026-25210      │ HIGH     │              │ 2.7.1-2                              │                       │ libexpat: libexpat: Information disclosure and data          │
│ libheif-plugin-dav1d    │ CVE-2025-68431      │ HIGH     │              │ 1.19.8-1                             │                       │ libheif is an HEIF and AVIF file format decoder and encoder. │
│ libheif-plugin-libde265 │ CVE-2025-68431      │ HIGH     │              │                                      │                       │ libheif is an HEIF and AVIF file format decoder and encoder. │
│ libheif1                │ CVE-2025-68431      │ HIGH     │              │                                      │                       │ libheif is an HEIF and AVIF file format decoder and encoder. │
```


**🌞 Utilisez l'outil Docker Bench for Security**

oh my god j'ai eu 1 en score je crois que c'est vraiment grave