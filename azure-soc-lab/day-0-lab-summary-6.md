---
icon: chart-column
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1767057510266
coverY: 0
coverHeight: 138
---

# Day 6 - ELK Setup

**Date:** 26.06.2026

**Core Concept**: Centralized Log Collection and SIEM Backend Deployment

Day 6 marks the transition from a standalone honeypot into a functional SOC pipeline. With Filebeat already installed and waiting on the Linux honeypot VM, the goal today was to stand up the Analysis Zone backend: an ELK stack that will receive, parse, and visualize every event Cowrie captures. By the end of the session, Elasticsearch was healthy, Logstash was ingesting on port 5044, and Kibana was accessible from the browser.

***

## Section 1: Provisioning the Analysis Zone VM

A dedicated VM was provisioned inside `snet-analysis` (10.0.1.0/24) to host the ELK stack. Unlike the honeypot VM, this machine sits in the secure backend zone and is never exposed to the public internet beyond a single Kibana port locked to the Home IP.

**VM Specs:**

* **Name:** `vm-elk-analysis`
* **Size:** Standard\_D2s\_v3 (2 vCPU, 8GB RAM) at $0.096/hr
* **Image:** Ubuntu 22.04 LTS
* **Storage:** 30GB Standard SSD
* **Subnet:** `snet-analysis` (10.0.1.0/24)
* **NSG:** `nsg-analysis`

The same baseline initialization script used in Day 3 was applied via the Custom Data field at creation time, handling system updates, essential tooling, and UFW configuration automatically on first boot. One addition to the tool list this time was `jq`, needed for querying the Elasticsearch REST API cleanly from the terminal.

***

## Section 2: System Prerequisites for Elasticsearch

Before installing anything, a critical kernel parameter must be configured. Elasticsearch requires `vm.max_map_count` to be at least 262144 to manage its memory-mapped files. Without this, the container refuses to start entirely.

```bash
# Apply immediately
sudo sysctl -w vm.max_map_count=262144

# Persist across reboots
echo "vm.max_map_count=262144" | sudo tee -a /etc/sysctl.conf

# Verify
sysctl vm.max_map_count
```

This is one of the most common gotchas when running ELK in Docker and is worth verifying before every session if there is any doubt it persisted.

***

## Section 3: ELK Stack Deployment via Docker

Rather than configuring Elasticsearch, Logstash, and Kibana individually from scratch, we used the community-maintained `deviantony/docker-elk` repository. It provides a production-grade, well-structured Docker Compose setup that handles service dependencies, networking, and configuration mounting cleanly.

```bash
sudo apt install docker.io docker-compose-v2 -y
sudo systemctl enable docker && sudo systemctl start docker

git clone https://github.com/deviantony/docker-elk.git
cd docker-elk
```

***

## Section 4: Configuration Adjustments

Before launching the stack, three adjustments were made to the default configuration.

**Disabling xpack Security**

For a local research lab, TLS and credential enforcement add complexity without meaningful benefit. Security was disabled in `elasticsearch/config/elasticsearch.yml`:

```yaml
xpack.security.enabled: false
xpack.security.http.ssl:
  enabled: false
xpack.security.transport.ssl:
  enabled: false
```

And xpack monitoring was disabled in `logstash/config/logstash.yml`:

```yaml
xpack.monitoring.enabled: false
```

**Tuning JVM Heap Sizes**

The D2s\_v3 has 8GB of RAM shared across three services plus the OS. The default heap allocations in `docker-compose.yml` were reduced to prevent memory contention:

```yaml
# Elasticsearch
ES_JAVA_OPTS: "-Xms512m -Xmx512m"

# Logstash
LS_JAVA_OPTS: "-Xms256m -Xmx256m"
```

**Adding the Cowrie Logstash Pipeline**

A custom pipeline configuration was created at `logstash/pipeline/cowrie.conf`. This pipeline listens for Filebeat connections on port 5044, identifies Cowrie events using the `source` field, normalizes the timestamp, and forwards parsed events to Elasticsearch under a daily index pattern:

```json
input {
  beats {
    port => 5044
  }
}

filter {
  if [source] == "cowrie_honeypot" {
    date {
      match => ["timestamp", "ISO8601"]
      target => "@timestamp"
    }
    mutate {
      add_tag => ["cowrie", "honeypot"]
      add_field => { "[@metadata][index_name]" => "cowrie-%{+YYYY.MM.dd}" }
    }
  }
}

output {
  elasticsearch {
    hosts => ["elasticsearch:9200"]
    index => "%{[@metadata][index_name]}"
  }
  stdout {
    codec => rubydebug
  }
}
```

***

## Section 5: NSG Updates

Two inbound rules were added to `nsg-analysis` before attempting to access any services:

<table><thead><tr><th width="200.5999755859375">Rule</th><th width="125.39996337890625">Port</th><th width="144.5999755859375">Source</th><th>Purpose</th></tr></thead><tbody><tr><td>Kibana Access</td><td>5601</td><td>Home IP</td><td>Browser access to Kibana UI</td></tr><tr><td>Logstash Beats</td><td>5044</td><td>10.0.2.0/24</td><td>Filebeat log ingestion from honeypot subnet</td></tr></tbody></table>

***

## Section 6: Filebeat Reconfiguration

With the ELK VM provisioned and its private IP known, the Filebeat configuration on the honeypot VM was updated to point at the correct Logstash endpoint. One important fix was adding `fields_under_root: true` to the Filebeat input block. Without this, custom fields like `source` are nested under `[fields]` in Logstash rather than being accessible at the root level, which breaks the pipeline filter logic entirely.

The updated `filebeat.yml`:

```yaml
filebeat.inputs:
- type: log
  enabled: true
  paths:
    - /home/labadmin/cowrie/var/log/cowrie/cowrie.json
  json.message_key: message
  json.keys_under_root: true
  json.add_error_key: true
  fields_under_root: true
  fields:
    source: cowrie_honeypot
    environment: lab
  tags: ["cowrie", "honeypot"]

output.logstash:
  hosts: ["10.0.1.X:5044"]
  ssl.enabled: false
```

After updating, the Filebeat service was restarted and confirmed as connecting to Logstash successfully.

***

## Section 7: Kibana YAML Fix

Kibana entered a crash loop on first start. The logs revealed a YAML syntax error in `kibana/config/kibana.yml` where two list entries had been merged onto the same line during the xpack edit:

```yaml
# Broken
  - name: system
    version: latest  - name: elastic_agent

# Fixed
  - name: system
    version: latest
  - name: elastic_agent
    version: latest
```

After correcting the indentation and restarting the container, Kibana came up cleanly.

***

## Section 8: Verification

With all services running, a quick health check confirmed the stack was operational:

```bash
# Elasticsearch cluster health
curl -s http://localhost:9200 | jq .

# Logstash port listening
ss -tulpn | grep 5044

# Container status
sudo docker compose ps
```

Elasticsearch returned cluster information. Logstash was confirmed listening on `0.0.0.0:5044`. The Kibana welcome screen was accessible from the browser on port 5601.

<figure><img src="https://cdn.discordapp.com/attachments/1498609087838158952/1520015012645703700/image.png?ex=6a3fa7cb&#x26;is=6a3e564b&#x26;hm=c66624b9744dfad3bfb5e727b1e95ed8f851b0063750a09699d3e370406f10f0" alt=""><figcaption></figcaption></figure>

***

## Conclusion

Day 6 is complete. The Analysis Zone is live with a fully operational ELK stack running via Docker. Elasticsearch is healthy, Logstash is ready to receive Filebeat events from the honeypot, and Kibana is accessible for log visualization. The full log pipeline is now structurally in place. Day 7 will focus on fine-tuning the ELK configuration and opening the honeypot VM to the public internet to begin capturing real attack telemetry.
