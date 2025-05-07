# SideQuest: Grafana & Prometheus Monitoring Demo

**Team:**

* Yanis Sebastian Zürcher
* Jason Bichsel
* Dominik Könitzer

**Datum:** 07.05.2025
**Abgabezeitpunkt:** Vor dem Start des nächsten Unterrichtsblocks (14.05.2025)

---

## 1. Zielsetzung

Im Rahmen dieser praktischen Aufgabe sollte ein vollständiges Monitoring-Setup mit Prometheus, Node Exporter und Grafana aufgebaut werden. Ziel war es, live Metriken (CPU, RAM, Disk, Netzwerk) eines Linux-Hosts zu erfassen (windows 11 hat probeleme) und in einem Grafana-Dashboard darzustellen.

---

## 2. Verwendete Technologie

* **Docker Engine** 
* **Docker Compose** 
* **Prometheus** 
* **Node Exporter** 
* **Grafana OSS**
* **GNU/Linux Kali Rolling** Container

---

## 3. Verzeichnisstruktur

```text
~/sq-grafana/
├── docker-compose.yml
├── prometheus.yml
├── datasource.png      
├── dashboard.png       
└── doc.md              
```

---

## 4. Installation und Start 

1. **Arbeitsverzeichnis anlegen**

   ```bash
   mkdir ~/sq-grafana && cd ~/sq-grafana
   ```

2. **`docker-compose.yml` erstellen**
   ```yml
    version: "3.8"

    services:
    prometheus:
        image: prom/prometheus:v2.52.0
        container_name: prometheus
        volumes:
        - ./prometheus.yml:/etc/prometheus/prometheus.yml:ro
        ports:
        - "9090:9090"
        restart: unless-stopped

    node-exporter:
        image: prom/node-exporter:v1.8.0
        container_name: node-exporter
        ports:
        - "9100:9100"
        restart: unless-stopped

    grafana:
        image: grafana/grafana-oss:11.0.0
        container_name: grafana
        environment:
        GF_SECURITY_ADMIN_USER: admin
        GF_SECURITY_ADMIN_PASSWORD: admin
        ports:
        - "3000:3000"
        depends_on:
        - prometheus
        restart: unless-stopped

   ```

3. **`prometheus.yml` anlegen**
   ```yml
    global:
    scrape_interval: 15s
    evaluation_interval: 15s

    scrape_configs:
    - job_name: 'node'
        static_configs:
        - targets:
            - 'node-exporter:9100'
   ```

4. **Stack starten**

   ```bash
   docker-compose pull
   docker-compose up -d
   docker-compose ps
   ```

---

## 5. Grafana-Konfiguration

1. **Login**

   * URL: `http://localhost:3000`
   * login: **username: admin / password: admin** (die daten die ich im docker-compose angelegt habe.)
   * pwd ändern

2. **Datenquelle hinzufügen**

   * Menü **Connections -> Data sources -> Add new data source**
   * Typ: **Prometheus**
   * URL: `http://prometheus:9090`
   * **Save & Test**
   * **Ergebnis:** grüner Banner "Successfully queried the Prometheus API"
     ![datasource](datasource.png)

3. **Dashboard importieren**

   * Menü **Dashboard -> new -> import**
   * Feld "Grafana.com dashboard URL or ID": `1860` → **Load**
   * Data Source: **prometheus** auswählen -> **Import**

---

## 6. Verifikation des Monitorings

* **Prometheus Targets**: unter `http://localhost:9090/targets` zeigt `node` als `UP`.
     ![datasource](target.png)


* **Grafana-Dashboard**:

  * CPU-Auslastung, RAM-Verbrauch, etc...

    ![dashboard](dashboard.png)

---

## 7. Fazit

Das vollständige Monitoring-Setup ließ sich binnen weniger Minuten reproduzierbar starten. Prometheus erfasst zuverlässig Systemmetriken vom Node Exporter, Grafana visualisiert diese live. Das Gesamtsystem erfüllt alle Anforderungen der praktischen Aufgabe und liefert anschauliche Dashboards.

---

**Ende der Dokumentation**
