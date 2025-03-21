# Característiques de Seguretat en Publicació de Pàgines WEB

Arxiu docker-compose.yml per crear una imatge Docker amb sistema operatiu Linux Alpine que permeti la connexió per SSH.

1) Primer, crea un fitxer anomenat Dockerfile amb el següent contingut:

```python
FROM alpine:latest

RUN apk update && apk add openssh

# Estableix la contrasenya per a l'usuari root (canvia 'la_teva_contrasenya' per una contrasenya segura)
RUN echo "root:la_teva_contrasenya" | chpasswd

# Opcional: Permet l'inici de sessió de root amb contrasenya (no recomanat per a producció)
RUN sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
RUN sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config

EXPOSE 22

CMD ["/usr/sbin/sshd", "-D"]
```

## Explicació del Dockerfile:

```c++
FROM alpine:latest: Indica que la imatge base serà la darrera versió d'Alpine Linux.
RUN apk update && apk add openssh: Actualitza els paquets i instal·la el servidor SSH (openssh).
RUN echo "root:la_teva_contrasenya" | chpasswd: Estableix la contrasenya per a l'usuari root. Important: Substitueix la_teva_contrasenya per una contrasenya segura.
RUN sed -i ...: Aquestes dues línies modifiquen el fitxer de configuració SSH (/etc/ssh/sshd_config) per permetre l'inici de sessió de l'usuari root mitjançant contrasenya. Això no es recomana per a entorns de producció per motius de seguretat. És preferible utilitzar claus SSH.   
EXPOSE 22: Indica que el contenidor exposarà el port 22 (el port per defecte d'SSH).
CMD ["/usr/sbin/sshd", "-D"]: Executa el servidor SSH en primer pla quan s'inicia el contenidor.

````

2) Després, crea un fitxer anomenat docker-compose.yml al mateix directori amb el següent contingut:

```python
version: '3.8'
services:
  alpine-ssh:
    build: .
    ports:
      - "2222:22"
````

**Explicació del docker-compose.yml:**

```python
version: '3.8': Especifica la versió del format de Docker Compose.
services:: Defineix els serveis que es crearan.
alpine-ssh:: El nom del servei.
build: .: Indica que la imatge es construirà a partir del Dockerfile que es troba en el mateix directori.
ports:: Defineix el mapeig de ports entre la màquina amfitriona i el contenidor. "2222:22" significa que el port 2222 de la teva màquina amfitriona es redirigirà al port 22 del contenidor.
````

**Com utilitzar-ho:**

a) Guarda els dos fitxers (Dockerfile i docker-compose.yml) en el mateix directori.

b) Obre la teva terminal i navega fins a aquest directori.

c) Executa la comanda: 
```c
docker-compose up -d
````
*Això construirà la imatge Docker i iniciarà el contenidor en segon pla.*

**Com connectar-se per SSH:**

Un cop el contenidor estigui en funcionament, podràs connectar-te per SSH des de la teva màquina amfitriona utilitzant la següent comanda:

**Bash**

```python
ssh root@localhost -p 2222
````

Se't demanarà la contrasenya que vas establir al Dockerfile (en l'exemple, la_teva_contrasenya).

**Important:**

**Seguretat:** Recorda que permetre l'inici de sessió de root amb contrasenya no és la pràctica més segura per a entorns de producció. Per a una major seguretat, es recomana utilitzar claus SSH. Si vols utilitzar claus SSH, hauries de modificar el Dockerfile per copiar la teva clau pública i configurar SSH per utilitzar l'autenticació per clau.

**Contrasenya:** Assegura't de triar una contrasenya forta i única per a l'usuari root.
Aquest arxiu docker-compose.yml i el Dockerfile et proporcionen una manera senzilla de crear un contenidor Alpine Linux amb accés SSH per a proves o desenvolupament
