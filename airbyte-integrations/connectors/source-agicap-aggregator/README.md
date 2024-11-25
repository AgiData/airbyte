# Agicap Aggregator Source
This directory contains the manifest-only connector for `source-agicap-aggregator` (generated using the Airbyte Connector Builder).  

This connector reads data from the Agicap Aggregator API.  

It has been developed for Agicap internal usage only.  
NB: no sensitive data is stored in this connector definition.

## Warning
The targeted API by this connector is reachable using OAuth authentication.  
Dedicated credentials have been created for the Data Team, and are available in a GCP secret.  

Also, the connector is only able to run from:
- Agicap local user account with VPN enabled
- Airbyte VM instance (IP whitelisted by Tech Teams)

## Local development
### Prerequisites
Have `airbyte-ci` installed.

### Build
This will create a dev image (`source-agicap-aggregator:dev`) that you can use to test the connector locally.
```bash
airbyte-ci connectors --name=source-agicap-aggregator build
```
NB: possibly with complementary `--architecture=linux/amd64` flag

### Test
This will run the acceptance tests for the connector.
```bash
airbyte-ci connectors --name=source-agicap-aggregator test
```

## Local usage
Some useful commands to test the connector locally:
1. Build the connector as described above
2. Copy the `integration_tests/sample_config.json` file to a `secrets/prod_config.json` file
3. Fill the required credentials in the `secrets/prod_config.json` file (available in GCP Secret)
4. Run below commands to test the connector and ensure it reads data correctly
```bash
# `cd` to the source-agicap-aggregator connector repository
docker run --rm -i airbyte/source-agicap-aggregator:dev spec
docker run --rm -v $(pwd):/data -i airbyte/source-agicap-aggregator:dev check --config /data/secrets/prod_config.json
docker run --rm -v $(pwd):/data -i airbyte/source-agicap-aggregator:dev discover --config /data/secrets/prod_config.json
docker run --rm -v $(pwd):/data -i airbyte/source-agicap-aggregator:dev read --config /data/secrets/prod_config.json --catalog /data/integration_tests/configured_catalog.json > secrets/output.json
cat secrets/output.json
```
