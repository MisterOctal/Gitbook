---
icon: hammer
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1767057510266
coverY: 0
coverHeight: 138
---

# Day 5 - Honeypot Automation

**Date:** 13.06.2026

**Core Concept**: Automated Deception Maintenance and Telemetry Collection

Today marks the transition from a static honeypot to a living, breathing collection system. We automated the fake filesystem refresh to run daily, ensuring our trap remains believable over time. More importantly, we deployed Filebeat as the first link in our log pipeline, beginning the journey from raw event capture to centralized analysis.

***

## Section 1: Automating Honeypot Personality Refresh

To ensure our cloaking remains effective without manual intervention, we integrated the `fake_log_generator.py` script into the system cron scheduler.

**Set Execute Permissions:**

```bash
chmod +x /home/labadmin/cowrie/fake_log_generator.py
```

**Verify Python Shebang:** Ensure the script begins with:

```python
#!/usr/bin/env python3
```

**Open Crontab Editor:**

```bash
crontab -e
```

**Add Daily Refresh Job:** Insert the following line to execute the generator at 2:00 AM daily:

```bash
0 2 * * * /home/labadmin/cowrie/fake_log_generator.py >> /var/log/cowrie-cloak.log 2>&1
```

**Verify Installation:**

```bash
crontab -l
```

The output should display the new scheduled job. Before relying on automation, execute the script directly to confirm it still generates the expected filesystem structure:

```bash
/home/labadmin/cowrie/fake_log_generator.py
```

Check the output directory:

```bash
ls -la ~/cowrie/honeyfs/etc/
cat ~/cowrie/honeyfs/var/log/auth.log | head -20
```

***

## Section 2: Installing Filebeat on the Linux Honeypot

With the honeypot persona refreshing automatically, we now deploy Filebeat to ship Cowrie event logs upstream to our centralized ELK stack.

**Download Filebeat:**

```bash
cd /tmp
curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-8.11.0-linux-x86_64.tar.gz
```

**Extract and Install:**

```bash
tar xzf filebeat-8.11.0-linux-x86_64.tar.gz
sudo mv filebeat-8.11.0-linux-x86_64 /opt/filebeat
cd /opt/filebeat
```

**Create System User for Filebeat:**

```bash
sudo useradd --system --home /var/lib/filebeat --shell /bin/false filebeat
```

**Set Ownership and Permissions:**

```bash
sudo chown -R filebeat:filebeat /opt/filebeat
sudo chmod -R 755 /opt/filebeat
mkdir -p /var/lib/filebeat
sudo chown filebeat:filebeat /var/lib/filebeat
```

***

## Section 3: Filebeat Configuration for Cowrie JSON Logs

We configure Filebeat to parse Cowrie's JSON event format and prepare it for Logstash ingestion.

**Create Custom Filebeat Config:** Create or replace `/opt/filebeat/filebeat.yml`:

```yaml
filebeat.inputs:
- type: log
  enabled: true
  paths:
    - /home/labadmin/cowrie/var/log/cowrie/cowrie.json
  json.message_key: message
  json.keys_under_root: true
  json.add_error_key: true
  fields:
    source: cowrie_honeypot
    environment: lab
  tags: ["cowrie", "honeypot"]

output.logstash:
  hosts: ["10.0.1.4:5044"]
  ssl.enabled: false

logging.level: info
logging.to_files: true
logging.files:
  path: /var/log/filebeat
  name: filebeat
  keepfiles: 7
  permissions: 0644
```

**Key Configuration Notes:**

* `paths:` Points to the Cowrie JSON event log file
* `json.keys_under_root: true` Flattens the JSON structure for easier parsing downstream
* `output.logstash:` Targets our ELK Analysis Zone server (IP `10.0.1.4` will be assigned tomorrow)
* `tags:` Adds metadata for filtering in Kibana

**Create Filebeat Directories:**

```bash
sudo mkdir -p /var/log/filebeat
sudo chown filebeat:filebeat /var/log/filebeat
```

**Test Configuration Syntax:**

```bash
cd /opt/filebeat
sudo -u filebeat ./filebeat test config -c filebeat.yml
```

Expected output: `Config OK`

***

## Section 4: Systemd Service Integration

To ensure Filebeat survives reboots and runs with proper isolation, we create a systemd service unit.

**Create Service File:**

```bash
sudo tee /etc/systemd/system/filebeat.service > /dev/null <<EOF
[Unit]
Description=Filebeat sends log files to Logstash and Elasticsearch.
Documentation=https://www.elastic.co/beats/filebeat
Wants=network-online.target
After=network-online.target

[Service]
Type=simple
User=filebeat
Group=filebeat
EnvironmentFile=-/etc/default/filebeat
ExecStart=/opt/filebeat/filebeat -c /opt/filebeat/filebeat.yml -path.home /opt/filebeat -path.config /opt/filebeat -path.data /var/lib/filebeat -path.logs /var/log/filebeat
Restart=on-failure
RestartSec=5s

[Install]
WantedBy=multi-user.target
EOF
```

**Enable and Start Filebeat:**

```bash
sudo systemctl daemon-reload
sudo systemctl enable filebeat
sudo systemctl start filebeat
```

**Verify Service Status:**

```bash
sudo systemctl status filebeat
sudo journalctl -u filebeat -n 20 -f
```

Expected behavior: Service should report `active (running)`. Logs will show connection attempts to `10.0.1.4:5044` (expected to fail until ELK is deployed).

***

## Section 5: Firewall and Network Verification

Before moving to ELK deployment, we verify the network path is open for log shipping.

**Confirm Outbound Port 5044 is Allowed:**

```bash
sudo ufw status
```

Filebeat's outbound traffic should already be allowed by the default `ufw default allow outgoing` rule from Day 3.

**Test UDP/TCP Connectivity to Future ELK Server (Optional):**

```bash
nc -zv 10.0.1.4 5044
```

This will fail with a connection refused (expected, since the ELK server doesn't exist yet), but confirms the routing path is open.

***

## Conclusion

Day 5 is complete. Our honeypot personality now refreshes automatically every 24 hours, and Filebeat is actively running, attempting to ship logs upstream. Although the Logstash endpoint doesn't exist yet, the infrastructure is in place. Tomorrow, we move into Day 6: ELK Stack Deployment, where we will provision the Analysis Zone server, install Elasticsearch, Logstash, and Kibana, and activate the complete log pipeline.
