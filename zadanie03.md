PS C:\Users\kubar\breach-checker\breach-checker> docker inspect --format '{{json .Config.Labels}}' jakub-bobon-frontend:v1 jakub-bobon-frontend:latest
{"autor":"Jakub Boboń","data":"2026-11-05","maintainer":"jakub.bobon@example.com"}
{"autor":"Jakub Boboń","data":"2026-11-05","maintainer":"jakub.bobon@example.com"}
PS C:\Users\kubar\breach-checker\breach-checker> docker history jakub-bobon-frontend:v1
IMAGE          CREATED          CREATED BY                                      SIZE      COMMENT
b49f23802b5f   15 minutes ago   EXPOSE [80/tcp]                                 0B        buildkit.dockerfile.v0
<missing>      15 minutes ago   RUN /bin/sh -c htmlhint /usr/share/nginx/htm…   4.1kB     buildkit.dockerfile.v0
<missing>      15 minutes ago   COPY . /usr/share/nginx/html # buildkit         32.8kB    buildkit.dockerfile.v0
<missing>      34 minutes ago   RUN /bin/sh -c apk add --no-cache nodejs npm…   99.3MB    buildkit.dockerfile.v0
<missing>      34 minutes ago   LABEL maintainer=jakub.bobon@example.com aut…   0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      RUN /bin/sh -c set -x     && apkArch="$(cat …   52.4MB    buildkit.dockerfile.v0
<missing>      2 weeks ago      ENV ACME_VERSION=0.4.1                          0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      ENV NJS_RELEASE=1                               0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      ENV NJS_VERSION=1.0.2                           0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      CMD ["nginx" "-g" "daemon off;"]                0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      STOPSIGNAL SIGQUIT                              0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      EXPOSE map[80/tcp:{}]                           0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      ENTRYPOINT ["/docker-entrypoint.sh"]            0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      COPY 30-tune-worker-processes.sh /docker-ent…   16.4kB    buildkit.dockerfile.v0
<missing>      2 weeks ago      COPY 20-envsubst-on-templates.sh /docker-ent…   12.3kB    buildkit.dockerfile.v0
<missing>      2 weeks ago      COPY 15-local-resolvers.envsh /docker-entryp…   12.3kB    buildkit.dockerfile.v0
<missing>      2 weeks ago      COPY 10-listen-on-ipv6-by-default.sh /docker…   12.3kB    buildkit.dockerfile.v0
<missing>      2 weeks ago      COPY docker-entrypoint.sh / # buildkit          8.19kB    buildkit.dockerfile.v0
<missing>      2 weeks ago      RUN /bin/sh -c set -x     && addgroup -g 101…   5.75MB    buildkit.dockerfile.v0
<missing>      2 weeks ago      ENV DYNPKG_RELEASE=1                            0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      ENV PKG_RELEASE=1                               0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      ENV NGINX_VERSION=1.31.8                        0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      LABEL maintainer=NGINX Docker Maintainers <d…   0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      CMD ["/bin/sh"]                                 0B        buildkit.dockerfile.v0
<missing>      2 weeks ago      ADD alpine-minirootfs-3.24.3-x86_64.tar.gz /…   9.08MB    buildkit.dockerfile.v0

1. Wszystkie warstwy 2 weeks ago są z obrazu bazowego.
2. Niektóre mają 0B, bo nie tworzą nic, tylko modyfikują dane.
3. Moja wartość nadpisała wartość bazową.

Thu Nov  5 15:24:59 UTC 2026
Thu Nov  5 15:24:59 UTC 2026

PS C:\Users\kubar\breach-checker\breach-checker> docker ps
CONTAINER ID   IMAGE                            COMMAND                  CREATED         STATUS         PORTS                                     NAMES
06de03a9d079   breach-checker-frontend:latest   "/docker-entrypoint.…"   2 minutes ago   Up 2 minutes   0.0.0.0:8082->80/tcp, [::]:8082->80/tcp   test-c3
0fa47649fd65   breach-checker-frontend:v1       "/docker-entrypoint.…"   2 minutes ago   Up 2 minutes   0.0.0.0:8081->80/tcp, [::]:8081->80/tcp   test-c2

1. Czas jest taki sam, bo RUN wykonał się raz z Dockerfile.

root   /usr/share/nginx/html

1. Ponieważ dyrektywa root w domyślnej konfiguracji Nginx wskazuje ten katalog jako główny folder

PS C:\Users\kubar\breach-checker\breach-checker> docker exec bad-frontend ls /usr/share/nginx
html
index.html
script.js
style.css
PS C:\Users\kubar\breach-checker\breach-checker> docker exec bad-frontend ls /usr/share/nginx/html
50x.html
build.txt
index.html

1. Sprawdził domyślny plik powitalny Nginxa z obrazu bazowego, ponieważ pliki projektu trafiły o folder za wysoko. Build przeszedł, bo ten domyślny plik istniał i nie zawierał błędów składniowych.

PS C:\Users\kubar\breach-checker\breach-checker> docker rmi breach-checker-frontend:latest
Untagged: breach-checker-frontend:latest
PS C:\Users\kubar\breach-checker\breach-checker> docker images breach-checker-frontend
                                                               i Info →   U  In Use
IMAGE                        ID             DISK USAGE   CONTENT SIZE   EXTRA
breach-checker-frontend:v1   fb21299b87ae        225MB         58.2MB    U
breach-checker-frontend:v2   310ebe04417f       93.6MB         26.3MB    U
breach-checker-frontend:v3   9c3a83658933        225MB         58.2MB
PS C:\Users\kubar\breach-checker\breach-checker> docker rm -f test-c1 test-c2 bad-frontend
test-c1
test-c2
bad-frontend
PS C:\Users\kubar\breach-checker\breach-checker> docker rmi breach-checker-frontend:v2
Untagged: breach-checker-frontend:v2
Deleted: sha256:310ebe04417f63a563df1172a4e48ea36f4c408d86da3c679cb601a22ed11fc4

1. Jak kontener nie ma więcej tagów.