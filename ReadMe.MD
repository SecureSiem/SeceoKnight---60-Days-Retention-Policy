nano /root/wazuh-retention-60d.json


Save and Exit

# Upload the policy to Wazuh Indexer

      curl -k -u admin -X PUT \
      "https://localhost:9200/_plugins/_ism/policies/wazuh-60d" \
      -H "Content-Type: application/json" \
      -d @/root/wazuh-retention-60d.json
