# the native web
**Quellen**: 

https://www.youtube.com/watch?v=DESdVoKhIxY&t=3437s

https://www.youtube.com/watch?v=3YP0eUHdLc8&t=31s

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

## Layer im Docker
Images bestehen meistens aus mehreren Layern. _FROM_-Behel im Dockerfile führt dazu, dass ein neuer Layer auf 
ein bestehenden Layer erstellt wird. z.B:

`FROM node:20.10.0-alpine`

jeder weiterer Befehl, z.B. _ADD_, _RUN_ oder _COPY_, in diesem Dockerfile führt zu weiteren Layern.

Mit den Befehlen wie _WORKDIR_ und _CMD_ werden aber kein neuer Layer erstellt, diese ändern also nur die Attribute.

Ein Image sollte derzeit max. aus **127 Layern** bestehen.

Layer werden besonders für das Caching verwendet. Damit die Layer, die sich niht verändert haben, wieder verwerdet werden können. Dieses Caching-Verfahren könnte 
durch den folgenden Befehl übersprungen werden:

`docker build --no-cache -t mein-image .`


## Multi-Stage Build

Im Kontext von Docker bezieht sich der Begriff "Multi-Stage Build" auf eine Technik, die es ermöglicht, mehrere Build-Stufen in einem Dockerfile zu definieren. Diese Technik wurde eingeführt, um Docker-Images effizienter und schlanker zu gestalten.

Ein Docker-Image ist im Wesentlichen eine ausführbare Softwarepaket, das alle Abhängigkeiten und Konfigurationen enthält, die für das Ausführen einer Anwendung erforderlich sind. Bei herkömmlichen Dockerfiles werden alle notwendigen Schritte (wie das Kompilieren von Code, das Hinzufügen von Abhängigkeiten und das Kopieren von Dateien) in einer einzigen Stufe durchgeführt. Das führt oft dazu, dass das endgültige Image größer ist, da alle Build-Artefakte und Zwischenschritte im finalen Image enthalten sind.

Die Multi-Stage-Build-Technik löst dieses Problem, indem sie erlaubt, mehrere FROM-Anweisungen in einem Dockerfile zu verwenden. Jede FROM-Anweisung definiert eine neue Build-Stufe mit einem separaten Basisimage. Das ermöglicht es, nur die notwendigen Dateien und Artefakte aus einer vorherigen Stufe in die nächste zu kopieren. Die endgültige Stufe enthält dann nur die für die Ausführung der Anwendung notwendigen Komponenten, ohne überflüssige Build-Artefakte.

Hier ist ein einfaches Beispiel für ein Dockerfile mit Multi-Stage-Build:

```
# Erste Build-Stufe
FROM node:14 as builder \ORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Zweite Build-Stufe
FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
```

In diesem Beispiel wird in der ersten Build-Stufe Node.js verwendet, um den Code zu kompilieren, und in der zweiten Stufe wird nur das kompilierte Artefakt in ein leichtgewichtiges Nginx-Image kopiert. Das Ergebnis ist ein schlankeres Docker-Image, da nur das endgültige Build-Artefakt im finalen Image enthalten ist.

Um in der ersten Stufe erstellten Image zu sehen, könnte man das Folgende verwenden:

`docker build --target builder -t mein-builder-image .`


## Starten eines Containers mit _Bind mounts_ aus HOST-System.

`docker run -d --name webserver -p 3000:80 -v [ABSOLUTES_PFAD_IN_HOST]:[ABSOLUTES_PFAD_IN_CONTAINER] nginx`

`docker run -d --name webserver -p 3000:80 -v $(pwd):[ABSOLUTES_PFAD_IN_CONTAINER] nginx`

`docker run -d --name webserver -p 3000:80 -v [ABSOLUTES_PFAD_IN_HOST]:/usr/share/nginx/html nginx`

## Volumes

**Docker-Volumes sind eigenständige Dateisysteme, die von Docker verwaltet werden**. Sie existieren unabhängig von einem bestimmten Container und können mehreren Containern gemeinsam zugewiesen werden.

* **Effiziente Datenspeicherung**: Docker-Volumes bieten eine effiziente Datenspeicherung und können auch für Backups und Datenpersistenz verwendet werden. Sie können auch in der Cloud oder über Docker-Plugins bereitgestellt werden.
* **Isolation**: Volumes können auch Daten zwischen Containern teilen, ohne dass diese direkt miteinander verbunden sind. Dies ermöglicht eine bessere Isolation von Daten.
* **Lebenszyklus**: Docker-Volumes haben einen längeren Lebenszyklus als Container und können Daten über mehrere Containerstarts und -stops hinweg speichern.

### Volumes anzeigen

`docker volume ls`

### Volume erstellen

`docker volume create myfiles`

### Erstelltes Volume verwenden

`docker run -d --name webserver -p 3000:80 -v [ERSTELLTEST_VOLUME]:[ABSOLUTES_PFAD_IN_CONTAINER] nginx`

`docker run -d --name webserver -p 3000:80 -v myfiles:[ABSOLUTES_PFAD_IN_CONTAINER] nginx`

### Volume entfernen

`docker volume rm myfiles`

### Alle Volumes enfernen, die nicht verwendet werden.

`docker volume prune`

## Virtuelle Netzwerke (Bridge, Host & Co)

Container erreichen anderen Container, wenn diese im selben Netzwerk sind.

**Netwerke auflisten**

`docker network ls`

Wenn ein Container getartet wird, wird er automatisch zum **_bridge_**-Netwerk hinzugefügt, was bedeutet, dass alle diese Container sich gegenseitig erreichen können.
Dieses Verhalten ist aber unter Umständen nicht wünschenswert.

**Ein Netzwerk erstellen (vom Type _BRIDGE_)**

`docker network create test`

**Ein Netzwerk löschem**

`docker network rm test`

**Alle Netzwerke enfernen, die nicht verwendet werden**

`docker network prune`

**Möchte man einen Container mit einem Netzwerk verbinden**

`docker network connect [NETWORK_NAME] [CONTAINER_ID]`

**Möchte man einen Container aus einem Netzwerk entfernen**

`docker network disconnect [NETWORK_NAME] [CONTAINER_ID]`

**Container direkt beim Starten mit einem Netzwerk verbinden**

`docker run --network test ...`

