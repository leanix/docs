# On-Premise requirements

The following requirements apply to the current available release of leanIX.

## Hardware

* CPU >= 4 cores
*	Memory >= 16 GB
*	Harddisk >= 100 GB, preferred SSD type storage

## Required Software

* Docker >= 1.6.2
* Docker-Compose >= 1.3.2

## Accessible URLs

* https://index.docker.io
* https://hub.docker.com
* https://registry.hub.docker.com
* https://github.com
* https://maps.googleapis.com/maps/api/geocode/json (optional; Zugriff kann durch Ausblenden der jeweiligen Kategorie in den Fact Sheets ausgeschaltet werden.)

## Health Check

The Health-Check is reachable under the following URL and should return Code 200 if everything is OK.

https://<instance-url>/demo/api/v1/health, e.g. https://app.leanix.net/demo/api/v1/health

