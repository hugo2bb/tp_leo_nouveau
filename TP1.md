

# Partie 1. Install

```                   _
                       | \
                       | |
                       | |
  |\                   | |
 /, ~\                / /
X     `-.....-------./ /
 ~-. ~  ~              |
    \             /    |
     \  /_     ___\   /
     | /\ ~~~~~   \ |
     | | \        || |
     | |\ \       || )
    (_/ (_/      ((_/

```

## **🌞 Installer Docker votre machine Azure**

```g4mberge@g4mberge-82tl:~$ sudo pacman -S docker
résolution des dépendances…
recherche des conflits entre paquets…

Paquet (3)        Nouvelle version  Changement net  Taille du téléchargement

extra/containerd  2.2.2-1                93,35 MiB                 20,78 MiB
extra/runc        1.4.1-1                 9,77 MiB                  3,15 MiB
extra/docker      1:29.3.0-1            112,14 MiB                 27,06 MiB

Taille totale du téléchargement :   50,98 MiB
Taille totale installée :          215,26 MiB

:: Procéder à l’installation ? [O/n] 



g4mberge@g4mberge-82tl:~$ sudo systemctl start docker
g4mberge@g4mberge-82tl:~$ sudo systemctl enable docker
Created symlink '/etc/systemd/system/multi-user.target.wants/docker.service' → '/usr/lib/systemd/system/docker.service'.
g4mberge@g4mberge-82tl:~$ sudo usermod -aG docker $(whoami)
```


## 🌞 Utiliser la commande docker run

```g4mberge@g4mberge-82tl:~$ docker run --name web -p 9999:80 nginx
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2026/03/20 08:01:16 [notice] 1#1: using the "epoll" event method
2026/03/20 08:01:16 [notice] 1#1: nginx/1.29.6
2026/03/20 08:01:16 [notice] 1#1: built by gcc 14.2.0 (Debian 14.2.0-19) 
2026/03/20 08:01:16 [notice] 1#1:
```

## 🌞 Rendre le service dispo sur internet

```
g4mberge@g4mberge-82tl:~$ curl http://10.100.0.222:9999/

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

<p>For online documentation and support please refer to
<a href="https://nginx.org/">nginx.org</a>.<br/>
To engage with the community please visit
<a href="https://community.nginx.org/">community.nginx.org</a>.<br/>
For enterprise grade support, professional services, additional 
security features and capabilities please refer to
<a href="https://f5.com/nginx">f5.com/nginx</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```
## 🌞 Custom un peu le lancement du conteneur


```
g4mberge@g4mberge-82tl:~$ mkdir -p ~/meow/nginx
g4mberge@g4mberge-82tl:~$ nano ~/meow/nginx/nginx.conf

server {
    listen 7777;
    location / {
        root /usr/share/nginx/html;
        index index.html;
    }
}

nano ~/meow/nginx/index.html

g4mberge@g4mberge-82tl:~$ docker run \
  --name meow \
  -p 7777:7777 \
  -m 512m \
  -v ~/meow/nginx/nginx.conf:/etc/nginx/conf.d/default.conf \
  -v ~/meow/nginx/index.html:/usr/share/nginx/html/index.html \
  nginx

http://10.100.0.222:7777/

```


# Partie 2

## Construisez votre propre Dockerfile

### 🌞 Construire votre propre image

```
g4mberge@g4mberge-82tl:~$ mkdir dockertp
g4mberge@g4mberge-82tl:~$ cd dockertp
g4mberge@g4mberge-82tl:~/dockertp$ cat dockerfile
cat: dockerfile: Aucun fichier ou dossier de ce nom
g4mberge@g4mberge-82tl:~/dockertp$ nano dockerfile
g4mberge@g4mberge-82tl:~/dockertp$ nano index.html
g4mberge@g4mberge-82tl:~/dockertp$ nano apache2.conf
g4mberge@g4mberge-82tl:~/dockertp$ nano debian




g4mberge@g4mberge-82tl:~/dockertp$ mv dockerfile Dockerfile
g4mberge@g4mberge-82tl:~/dockertp$ docker build . -t apache_prout
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
            Install the buildx component to build images with BuildKit:
            https://docs.docker.com/go/buildx/

Sending build context to Docker daemon   5.12kB
Step 1/6 : FROM debian
 ---> 55a15a112b42
Step 2/6 : RUN apt update -y
 ---> Using cache
 ---> c57f8d6151d6
Step 3/6 : RUN apt install -y apache2
 ---> Running in 7e6c80bee9bb




g4mberge@g4mberge-82tl:~/dockertp$ docker run -d -p 80:80 apache_prout
c16f09e5836f74d0fb252d63f7a9b2661b04b14a157033b2d30e8412471734f4
g4mberge@g4mberge-82tl:~/dockertp$ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS                                 NAMES
c16f09e5836f   apache_prout   "apache2 -DFOREGROUND"   6 seconds ago   Up 5 seconds   0.0.0.0:80->80/tcp, [::]:80->80/tcp   festive_feynman
g4mberge@g4mberge-82tl:~/dockertp$ curl http://localhost
<h2> coucou c l'heure du brainstorming suivi d'un brunch pour voir qui prend le lead du prochaine meet up qu'on a mit sur le linkedin afin de d'optimiser les ezbfhjqdsjhaslfjgeQKUEFKQEHF prout <h2>


```
## Part III : docker-compose

``` 
g4mberge@g4mberge-82tl:~$ nano docker-compose.yml

services:
  db:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: wiki
      POSTGRES_PASSWORD: wikijsrocks
      POSTGRES_USER: wikijs
    restart: unless-stopped
    volumes:
      - db-data:/var/lib/postgresql/data

  wiki:
    image: ghcr.io/requarks/wiki:2
    depends_on:
      - db
    environment:
      DB_TYPE: postgres
      DB_HOST: db
      DB_PORT: 5432
      DB_USER: wikijs
      DB_PASS: wikijsrocks
      DB_NAME: wiki
    restart: unless-stopped
    ports:
      - "8886:3000"

volumes:
  db-data:


g4mberge@g4mberge-82tl:~$ docker-compose up -d
[+] up 30/31
 ✔ Image ghcr.io/requarks/wiki:2 Pulled                              20.4s
 ✔ Image postgres:15-alpine      Pulled                              14.8s
 ✔ Network g4mberge_default      Created                             0.1s
 ✔ Volume g4mberge_db-data       Created                             0.0s
 ✔ Container g4mberge-db-1       Started                             1.0s
 ✔ Container g4mberge-wiki-1     Started                             0.4s
g4mberge@g4mberge-82tl:~$ docker compose ps
NAME              IMAGE                     COMMAND                  SERVICE   CREATED          STATUS         PORTS
g4mberge-db-1     postgres:15-alpine        "docker-entrypoint.s…"   db        10 seconds ago   Up 9 seconds   5432/tcp
g4mberge-wiki-1   ghcr.io/requarks/wiki:2   "docker-entrypoint.s…"   wiki      10 seconds ago   Up 9 seconds   3443/tcp, 0.0.0.0:8886->3000/tcp, [::]:8886->3000/tcp


```

## 3. Make your own meow

```
g4mberge@g4mberge-82tl:~$ mkdir ~/python_app
g4mberge@g4mberge-82tl:~$ cd ~/python_app
g4mberge@g4mberge-82tl:~/python_app$ touch Dockerfile
g4mberge@g4mberge-82tl:~/python_app$ touch docker-compose.yml
g4mberge@g4mberge-82tl:~/python_app$ touch requirements.txt
g4mberge@g4mberge-82tl:~/python_app$ touch app.py
g4mberge@g4mberge-82tl:~/python_app$ nano Dockerfile
g4mberge@g4mberge-82tl:~/python_app$ nano docker-compose.yml

DockerFile :

FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install -r requirements.txt

COPY . .

CMD ["python3", "app.py"]

docker-compose.yml :

services:
  db:
    image: redis:latest
    restart: unless-stopped

  app:
    build: .
    depends_on:
      - db
    ports:
      - "8888:8888"
    restart: unless-stopped

```

## Part IV : Docker security

```
g4mberge@g4mberge-82tl:~/python_app$ docker run -it --rm -v /:/disque_host alpine cat /disque_host/etc/shadow

root:heheheheheheheheheheheh::::::
bin:!*:20518:::::1:
daemon:!*:20518:::::1:
mail:!*:20518:::::1:
ftp:!*:20518:::::1:
http:!*:20518:::::1:
nobody:!*:20518:::::1:
dbus:!*:20518::::::
systemd-coredump:!*:20518:::::1:
systemd-network:!*:20518:::::1:
systemd-oom:!*:20518:::::1:
systemd-journal-remote:!*:20518:::::1:
systemd-resolve:!*:20518:::::1:
systemd-timesync:!*:20518:::::1:
tss:!*:20518::::::
uuidd:!*:20518:::::1:
alpm:!*:20518::::::
avahi:!*:20518::::::
named:!*:20518:::::1:
dnsmasq:!*:20518:::::1:
fwupd:!*:20518:::::1:
git:!*:20518::::::
_talkd:!*:20518::::::
nm-openconnect:!*:20518::::::
nm-openvpn:!*:20518:::::1:
ntp:!*:20518:::::1:
nvidia-persistenced:!*:20518:::::1:
openvpn:!*:20518:::::1:
partimag:!*:20518:::::1:
passim:!*:20518::::::
pcscd:!*:20518:::::1:
polkitd:!*:20518::::::
rpc:!*:20518:::::1:
rpcuser:!*:20518:::::1:
rtkit:!*:20518::::::
sddm:!*:20518::::::
g4mberge:hehehehehehehehehehehehehehehe
.4EyEEO61Fyw2RCAW3c.DUqxB:20532:0:99999:7:::
tor:!*:20538:::::1:

```

## 2. Scan de vuln
Il existe des outils dédiés au scan de vulnérabilités dans des images Docker.

C'est le cas de Trivy par exemple.
🌞 Utilisez Trivy

```
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image ghcr.io/requarks/wiki:2
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image postgres:15-alpine
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image mon_image_apache
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image nginx 
 ```

 