# Helm Charts

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
![Release Charts](https://github.com/freepik-company/bucket-simple-server/workflows/Release%20Charts/badge.svg?branch=main)
[![Releases downloads](https://img.shields.io/github/downloads/freepik-company/bucket-simple-server/total.svg)](https://github.com/freepik-company/bucket-simple-server/releases)

Bucket Simple Server Helm Charts

## Usage

[Helm](https://helm.sh) must be installed to use the charts.
Please refer to Helm's [documentation](https://helm.sh/docs/) to get started.

Once Helm is set up properly, add the repo as follows:

```console
helm repo add bucket-simple-server https://freepik-company.github.io/bucket-simple-server/
```

You can then run the following command to see the charts.

```console
helm search repo bucket-simple-server
```

## How to contribute

For doing it you must:
* Fork the repository
* Make your changes to the code in a specific branch
* Open a PR

> The charts will be reviewed and tested (always)

## License

[Apache 2.0 License](./LICENSE)










## Motivation

This project was created to solve a common problem: securely sharing reports and small files without exposing an entire storage bucket. 

Setting up a complex proxy like Envoy is time-consuming and often requires crafting custom plugins to connect to different storage services. While Envoy is great for large-scale projects, this lightweight solution is perfect for smaller needs, such as automated
reports, etc.

It’s simple, focused, and easy to use, making secure file sharing much easier.

## Flags

As every configuration parameter can be defined in the config file, there are only few flags that can be defined.
They are described in the following table:

| Name              | Description                    |    Default    | Example                  |
|:------------------|:-------------------------------|:-------------:|:-------------------------|
| `--config`        | Path to the YAML config file   | `config.yaml` | `--config ./config.yaml` |
| `--log-level`     | Verbosity level for logs       |    `info`     | `--log-level info`       |
| `--disable-trace` | Disable showing traces in logs |    `info`     | `--log-level info`       |

> Output is thrown always in JSON as it is more suitable for automations

```console
bss run \
    --log-level=info
    --config="./config.yaml"
```

## Examples

Here you have a complete example. More up-to-date one will always be maintained in 
`docs/prototypes` directory [here](./docs/prototypes)


```yaml
version: v1alpha1
kind: Config
metadata:
  name: access-to-reports
spec:
  # Source of data to be served. 
  # This section can be extended to support other sources like S3, Azure Blob Storage, etc.
  source:
    gcs:
      bucket: general-purposes-bucket
      credentials:
        path: /tmp/credentials.json

  # Web server configuration
  # Here it's the place to define the server configuration like port, host, credentials, etc.
  webServer:
    listener:
      port: 9090
      host: localhost

    # Several credentials can be defined
    credentials:
      - type: "bearer"
        token: "12345xxxx12345"

      - type: "bearer"
        token: "6789yyyy6789"
      
      - type: "bearer"
        token: "${TOKEN_FROM_ENV}"

    # Routes must be defined to allow access to the data
    # They are defined as golang regular expressions
    # Remember: negative lookbehind is not supported
    allowedTargets:
      - route: "^/qa-reports/(.*).json$"
      - route: "^/pipelines-results/(.*).json$"

```

> ATTENTION:
> If you detect some mistake on the config, open an issue to fix it. This way we all will benefit

## How to deploy

This project can be deployed in Kubernetes, but also provides binary files 
and Docker images to make it easy to be deployed however wanted

### Binaries

Binary files for most popular platforms will be added to the [releases](https://github.com/freepik-company/bucket-simple-server/releases)

### Kubernetes

You can deploy `bucket-simple-server` in Kubernetes using Helm as follows:

```console
helm repo add bucket-simple-server https://freepik-company.github.io/bucket-simple-server/

helm upgrade --install --wait bucket-simple-server \
  --namespace bucket-simple-server \
  --create-namespace freepik-company/bucket-simple-server
```

> More information and Helm packages [here](https://freepik-company.github.io/bucket-simple-server/)


### Docker

Docker images can be found in GitHub's [packages](https://github.com/freepik-company/bucket-simple-server/pkgs/container/bucket-simple-server) 
related to this repository

> Do you need it in a different container registry? I think this is not needed, but if I'm wrong, please, let's discuss 
> it in the best place for that: an issue

## How to contribute

We are open to external collaborations for this project: improvements, bugfixes, whatever.

For doing it, open an issue to discuss the need of the changes, then:

- Fork the repository
- Make your changes to the code
- Open a PR and wait for review

The code will be reviewed and tested (always)

> We are developers and hate bad code. For that reason we ask you the highest quality
> on each line of code to improve this project on each iteration.

## License

Copyright 2022.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
