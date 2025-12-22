# Verify Wazuh Indexer is accessible

      curl -k -u admin https://localhost:9200

Expected result: JSON output with cluster info.
If this works, it is safe to proceed.
-----------
# Identify which indices consume space

Show largest indices first (real sizes):

      curl -k -u admin \
      "https://localhost:9200/_cat/indices?bytes=gb&h=index,store.size&s=store.size:desc" | head -20

Typical heavy indices:

wazuh-alerts-4.x-YYYY.MM.DD

wazuh-archives-4.x-YYYY.MM.DD

Each daily index may be ~1 GB, but hundreds of them add up.

------------

# Free disk space immediately (manual cleanup)
Decide retention target (example: keep last 60 days).

Delete old alert indices safely by month.

Example: delete old alert indices

      curl -k -u admin -X DELETE \
      "https://localhost:9200/wazuh-alerts-4.x-2025.07.*"
---
      curl -k -u admin -X DELETE \
      "https://localhost:9200/wazuh-alerts-4.x-2025.08.*"
---
      curl -k -u admin -X DELETE \
      "https://localhost:9200/wazuh-alerts-4.x-2025.09.0*"
---

Each command should return: {"acknowledged":true}
# Verify Disk is Free

      du -sh /var/lib/wazuh-indexer
      df -h /

# Lets Get deploy Restention policy for 60 Days : -

nano /root/wazuh-retention-60d.json


Save and Exit

# Upload the policy to Wazuh Indexer

      curl -k -u admin -X PUT \
      "https://localhost:9200/_plugins/_ism/policies/wazuh-60d" \
      -H "Content-Type: application/json" \
      -d @/root/wazuh-retention-60d.json


# Apply retention policy to alert indices

      curl -k -u admin -X POST \
      "https://localhost:9200/_plugins/_ism/add/wazuh-alerts-4.x-*" \
      -H "Content-Type: application/json" \
      -d '{"policy_id":"wazuh-60d"}'


