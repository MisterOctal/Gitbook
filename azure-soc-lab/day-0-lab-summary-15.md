---
icon: map
cover: >-
  https://tryhackme-images.s3.eu-west-1.amazonaws.com/room-icons/678ecc92c80aa206339f0f23-1767057510266
coverY: 0
coverHeight: 138
---

# Day 15 - Kibana

**Date:** 13.07.2026

**Core Concept**: Geographic Threat Distribution Analysis and Monitoring Validation

Today's focus was on visualizing the geographic spread of RDP brute force attempts using Kibana, alongside confirming Uptime Kuma is reliably tracking the Windows honeypot's availability.

***

## Section 1: Overview

With the Windows honeypot's decoy accounts now in place and generating richer data, attention turned to mapping where the RDP brute force traffic is actually originating from. The Cowrie honeypot remains offline for this round, as Azure's Malaysia region is currently experiencing heavy usage, making it impractical to bring back online today.

***

## Section 2: Kibana Geo Mapping

A geographic map was built in Kibana using Winlogbeat data, plotting the source locations of RDP login attempts against the Windows honeypot. The known brute-forcing IPs already identified in prior analysis were filtered out first, in order to see what the remaining traffic looked like without the dominant scanners skewing the picture.

Getting the map working required enriching the existing Winlogbeat data with GeoIP first, since the raw failed-logon events (Event ID 4625) had no location data attached. A GeoIP ingest pipeline was created, guarded to skip events where the source IP is a literal dash or a loopback address rather than a real attacker IP, since Windows logs those for local/system logons and they would otherwise error the processor:

```bash
curl -X PUT "http://10.0.1.4:9200/_ingest/pipeline/add-geoip?pretty" -H 'Content-Type: application/json' -d'
{
  "processors": [
    {
      "geoip": {
        "field": "winlog.event_data.IpAddress",
        "target_field": "geoip",
        "ignore_missing": true,
        "if": "ctx.winlog?.event_data?.IpAddress != null && ctx.winlog.event_data.IpAddress != \"-\" && ctx.winlog.event_data.IpAddress != \"::1\" && ctx.winlog.event_data.IpAddress != \"127.0.0.1\"",
        "ignore_failure": true
      }
    }
  ]
}'
```

A destination index was created with an explicit `geo_point` mapping before any data was loaded into it. This step turned out to be the critical one, since letting a reindex auto-create the index instead lets Elasticsearch guess the field type, and it guessed wrong, which silently broke Kibana Maps' ability to read the location field:

```bash
curl -X PUT "http://10.0.1.4:9200/winlogbeat-geo?pretty" -H 'Content-Type: application/json' -d'
{
  "mappings": {
    "properties": {
      "geoip": { "properties": { "location": { "type": "geo_point" } } },
      "@timestamp": { "type": "date" }
    }
  }
}'
```

The existing days of Winlogbeat data were then reindexed through the pipeline into the correctly mapped index:

```bash
curl -X POST "http://10.0.1.4:9200/_reindex?pretty" -H 'Content-Type: application/json' -d'
{
  "source": { "index": ["winlogbeat-2026.07.11", "winlogbeat-2026.07.12"] },
  "dest":   { "index": "winlogbeat-geo", "pipeline": "add-geoip" }
}'
```

23 events landed with resolved coordinates, confirmed via a count query against `geoip.location` and a mapping check to verify the field typed correctly as `geo_point`. From there, a Kibana data view was created against the `winlogbeat-geo` index pattern, and a Maps layer was added pointing at `geoip.location` as the geospatial field, with the time range set to cover the collection window.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-13 131941.png" alt=""><figcaption></figcaption></figure>

Once filtered, the results spread across a wide range of countries rather than clustering around a small number of sources. Locations observed include Seychelles, South Korea, Thailand, Singapore, Germany, the United States, and Brazil, among others.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-13 131248.png" alt="" width="375"><figcaption></figcaption></figure>

The Seychelles data point stood out, not because of any unusual attack behavior, but because it isn't a location commonly associated with cyber threat activity in general threat intelligence reporting, making it worth flagging even though the traffic itself didn't behave differently from the rest.

<figure><img src="../.gitbook/assets/Screenshot 2026-07-13 131321.png" alt="" width="375"><figcaption></figcaption></figure>

The single most active source by volume remains the Germany-based IP identified back on Day 14, which continues to account for the largest share of brute force attempts. Every other location represents comparatively low-volume, scattered activity, consistent with the earlier finding that a large portion of the traffic comes from a long tail of minor sources rather than a handful of dominant ones.

***

## Section 3: Uptime Kuma Validation

<figure><img src="../.gitbook/assets/Screenshot 2026-07-13 131922.png" alt=""><figcaption></figcaption></figure>

Uptime Kuma's Windows honeypot monitor was confirmed working correctly today, showing a live "Up" status with sub-millisecond ping response and a 75.22% uptime figure over the current window, reflecting the periods the VM has been intentionally powered on and off rather than any unexpected downtime. The Cowrie monitor correctly reflects a down status, consistent with the honeypot being deliberately offline this round.

***

## Conclusion

Day 15 confirms that the RDP brute force traffic hitting the Windows honeypot is globally distributed rather than concentrated in a small number of regions, with Germany remaining the standout source by volume. Uptime Kuma is now validated as a reliable signal for infrastructure status going forward, correctly distinguishing between intentional and unintentional downtime across both honeypots.
