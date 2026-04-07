
# EUDI-Compatible Sports Wallet - Status List Server

This project is a proof of concept to show the feasibility of applying EUDI Wallet standards to the decentralized nature of credentials in regulated sports. This project was developed under the scope of [W3C OpenAthletics Community Group](https://www.w3.org/community/opentrack/) and motivated by the outcomes of [AthTech'25](https://athtech.run/2025). The implementation is an adaptation of the [official EUDI Android Wallet reference app](https://github.com/eu-digital-identity-wallet/).

In this project you can find new document definitions required to implement the main [use cases and scenarios for sports credentials](https://www.w3.org/community/opentrack/2026/04/03/use-cases-of-decentralized-sports-credentials/).    


## Related repositories

If you want to test or reuse this project, just use the existing servers deployed or get all the software components in the following repositories:

* WALLET NATIVE APP
  * Wallet for Android: https://github.com/espinr/eudi-app-android  
  * Backend for the Wallet: https://github.com/espinr/eudi-srv-wallet-provider

* VERIFIER (PROXIMITY)
  * Verifier for Android (Based on Multipaz): https://github.com/espinr/eudi-app-multiplatform-verifier-ui

* ISSUANCE SERVICE:
  * Status list server (this repo): https://github.com/espinr/eudi-srv-statuslist-py
  * OIDC server: https://github.com/espinr/eudi-srv-issuer-oidc-py
  * APIs for the backend: https://github.com/espinr/eudi-srv-web-issuing-eudiw-py
  * Frontend: https://github.com/espinr/eudi-srv-web-issuing-frontend-eudiw-py

* ONLINE VERIFICATION SERVICE:
  * Backend APIs: https://github.com/espinr/eudi-srv-web-verifier-endpoint
  * Frontend: https://github.com/espinr/eudi-web-verifier

The issuance and verification services can be deployed easily using Docker Compose. 

## Issuance service orchestration using `docker-compose`

For an easy deployment, this project can be configured using Docker and `docker-compose` in particular.

### Organization of directories and services

The server would require a structure of directories with the content of the issuance-related repositories listed above, including: 

```
eudi-issuer-backend 
eudi-issuer-frontend
eudi-issuer-oidc
eudi-statuslist
logs                    // containers' logs
secrets-pid-issuer      // certs and keys
config-services         // environment files and server config
docker-compose.yml      // see below
```

`/config-services` contains the configuration files (and ENV files) for the servers, including:

```
.backend-env            // .env for eudi-issuer-backend 
.frontend-env           // .env for eudi-issuer-frontend
.statuslist-env         // .env for eudi-statuslist
oidc-config.json        // config file for eudi-issuer-oidc
```

`/secrets-pid-issuer` contains the private keys and certificates for the issuance server, organized in two directories:

```
cert
privKey
```

### `docker-compose.yml` 

```
version: "3.3"
services:
  eudiw-issuer-oidc:
    build:
      context: ./eudi-issuer-oidc
      dockerfile: Dockerfile
    ports:
      - "6005:5000"
    volumes:
      - ./config-services/oidc-config.json:/config.json:ro
      - ./logs/oidc/:/tmp/oidc_log_dev/
    restart: unless-stopped

  eudiw-issuer-backend:
    build:
      context: ./eudi-issuer-backend
      dockerfile: Dockerfile
    ports:
      - "5000:5000"
    volumes:
      - ./secrets-pid-issuer/cert/:/etc/eudiw/pid-issuer-dev/cert/:ro
      - ./secrets-pid-issuer/cert/:/etc/eudiw/pid-issuer/cert/:ro
      - ./secrets-pid-issuer/privKey/:/etc/eudiw/pid-issuer-dev/privKey/:ro
      - ./secrets-pid-issuer/privKey/:/etc/eudiw/pid-issuer/privKey/:ro
      - ./logs/backend/:/tmp/log_dev
    env_file:
      - ./config-services/.backend-env
    extra_hosts:
      - "host.docker.internal:host-gateway"
    restart: unless-stopped

  eudiw-issuer-frontend:
    build:
      context: ./eudi-issuer-frontend
      dockerfile: Dockerfile
    ports:
      - "6007:5000"
    volumes:
      - ../logs/frontend/:/tmp/log_dev
    env_file:
      - ./config-services/.frontend-env
    restart: unless-stopped

  eudiw-statuslist:
    build:
      context: ./eudi-statuslist
      dockerfile: Dockerfile
    ports:
      - "6009:5000"
    volumes:
      - ./secrets-pid-issuer/cert/:/etc/eudiw/pid-issuer/cert/:ro
      - ./secrets-pid-issuer/privKey/:/etc/eudiw/pid-issuer/privKey/:ro
      - ../logs/statuslist/:/tmp/status_lists
    env_file:
      - ./config-services/.statuslist-env
    restart: unless-stopped
```

----

:heavy_exclamation_mark: **Important!** For more information about the base of the original project, please read the [EUDI Wallet Reference Implementation project description](https://github.com/eu-digital-identity-wallet/.github/blob/main/profile/reference-implementation.md)


[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://www.apache.org/licenses/LICENSE-2.0)

## About eudi-srv-statuslist-py


*A Python-based revocation project for Attestation Status List and Attestation Revocation List.*

## Description
`statuslist-py` is a Python library designed to issue and manage revocation status using Attestation Status Lists and Attestation Revocation Lists.

## Installation
Refer to the [installation guide](./install.md) for setup instructions.

## Usage
Detailed API documentation and usage examples can be found in the [Swagger UI](https://dev.issuer.eudiw.dev/tslswagger/swagger).
X-API-Key = test

## Features
- Supports Attestation Status List (ASL) [draft-ietf-oauth-status-list-02](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-status-list-02)
- Supports Attestation Revocation List (ARL) [ ISO/IEC CD 18013-5 second edition ]

## :heavy_exclamation_mark: Disclaimer

The released software is a initial development release version:

-   The initial development release is an early endeavor reflecting the efforts of a short timeboxed
    period, and by no means can be considered as the final product.
-   The initial development release may be changed substantially over time, might introduce new
    features but also may change or remove existing ones, potentially breaking compatibility with your
    existing code.
-   The initial development release is limited in functional scope.
-   The initial development release may contain errors or design flaws and other problems that could
    cause system or other failures and data loss.
-   The initial development release has reduced security, privacy, availability, and reliability
    standards relative to future releases. This could make the software slower, less reliable, or more
    vulnerable to attacks than mature software.
-   The initial development release is not yet comprehensively documented.
-   Users of the software must perform sufficient engineering and additional testing in order to
    properly evaluate their application and determine whether any of the open-sourced components is
    suitable for use in that application.
-   We strongly recommend not putting this version of the software into production use.
-   Only the latest version of the software will be supported

## How to contribute

We welcome contributions to this project. To ensure that the process is smooth for everyone
involved, follow the guidelines found in [CONTRIBUTING.md](CONTRIBUTING.md).

## License

### License details

Copyright (c) 2023 European Commission

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
