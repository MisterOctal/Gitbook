---
icon: hammer
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/616945d482ef350052080da1-1773109526855
coverY: 0
coverHeight: 138
---

# Elastic: Setting up a SOC Lab

**Date:** 13.05.2026&#x20;

**Room Category:** Walkthrough&#x20;

**Core Concept:** Building a centralized Security Operations Center lab using the Elastic Stack, including Elasticsearch, Kibana, and Fleet Server for log ingestion and analysis.

In a modern Security Operations Center, detection starts with data. Raw logs alone provide limited value without the ability to centralize, search, and visualize them at scale. The Elastic Stack enables security teams to ingest massive volumes of data and transform them into actionable insights. This room explores Elastic's core architecture. It provides hands-on experience building a lab to ingest, search, and investigate log data from multiple sources.

***

## Task 1: Introduction

The learning objectives for this module cover the foundational skills needed to operate a modern SIEM.

The goals include understanding the core components of the Elastic Stack and getting a deployment up and running.

This requires learning how to install the stack, configure it, and ingest log data from multiple sources. Finally, the data must be parsed and visualized by building dashboards to analyze it effectively.

Question: I understand the learning objectives and am ready to build a SOC lab with Elastic!

> **Answer:** No answer needed

***

## Task 2: Elastic Stack Architecture

The Elastic Stack is a search and analysis platform designed to help analysts manage data from a wide variety of sources. It forms the foundation of a modern Security Information and Event Management (SIEM) solution.

Before setting up the lab environment, it is crucial to understand how the components of the Elastic Stack fit together.

<figure><img src="../.gitbook/assets/image (361).png" alt=""><figcaption></figcaption></figure>

**Elasticsearch** is the core search engine and data store of the Elastic Stack. It indexes and stores data that enables quick searching, filtering, and aggregation. In a SOC context, Elasticsearch stores logs, alerts, and security telemetry. It listens on TCP port 9200 for HTTP-based API access.

**Kibana** is the graphical user interface of the Elastic Stack. It acts as the visual interface of the stack, allowing analysts to view log data, build dashboards, create visualizations, and investigate security events. Kibana is accessed over port 5601 using a web browser. It does not store data itself but communicates with Elasticsearch over port 9200.

**Elastic Agents** are lightweight host-based services installed on individual systems to collect and forward data to Elastic. They serve as the modern replacement for individual Beats. Elastic Agent provides a single unified service to gather logs, metrics, and security data.

**Fleet Server** is the centralized management interface for Elastic Agents, accessible through Kibana. It allows for the deployment of agents, assignment of policies, configuration of integrations, and management of data collection across many hosts. Fleet Server listens on a configurable TCP port, commonly 8220.

Question: Which component of the Elastic Stack represents the interface analysts use to visualize log data?

> **Answer:** Kibana

Question: Which Elastic component would you use to manage the agents deployed across your client's infrastructure?

> **Answer:** Fleet Server

***

## Task 3: Deploying Elasticsearch and Kibana

With a solid understanding of the components, the deployment begins by installing Elasticsearch and Kibana on a Linux machine. The `.deb` installers are located in the `/home/ubuntu/Downloads/elastic` directory.

First, switch to the root user, navigate to the directory, and install Elasticsearch using the `dpkg` command.

During installation, Elastic generates a password for the built-in superuser. It is important to save this password.

```bash
sudo su
cd Downloads/elastic
dpkg -i elasticsearch.deb
```

Because the Elastic Stack is running on a virtual machine with limited resources, Elasticsearch's memory usage must be restricted.

Create a configuration file at `/etc/elasticsearch/jvm.options.d/heap.options` and add the following two lines:

```bash
-Xms1g
-Xmx1g
```

Next, start and enable Elasticsearch to ensure it runs efficiently upon boot:

```bash
systemctl start elasticsearch
systemctl enable elasticsearch
systemctl status elasticsearch
```

Then, install Kibana using its `.deb` package:

```bash
dpkg -i kibana.deb
```

Before starting Kibana, make two additions to its configuration file at `/etc/kibana/kibana.yml`. This will assist with the Fleet server installation later:

```bash
xpack.encryptedSavedObjects.encryptionKey: "soc-lab-training-key-32chars-long!"
xpack.fleet.registryUrl: "http://localhost:8081"
```

Start, enable, and check Kibana's status:

```bash
systemctl start kibana
systemctl enable kibana
systemctl status kibana
```

By default, Kibana binds to localhost on port 5601. Open the browser and navigate to `http://localhost:5601/`.

To complete the setup, an enrollment token and a verification code must be generated from the terminal:

```bash
/usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s kibana
/usr/share/kibana/bin/kibana-verification-code
```

Enter the enrollment token in the browser, click Configure Elastic, and enter the verification code.

<figure><img src="../.gitbook/assets/image (363).png" alt=""><figcaption></figcaption></figure>

Finally, log in using the "elastic" username and the password generated during the initial installation. The Kibana home page will then load.

<figure><img src="../.gitbook/assets/image (364).png" alt=""><figcaption></figcaption></figure>

Question: Which port does the Kibana web interface run on by default?

> **Answer:** 5601

Question: Access the Kibana menu in the top left of the Kibana home page. What is the name of the first section that is listed?

> **Answer:** Analytics

***

## Task 4: Deploying Fleet Server and Elastic Agent

Now that the core components are installed and ready, the next step is installing an Elastic Agent and managing it using Fleet Server. Both the Fleet Server and Elastic Agent will run on the same Linux virtual machine.

<figure><img src="../.gitbook/assets/image (365).png" alt=""><figcaption></figcaption></figure>

From the Kibana home page, navigate to the top left menu, scroll down to Management, and select Fleet. From the Fleet home page, click Add Fleet Server.

In the configuration menu, enter the name `fleet-server` and use the virtual machine IP for the URL: `https://MACHINE_IP:8220`.

<figure><img src="../.gitbook/assets/image (366).png" alt="" width="296"><figcaption></figcaption></figure>

Click Generate Fleet Server policy. Elastic will generate a command to run in the terminal to install the Fleet Server.

Under the "Install Fleet Server to a centralized host" section, select Linux x86\_64 and copy the command starting at `./elastic-agent`.

<figure><img src="../.gitbook/assets/image (367).png" alt="" width="475"><figcaption></figcaption></figure>

Add the `--insecure` flag to the end of the command because this lab uses self-signed TLS certificates.

Run the command as root from the `/home/ubuntu/Downloads/elastic/elasticagent` directory:

```bash
./elastic-agent install \
  --fleet-server-es=https://MACHINE_IP:9200 \
  --fleet-server-service-token=<my_token> \
  --fleet-server-policy=fleet-server-policy \
  --fleet-server-es-ca-trusted-fingerprint=<my_fingerprint> \
  --fleet-server-port=8220 \
  --install-servers \
  --insecure
```

After confirming the installation prompt, the agent will be successfully installed.

<figure><img src="../.gitbook/assets/image (368).png" alt=""><figcaption></figcaption></figure>

Go back to Kibana and refresh the page. The newly installed Fleet Server and Elastic Agent should show as connected and healthy.

Checking the Fleet Server Policy will reveal that the agent has two default integrations: Fleet Server and System. The System integration collects host-level telemetry like syslog, authentication logs, CPU, and memory metrics.

<figure><img src="../.gitbook/assets/image (369).png" alt=""><figcaption></figcaption></figure>

To confirm log ingestion, open Discover from the top-left menu, select the `logs-*` Data view, and the incoming Linux logs will be visible.

<figure><img src="../.gitbook/assets/image (370).png" alt=""><figcaption></figcaption></figure>

Question: What is the name of the default integration used to collect system metrics and log data from your host?

> **Answer:** System

Question: In your VM terminal, create a new user with useradd testuser. In Discover, enter the query process.name: "useradd" to highlight the useradd events. What is the event.dataset field value of the associated logs?

> **Answer:** system.auth

Question: Add testuser to the sudoers group with gpasswd -a testuser sudo. Enter the query process.name: "gpasswd" to highlight the log. What is the full message field value for the event created?

> **Answer:** user testuser added by root to group sudo

***

## Task 5: Integrating Apache Web Logs

The next objective is adding web logs to the Elastic environment. The target is a simulated boutique shop named TryHatMe, which hosts its storefront on port 8080.

<figure><img src="../.gitbook/assets/image (371).png" alt=""><figcaption></figcaption></figure>

Visit `http://MACHINE_IP:8080` and click around the shop to generate traffic.

View the access logs generated by the Apache web server directly in the terminal to see what the server recorded:

```bash
tail /var/log/apache2/access.log
```

To send these logs to Elastic for easier analysis, navigate to Management > Integrations in Kibana.

<figure><img src="../.gitbook/assets/image (372).png" alt=""><figcaption></figcaption></figure>

Search for "Apache HTTP Server", select it, and click Add Apache HTTP Server.

<figure><img src="../.gitbook/assets/image (373).png" alt=""><figcaption></figcaption></figure>

Keep the default name, select "Existing hosts", and ensure the "Fleet Server Policy" is selected. Then click Save and Continue followed by Save and deploy changes.

<figure><img src="../.gitbook/assets/image (374).png" alt=""><figcaption></figcaption></figure>

To confirm the integration worked, head back to Discover, select the `logs-*` Data view, and search for `event.module: "apache"`. The web logs should now be successfully flowing into Elasticsearch.

<figure><img src="../.gitbook/assets/image (375).png" alt=""><figcaption></figcaption></figure>

Question: What is the event.dataset field value for the Apache access logs?

> **Answer:** apache.access

Question: Head to /secret.html on the TryHatMe site. Using Discover, investigate the user\_agent.original field from the recent requests. What is the hidden flag value?

> **Answer:** THM{access\_log\_secrets!}

***

## Task 6: Managing Custom Log Types

Built-in integrations handle collection and parsing automatically, but custom log sources require a different approach. This task involves ingesting logs from a custom VPN solution.

<figure><img src="../.gitbook/assets/image (376).png" alt="" width="503"><figcaption></figcaption></figure>

Generate simulated VPN log data by running a provided python script as root:

```bash
python3 /home/ubuntu/Downloads/scripts/vpnlog.py
tail /var/log/vpnlog
```

Before bringing these logs into Elastic, an ingest pipeline is required. This tells Elasticsearch how to parse the custom log lines and extract structured fields.

Go to Management > Stack Management > Ingest Pipelines and click Create pipeline > New pipeline.

<figure><img src="../.gitbook/assets/image (377).png" alt=""><figcaption></figcaption></figure>

Name the pipeline `vpn.logs.pipeline` and add the following two processors.

<figure><img src="../.gitbook/assets/image (378).png" alt=""><figcaption></figcaption></figure>

**Grok Processor:** Extracts multiple fields from the raw message.

* Field: `message`
* Pattern: `%{TIMESTAMP_ISO8601:event.time_string} %{WORD:event.action} %{USER:user.name} %{IP:source.ip} %{IP:vpn.client.ip} %{NOTSPACE:vpn.server.region}`

**Date Processor:** Converts the parsed time string into the native timestamp field.

* Field: `event.time_string`
* Formats: `ISO8601`
* Target field: `@timestamp`

<figure><img src="../.gitbook/assets/image (380).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (383).png" alt=""><figcaption></figcaption></figure>

Once the pipeline is created, navigate back to Management > Integrations and search for "Custom Logs (Filestream)".

<figure><img src="../.gitbook/assets/image (384).png" alt=""><figcaption></figcaption></figure>

Add the integration, keep the default name, and make the following changes under "Change defaults":

* Log path: `/var/log/vpnlog`
* Ingest pipeline: `vpn.logs.pipeline`

<figure><img src="../.gitbook/assets/image (385).png" alt="" width="364"><figcaption></figcaption></figure>

Apply it to existing hosts under the Fleet Server Policy and deploy the changes.

In Discover, set the query to `event.module: "filestream"` and adjust the time to the Last 24 hours.

Add the newly parsed fields (`event.action`, `user.name`, `source.ip`, `vpn.client.ip`, `vpn.server.region`) as columns in the table and save the search session as "VPN Logs".

<figure><img src="../.gitbook/assets/image (386).png" alt=""><figcaption></figcaption></figure>

Question: Investigate the newly ingested VPN log data. Who is the most active user on the network?

> **Answer:** s.summer

Question: What is the source.ip of the user you identified in the previous question?

> **Answer:** 72.14.24.1

***

## Task 7: Creating Dashboards and Visualizations

Dashboards are a core tool used by Security Operations Centers to monitor activity, identify anomalies, and support investigations. Using the newly ingested VPN logs, a monitoring dashboard can be created in Elastic.

From the Kibana sidebar, select Dashboards and click Create dashboard.

<figure><img src="../.gitbook/assets/image (387).png" alt=""><figcaption></figcaption></figure>

Click Add from library and import the "VPN Logs" saved search created in the previous task. This provides a clean overview of the VPN log data.

<figure><img src="../.gitbook/assets/image (388).png" alt=""><figcaption></figcaption></figure>

To highlight patterns more effectively, visualizations are needed. Click Add then Visualization to focus on the `event.action` field:

* Query: `event.module: "filestream"`
* Chart type: Pie
* Slice by: `event.action`
* Metric: Count

<figure><img src="../.gitbook/assets/image (389).png" alt=""><figcaption></figcaption></figure>

To see the connections as they occurred over time, create a second visualization:

* Chart type: Line
* Horizontal axis: `@timestamp`
* Breakdown: `event.action`

<figure><img src="../.gitbook/assets/image (390).png" alt=""><figcaption></figcaption></figure>

These flexible visualizations allow analysts to drag and drop searches and layout components to best support investigation and monitoring.

Question: Create a new Pie chart visualization using the search event.action: "auth\_fail". In Slice by, add the user.name field and set the Metric to Count. What percent of auth\_fail events does the user p.mallow account for? (e.g., 82.7%)

> **Answer:** 62.5%

Question: Using your dashboard, experiment with creating a visualization for the vpn.server.region field. Which field value represents the least accessed vpn.server.region?

> **Answer:** us-west-1

Question: Investigate the vpn.client.ip field. Which vpn.client.ip was only logged 26 times within the available data?

> **Answer:** 10.10.10.116

***

## Conclusion

This room covered the core architecture of the Elastic Stack. It included the installation of Elasticsearch and Kibana on a Linux host, followed by the deployment of a Fleet Server and Elastic Agent. Native Linux system logs, Apache web logs, and custom VPN logs were all successfully ingested. Finally, dashboards and visualizations were built to transform raw log data into readable metrics, completing the foundational setup of a SOC lab.
