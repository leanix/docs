# On-Premise requirements

The following requirements apply to the current available release of LeanIX.

## Hardware

* CPU >= 2 Cores (Recommended: >= 4 Cores)
*	Memory >= 8 GB (Recommended: >= 16 GB) 
*	Harddisk >= 20 GB (Recommended: >= 100 GB SSD type storage)

## Required Software

* Linux (Recommended: Ubuntu 14.04, SLES 12)
* Docker >= 1.6.2
* Docker-Compose >= 1.3.2

## Accessible URLs from host

* During installation/upgrade
  * https://index.docker.io
  * https://hub.docker.com
  * https://registry.hub.docker.com
* At runtime
  * https://github.com (use of custom reports stored at github is optional)
  * https://maps.googleapis.com/maps/api/geocode/json (optional; geocoding can be disabled at Fact Sheet level)

## Used infrastructure

* SMTP server to send mails
* optional Single Sign-on via SAML 2.0, see [SSO Setup documentation](../SSO_Setup.md)
* The LeanIX-webserver needs a SSL certificate for the (sub)domain it will be running on. If it is a self-signed certificate we also need the CA chain available on the server.

## Backup

We recommend to take snapshots of the whole VM where LeanIX is running on.

## Monitoring

We provide a Health-Check endpoint that can be accessed by your monitoring solution. The Health-Check is reachable under the following URL and should return Code 200 if everything is OK.

`https://<instance-url>/demo/api/v1/health`, e.g. https://app.leanix.net/demo/api/v1/health
