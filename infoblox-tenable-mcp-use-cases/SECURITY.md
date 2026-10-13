# Security Policy

## Reporting a Vulnerability

Please do not open a public GitHub issue for security vulnerabilities.

Report suspected vulnerabilities in Infoblox products through the Vulnerability Disclosure page on [infoblox.com](https://www.infoblox.com/). Report issues in Tenable products to Tenable through its published disclosure process.

<!-- TODO: confirm the preferred reporting contact with Infoblox PSIRT before publishing. -->

## Handling Credentials

* Never commit API keys, tokens or other secrets to this repository.
* Keep credentials in `.env` (ignored by Git) or in a secrets manager.
* Use a Service API key tied to a Service User with a read-only role for these examples.
* If a key is exposed, revoke it immediately in the Infoblox Portal under User Access → Service API Keys, then issue a new one.
