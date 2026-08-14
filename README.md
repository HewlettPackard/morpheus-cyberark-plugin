# Morpheus CyberArk Conjur Plugin

The Morpheus CyberArk Conjur Plugin integrates Morpheus with CyberArk Conjur to enable secure credential retrieval and secret storage. It provides a credential provider for using Conjur-managed credentials in Morpheus automation, and a Cypher module that maps Morpheus Cypher paths to Conjur secrets.

## Table of Contents

- [Features](#features)
- [Requirements](#requirements)
- [Repository structure](#repository-structure)
- [Building the plugin](#building-the-plugin)
- [License](#license)
- [Installing](#installing)
- [Detailed Usage Steps](#detailed-usage-steps)
- [API Endpoints](#api-endpoints)

---

## Features

### Conjur Credential Provider

Use CyberArk Conjur as a credential store for Morpheus integrations and automation. Credentials are retrieved from Conjur at runtime rather than stored in Morpheus.

### Conjur Cypher Module

Map Morpheus Cypher key paths to Conjur secret paths. Supports read, write, and delete operations on secrets stored in Conjur, accessible via the `conjur/` Cypher prefix in Morpheus.

---

## Requirements

| Requirement | Version |
|-------------|---------|
| Morpheus | 7.0.3 or later |
| Java | 11 or later |
| Gradle | Use the included Gradle wrapper (`./gradlew`) |

Additional prerequisites:

- A running CyberArk Conjur server accessible over HTTPS from the Morpheus appliance
- A Conjur user account and API key with read (and optionally write) access to the target secrets
- The Conjur organisation (account) name

---

## Repository structure

```
src/main/groovy/com/morpheusdata/cyberark/
├── CyberArkPlugin.groovy           - Plugin entry point; registers providers and plugin-level settings (URL, username, API key, organisation)
├── ConjurCredentialProvider.groovy - CredentialProvider implementation; retrieves credentials from Conjur at runtime
├── ConjurCypherProvider.groovy     - CypherModuleProvider implementation; wires the Conjur Cypher module
└── ConjurCypherModule.groovy       - AbstractCypherModule implementation; read/write/delete secrets via Conjur API
build.gradle, gradle.properties     - Build configuration and plugin metadata
```

---

## Building the plugin

Run the following command to compile and package the plugin jar:

```bash
./gradlew clean build
```

The packaged jar will be written to `build/libs/`.

To execute tests, use the following command:

```bash
./gradlew test
```

---

## License

This project is licensed under the Apache License 2.0.

See the [LICENSE](LICENSE) file for details.

---

## Installing

1. Build the plugin (see [Building the plugin](#building-the-plugin)) or download a released jar.
2. In Morpheus, navigate to **Administration > Integrations > Plugins**.
3. Click **Add** and upload the `morpheus-cyberark-plugin-<version>.jar` from `build/libs/`.
4. Navigate to **Administration > Integrations > Plugins**, open the CyberArk Conjur plugin settings, and provide the **Conjur API Url**, **Conjur Username**, **Conjur Username API Key**, and **Conjur Organization**.

---

## Detailed Usage Steps

### Configuring the Plugin

1. After installing, go to **Administration > Integrations > Plugins**.
2. Open the CyberArk Conjur plugin and click **Edit Settings**.
3. Enter:
   - **Conjur API Url** — full URL including port, e.g. `https://conjur.example.com:8443`
   - **Conjur Username** — the Conjur identity used to authenticate
   - **Conjur Username API Key** — the API key for the above identity
   - **Conjur Organization** — the Conjur account name
   - **Clear Secret On Deletion** — if checked, secrets are deleted from Conjur when removed from Morpheus Cypher
4. Save.

### Using Conjur Secrets in Cypher

1. Go to **Services > Cypher > Add**.
2. Use the `conjur/` mount prefix and provide the path to the secret in Conjur.
3. The plugin retrieves the secret value from Conjur at read time.

### Using Conjur as a Credential Provider

1. When adding a Morpheus integration that supports credentials, select **CyberArk Conjur** as the credential type.
2. Provide the Conjur path to the credential secret. Morpheus retrieves the credential from Conjur at runtime.

---

## API Endpoints

This plugin communicates with the **CyberArk Conjur REST API** at the configured Conjur API Url. All calls use HTTPS.

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `{conjurUrl}/authn/{account}/{username}/authenticate` | POST | Authenticate and obtain an access token |
| `{conjurUrl}/secrets/{account}/variable/{secretPath}` | GET | Read a secret value |
| `{conjurUrl}/secrets/{account}/variable/{secretPath}` | POST | Write a secret value |
| `{conjurUrl}/secrets/{account}/variable/{secretPath}` | DELETE | Delete a secret |
