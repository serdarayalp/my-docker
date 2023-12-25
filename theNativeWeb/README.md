# the native web
Quelle: https://www.youtube.com/watch?v=DESdVoKhIxY&t=3437s

## Prüfen, ob Docker installiert ist
`docker version`

## Ein bestimmtes Image herunterladen
[sudo] docker pull [Name des Images]

`sudo docker pull ubuntu`

Damit wird die letzte Version des Images (latest) heruntergeladen.

## Ein bestimmtes Image mit einer bestimmten Version bzw. Tag herunterladen

`sudo docker pull ubuntu:21.10`

## Auflisten aller bestehenden Images
`sudo docker images`

## Ausführen eines Images bzw. Erstellung eines Containers
`sudo docker run -it ubuntu bash`

Hier ist die Anwendung _bash_ innerhalb des Images _ubuntu_ im iterativen Mode (_-it_) auszuführen.
Von einem Image kann mehrere Container erstellt werden, im Sinne von mehreren Prozessen, die 
voneinander isoliert arbeiten.

Man kann auch bash einer bestimmten Ubuntu-Version starten. Wenn dieses Image nicht vorhanden ist,
wird es automatisch heruntergeladen.

`sudo docker run -it ubuntu:21.10 bash`

nginx im iterativen Mode starten. Gibt man nach Image und Version keinen Prozessnamen, startet
man damit den sogenannte _Standard-Prozess_. Die folgenden Befehle machen 
in diesem Sinne das Gleiche:

`sudo docker run -it nginx:1.21.4`

`sudo docker run -it nginx:1.21.4 nginx`

**nginx im detach-Mode (_-d_) starten.**

`sudo docker run -d nginx:1.21.4`

## Starten eines Containers und Vergabe eines Namen für diesen

`sudo docker run -d --name webserver nginx:1.21.4`

## Starten eines Containers mit Port-Weiterleitung

`sudo docker run -d --name webserver -p [PORT_HOST]:[PORT_CONTAINER] nginx`

`sudo docker run -d --name webserver -p 3000:80 nginx`

## Starten eines Containers mit Umgebungsvariablen (Enviroment-Variables oder _-e_)

`sudo docker run -d --name webserver -p 3000:80 -e FOO=bar nginx`

## Starten eines Containers mit Volume (_-v_) aus HOST-System.

`sudo docker run -d --name webserver -p 3000:80 -v [ABSOLUTES_PFAD_IN_HOST]:[ABSOLUTES_PFAD_IN_CONTAINER] nginx`

`sudo docker run -d --name webserver -p 3000:80 -v $(pwd):[ABSOLUTES_PFAD_IN_CONTAINER] nginx`

`sudo docker run -d --name webserver -p 3000:80 -v [ABSOLUTES_PFAD_IN_HOST]:/usr/share/nginx/html nginx`

## Starten eines Containers mit der Image-ID
`sudo docker run -d -p 3000:3000 -e MESSAGE = 'Das ist ein Test...' [IMAGE-ID]`

## Auflisten aller Containers, die gerade ausgeführt werden. 
`sudo docker ps`

## Auflisten ALLER (_-a_) Containers, die gerade ausgeführt werden oder schon beendet wurden-
`sudo docker ps -a`

Jeder Container, der ausgeführt wird, bekommt einen zufälligen Namen zugeordnet.

## Auslesen der Logs eines Prozesses oder Containers
`sudo docker logs [NAME oder ID des Containers]`

Wenn die Logs permanent ausgelesen werden müssen, also die Ausgabe
muss nicht beendet werden, dann:

`sudo docker logs --follow [NAME oder ID des Containers]` 

oder

`sudo docker logs -f [NAME oder ID des Containers]`

## Beenden eines Containers (Graceful Shutdown)

`sudo docker stop [NAME des Containers]`

`sudo docker stop webserver`

## Beenden eines Containers (NOT Graceful Shutdown)

`sudo docker kill [NAME des Containers]`

`sudo docker kill webserver`

## Löschen eines beendeten Containers

`sudo docker rm [NAME des Containers]`

`sudo docker rm webserver`

## Löschen eines Images

`sudo docker rmi [NAME des Images]`

Ubuntu-Image mit der LATEST-Version entfernen

`sudo docker rmi ubuntu`

Ubuntu-Image mit einer bestimmten Version entfernen

`sudo docker rmi ubuntu:20.04`

## Komplettes System-Reinigung
`sudo docker system prune --all --volumes`

Damit werden die folgenden entfernt:
* alle beendeten Containers.
* alle Netzwerke, die von keinem Container verwendet werden.
* alle Volumes (virtuellen Dateisysteme mit _--volumes_), die von keinem Container verwendet werden.
* alle Images mit keinem Container

## Erstellen eines Docker-Images

`sudo docker build .`

**Punkt (.) oben zeigt den Ordner, indem die Dockerfile befindet.** 

Mit diesem Befehl wird ein Image erstellt, das keinen Namen (Repository) und keine Version-Nummer (TAG) hat. 

## Erstellen eines Docker-Images mit Name und Tag
`sudo docker build -t ddd-docker:latest .`

## Taggen eines bestehenden Images unter einem anderen Tag (Version-Nummer)
`sudo docker tag ddd-docker:latest ddd-docker:1.0.0`


## Pushen auf DockerHub
`sudo docker push ddd-docker:latest`

So kann man aber nicht aus DockerHub pushen, weil es für normale User nicht möglich ist ohne **Namespace** zu pushen. Stattdessen zuerst mit Namespace taggen und dann pushen:

`sudo docker tag ddd-docker:latest serdarayalp/ddd-docker:latest`


