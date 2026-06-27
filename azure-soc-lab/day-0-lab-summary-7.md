---
icon: toolbox
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1767057510266
coverY: 0
coverHeight: 138
---

# Day 7 - ELK Fixes & Deployment

**Date:** 27.06.2026

**Core Concept**: Complete Log Pipeline Verification and SIEM Integration

Day 7 focused on confirming the complete flow from honeypot to analysis backend. With the ELK stack deployed in the Analysis Zone, the goal was to verify that Cowrie events were being captured by Filebeat, processed by Logstash, stored in Elasticsearch, and visualized in Kibana. By session end, the full pipeline was confirmed operational and ready for real-world attack data.

***

## Section 1: ELK Stack Deployment via docker-elk

The community-maintained `deviantony/docker-elk` repository was used as the foundation for the ELK deployment. This approach provides production-grade Docker Compose configuration, proper service dependencies, and clean configuration mounting.

**Key configuration adjustments made:**

xpack security was disabled across all three services to simplify the lab environment. In `elasticsearch/config/elasticsearch.yml`:

```yaml
xpack.security.enabled: false
xpack.security.http.ssl:
  enabled: false
xpack.security.transport.ssl:
  enabled: false
```

JVM heap sizes were tuned in `docker-compose.yml` to fit within the D2s\_v3 VM's 8GB of RAM:

```yaml
ES_JAVA_OPTS: "-Xms512m -Xmx512m"
LS_JAVA_OPTS: "-Xms256m -Xmx256m"
```

The stack was deployed with:

```bash
git clone https://github.com/deviantony/docker-elk.git
cd docker-elk
sudo docker compose up -d
```

All three services (Elasticsearch, Logstash, Kibana) came up cleanly and remained stable throughout testing.

***

## Section 2: Logstash Configuration for Cowrie Events

Rather than maintaining a separate pipeline file, all logic was consolidated into the default `logstash/pipeline/logstash.conf` to avoid port binding conflicts. The pipeline listens on port 5044 for Filebeat input, applies a conditional filter for Cowrie events, and outputs to Elasticsearch.

**Complete logstash.conf:**

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
}
```

The critical element here is the filter condition: `if [source] == "cowrie_honeypot"`. This matches events where Filebeat has set the custom field at the root level (due to `fields_under_root: true` in the Filebeat config).

***

## Section 3: Filebeat Reconfiguration

The Filebeat configuration on the honeypot VM was updated to ensure correct field paths and Logstash connectivity. A key setting was `fields_under_root: true`, which flattens custom fields to the root event level rather than nesting them under a `[fields]` object.

**Updated filebeat.yml:**

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

logging.level: info
logging.to_files: true
logging.files:
  path: /var/log/filebeat
  name: filebeat
  keepfiles: 7
  permissions: 0644
```

After deployment, Filebeat was verified as running and attempting to connect to Logstash on the correct private IP address.

***

## Section 4: Kibana Data View Creation

With events flowing into Elasticsearch, a Kibana Data View was created to define how to read the Cowrie indices.

In Kibana (port 5601):

1. Clicked **Explore on my own** from the welcome screen
2. Navigated to **Stack Management > Data Views**
3. Created a new data view:
   * **Name:** Cowrie Honeypot
   * **Index pattern:** cowrie-\*
   * **Timestamp field:** @timestamp
4. Saved the data view

The data view allows Kibana to recognize all `cowrie-YYYY.MM.DD` indices and parse their fields automatically.

***

## Section 5: End-to-End Verification

A manual SSH login was triggered from a local machine to test the complete pipeline:

```bash
ssh dbadmin@<Honeypot_Public_IP> -p 22
# password: dbpassword123
```

Within seconds, the event appeared in Elasticsearch. Verification steps:

**Check Elasticsearch Index Creation:**

```bash
curl -s http://localhost:9200/_cat/indices | grep cowrie
```

Confirmed `cowrie-2026.06.27` index was created.

Afterwards, the KQL dashboard was confirmed to be visible and active.

<figure><img src="../.gitbook/assets/image (424).png" alt=""><figcaption></figcaption></figure>

***

## Section 6: Readiness for Day 8

With the complete SIEM pipeline verified and operational, the analysis backend is ready for continuous data ingestion. Day 8 will focus on opening the honeypot VM to the public internet, allowing automated scanners and bots to begin generating real-world attack telemetry. The ELK stack is prepared to ingest, parse, and visualize this traffic at scale.

***

## Conclusion

Day 7 is complete. The entire log pipeline from honeypot to SIEM backend has been deployed and verified. Elasticsearch is indexing Cowrie events, Logstash is enriching them with consistent timestamps and metadata, and Kibana is displaying them cleanly. Real attack data is now ready to be captured and analyzed. Tomorrow, the honeypot transitions from a staging environment to a live detection target.
