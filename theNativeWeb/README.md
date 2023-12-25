# the native web
Quelle: https://www.youtube.com/watch?v=DESdVoKhIxY&t=3437s

## Prüfen, ob Docker installiert ist
`docker version`

## Ein bestimmtes Image herunterladen
`docker pull ubuntu`

Damit wird die letzte Version des Images (latest) heruntergeladen.

## Ein bestimmtes Image mit einer bestimmten Version bzw. Tag herunterladen

`docker pull ubuntu:21.10`

## Auflisten aller bestehenden Images
`docker images`

## Ausführen eines Images bzw. Erstellung eines Containers
`docker run -it ubuntu bash`

Hier ist die Anwendung _bash_ innerhalb des Images _ubuntu_ im iterativen Mode (_-it_) auszuführen.
Von einem Image kann mehrere Container erstellt werden, im Sinne von mehreren Prozessen, die 
voneinander isoliert arbeiten.

Man kann auch _bash_ einer bestimmten Ubuntu-Version starten. Wenn dieses Image nicht vorhanden ist,
wird es automatisch heruntergeladen.

`docker run -it ubuntu:21.10 bash`

_nginx_ im iterativen Mode starten. Gibt man nach Image und Version keinen Prozessnamen, startet
man damit den sogenannte _Standard-Prozess_. Die folgenden Befehle machen 
in diesem Sinne das Gleiche:

`docker run -it nginx:1.21.4`

`docker run -it nginx:1.21.4 nginx`

**_nginx_ im detach-Mode (_-d_) starten.**

`docker run -d nginx:1.21.4`

## Starten eines Containers und Vergabe eines Namen für diesen

`docker run -d --name webserver nginx:1.21.4`

## Starten eines Containers mit Port-Weiterleitung

`docker run -d --name webserver -p [PORT_HOST]:[PORT_CONTAINER] nginx`

`docker run -d --name webserver -p 3000:80 nginx`

## Starten eines Containers mit Umgebungsvariablen (Enviroment-Variables oder _-e_)

`docker run -d --name webserver -p 3000:80 -e FOO=bar nginx`

## Starten eines Containers mit Volume (_-v_) aus HOST-System.

`docker run -d --name webserver -p 3000:80 -v [ABSOLUTES_PFAD_IN_HOST]:[ABSOLUTES_PFAD_IN_CONTAINER] nginx`

`docker run -d --name webserver -p 3000:80 -v $(pwd):[ABSOLUTES_PFAD_IN_CONTAINER] nginx`

`docker run -d --name webserver -p 3000:80 -v [ABSOLUTES_PFAD_IN_HOST]:/usr/share/nginx/html nginx`

## Starten eines Containers mit der Image-ID
`docker run -d -p 3000:3000 -e MESSAGE = 'Das ist ein Test...' [IMAGE-ID]`

## Auflisten aller Containers, die gerade ausgeführt werden. 
`docker ps`

## Auflisten ALLER (_-a_) Containers, die gerade ausgeführt werden oder schon beendet wurden-
`docker ps -a`

Jeder Container, der ausgeführt wird, bekommt einen zufälligen Namen zugeordnet, es sei denn man vergibt einen bestimmten Namen mit dem _name_-Parameter.

## Auslesen der Logs eines Prozesses oder Containers
`docker logs [NAME oder ID des Containers]`

Wenn die Logs permanent ausgelesen werden müssen, also die Ausgabe
muss nicht beendet werden, dann:

`docker logs --follow [NAME oder ID des Containers]` 

oder

`docker logs -f [NAME oder ID des Containers]`

## Beenden eines Containers (Graceful Shutdown)

`docker stop [NAME des Containers]`

`docker stop webserver`

## Beenden eines Containers (NOT Graceful Shutdown)

`docker kill [NAME des Containers]`

`docker kill webserver`

## Löschen eines beendeten Containers

`docker rm [NAME des Containers]`

`docker rm webserver`

## Löschen eines Images

`docker rmi [NAME des Images]`

Ubuntu-Image mit der LATEST-Version entfernen

`docker rmi ubuntu`

Ubuntu-Image mit einer bestimmten Version entfernen

`docker rmi ubuntu:20.04`

## Komplettes System-Reinigung
`docker system prune --all --volumes`

Damit werden die folgenden entfernt:
* alle beendeten Containers.
* alle Netzwerke, die von keinem Container verwendet werden.
* alle Volumes (virtuellen Dateisysteme mit _--volumes_), die von keinem Container verwendet werden.
* alle Images mit keinem Container

## Erstellen eines Docker-Images

`docker build .`

**Punkt (.) oben zeigt den Ordner, indem die Dockerfile befindet.** 

Mit diesem Befehl wird ein Image erstellt, das keinen Namen (Repository) und keine Version-Nummer (TAG) hat. 

## Erstellen eines Docker-Images mit Name und Tag
`docker build -t ddd-docker:latest .`

## Taggen eines bestehenden Images unter einem anderen Tag (Version-Nummer)
`docker tag ddd-docker:latest ddd-docker:1.0.0`


## Pushen auf DockerHub
`docker push ddd-docker:latest`

So kann man aber nicht auf DockerHub pushen, weil es für normale User nicht möglich ist ohne **Namespace** zu pushen. Stattdessen zuerst mit Namespace taggen und dann pushen:

`docker tag ddd-docker:latest serdarayalp/ddd-docker:latest`


