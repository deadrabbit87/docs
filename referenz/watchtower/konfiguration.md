---
title: Konfiguration
layout: page
parent: Watchtower
---

# Konfigurieren von Watchtower

**Watchtower** wird üblicherweise in die Gesamtkonfiguration von SOLECTRUS integriert, d.h. die bestehende Datei `compose.yaml` ist zu erweitern.

## compose.yaml

```yaml
services:
  # ....
  watchtower:
    image: containrrr/watchtower
    environment:
      - TZ
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    command: --scope solectrus --cleanup
    restart: unless-stopped
    logging:
      options:
        max-size: 10m
        max-file: '3'
    labels:
      - com.centurylinklabs.watchtower.scope=solectrus

  dashboard:
    # ...
    labels:
      - com.centurylinklabs.watchtower.scope=solectrus

  senec-collector:
    # ...
    labels:
      - com.centurylinklabs.watchtower.scope=solectrus
  # ...
```

Wesentlich sind hier zwei Dinge:

- Watchtower wird als eigener Service definiert. Dieser Service hat Zugriff auf den Docker-Daemon, um Container zu aktualisieren. Außerdem wird (über den Parameter `scope`) festgelegt, dass nicht sämtliche Container aktualisiert werden sollen, sondern nur die mit entsprechender Kennzeichnung. Falls SOLECTRUS also auf einem Docker-Host läuft, auf dem auch ganz andere Container laufen, werden diese nicht angetastet.
- Jeder Service, der von Watchtower aktualisiert werden soll, erhält das Label `com.centurylinklabs.watchtower.scope=solectrus`. Dadurch wird Watchtower angewiesen, den zugehörigen Container aktuell zu halten.

Watchtower lässt sich ansonsten vielfältig konfigurieren. Die hier angegebene Standardkonfiguration ist für SOLECTRUS aber vollkommen ausreichen.

{: .note }

Watchtower läuft standardmäßig einmal täglich, die erste Prüfung erfolgt 24 Stunden nach dem Start. Wenn Watchtower gerade erst eingerichtet wurde, musst also für die erste Update-Prüfung einen Tag gewartet werden.
