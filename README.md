# Morpheus CyberArk Conjur Plugin

This plugin provides secret and credential store integration between [CyberArk Conjur](https://www.cyberark.com/products/identity-security-platform/conjur-secrets-manager/) and [Morpheus](https://morpheusdata.com). It enables Conjur-backed Morpheus credentials and Conjur-backed Cypher secrets from within the Morpheus platform.

## Requirements

| Component | Minimum Version |
|-----------|----------------|
| Morpheus | 7.0.3 |

## Installation

1. Download the latest `.jar` from the [Releases](https://github.com/HewlettPackard/morpheus-cyberark-plugin/releases) page, or [build it yourself](#building).
2. In Morpheus, navigate to **Administration → Integrations → Plugins**.
3. Click **Browse** and upload the `.jar` file.
4. The **Conjur** credential store integration and **conjur** Cypher mount will appear after the plugin loads.

## Configuration

Configure the plugin settings in Morpheus (**Administration → Integrations → Plugins**) with the default Conjur connection values:

| Field | Description |
|-------|-------------|
| **Conjur API Url** | Full URL of the Conjur server, e.g. `https://example.conjur.server:8443`. |
| **Conjur Username** | Conjur user or host identity used for authentication. |
| **Conjur Username API Key** | API key for the Conjur identity. |
| **Conjur Organization** | Conjur account or organization name. |
| **Clear Secret On Deletion** | When enabled, deletes clear the remote value by writing an empty secret. |

When adding a Conjur credential store integration in Morpheus, the integration can override the plugin defaults:

| Field | Description |
|-------|-------------|
| **API Url** | Conjur API endpoint URL. Overrides the plugin value when set. |
| **Username** | Conjur user or host identity. Overrides the plugin value when set. |
| **API Key** | API key for the Conjur identity. Overrides the plugin value when set. |
| **Organization** | Conjur account or organization name. Overrides the plugin value when set. |
| **Secret Path** | Optional path prefix for Morpheus-managed credentials, e.g. `morpheus-credentials/`. |
| **Clear Secret On Deletion** | When enabled, deletes clear the remote value by writing an empty secret. |

## Features

### Credential Store

The plugin registers a `CredentialProvider` named **Conjur**. Supported operations include:

- Validate Conjur connectivity and authentication with `/whoami`.
- Create Morpheus credential data as Conjur variable secrets.
- Load credential data from Conjur on demand.
- Update credential data in Conjur.
- Optionally clear the Conjur secret value when a Morpheus credential is deleted.
- Use integration-level connection settings or inherit defaults from the plugin settings.

### Cypher Secrets

The plugin registers a `CypherModuleProvider` with the **conjur** mount point. Supported operations include:

- Read secrets from Conjur through Morpheus Cypher paths mounted under `conjur`.
- Write Cypher secret values to Conjur variables.
- Avoid persisting Conjur-backed values in the Morpheus datastore on read.
- Optionally clear the Conjur secret value when a Cypher secret is deleted.

## Building

```bash
./gradlew shadowJar
```

The plugin JAR will be written to `build/libs/`.

## License

Copyright 2024 Morpheus Data, LLC. Licensed under the [Apache License, Version 2.0](LICENSE).
