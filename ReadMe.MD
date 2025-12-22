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


