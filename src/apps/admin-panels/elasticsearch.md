# Elasticsearch / Kibana

**What it is:** Distributed search and analytics engine. Stores data in JSON indices. Frequently exposed without authentication, leaking credentials, PII, and application data. Kibana is its visualization frontend.

**Default ports:** 9200 (Elasticsearch), 5601 (Kibana)

**Vuln research:**
- [Elastic Security Advisories](https://www.elastic.co/community/security)
- [SearchSploit: `searchsploit elasticsearch`](https://www.exploit-db.com/search?q=elasticsearch)

---


### Check Elasticsearch (port 9200)

Unauthenticated by default — Dump all indices

```bash
curl -s http://$IP:9200/
curl -s http://$IP:9200/_cat/indices?v
curl -s http://$IP:9200/_search?pretty
```

<details>
<summary>Example Output</summary>

```
{
  "name" : "node-1",
  "cluster_name" : "elasticsearch",
  "version" : { "number" : "7.17.0" }
}
Indices may contain credentials, PII, logs
```
</details>

---

### Dump specific index

Search for sensitive data — Credentials, tokens, PII

```bash
curl -s "http://$IP:9200/<index>/_search?pretty&size=100"
curl -s "http://$IP:9200/_all/_search?q=password&pretty"
```

---

### Kibana (port 5601)

Dashboard may expose sensitive data — Check for open access

```bash
curl -s http://$IP:5601/api/status
# If accessible, browse dashboards for credentials/data
```

---
