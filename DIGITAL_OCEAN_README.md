# Digital Ocean Setup

### Gotchas that will save you time:

* Create a project in Google Cloud Provider (GCP). Copy the project ID for the next step.
* Create a project in Digital Ocean with the name of the ID that you copied in the GCP project. e.g. `My-Project-1234567`
  - Under the hood the Terraform code that is generated uses the `projectId` field in your `config/main.json` to store
    the Terraform state in a GCP bucket. This same field is also used to access the Digital Ocean project when building your infrastructure so these names MUST be
    the same.
* The ssh user name only works with `root` when digitalocean is the provider. Any other name will fail to fully install.
* Inside of your `config/main.json` the `machineType` field must have one of the slug values found in the [DigitalOcean docs](https://slugs.do-api.dev/). At the time of this writing `s-4vcpu-8gb-amd` is more than enough (~$48/mo.)

### Example config

Found in `config/main.json`

```
{
  "project": "my-project-name",
  "polkadotBinary": {
    "url": "https://github.com/paritytech/polkadot/releases/download/v0.9.3/polkadot",
    "checksum": "sha256:722767e515113ac2d5efb7dcdb6eafee98f021c803726fd16024ee67a4d4248f"
  },
  "nodeExporter": {
    "enabled": true,
    "binary": {
      "url": "https://github.com/prometheus/node_exporter/releases/download/v1.0.1/node_exporter-1.0.1.linux-amd64.tar.gz",
      "checksum": "sha256:3369b76cd2b0ba678b6d618deab320e565c3d93ccb5c2a0d5db51a53857768ae"
    }
  },
  "polkadotRestart": {
    "enabled": false
  },
  "chain": "kusama",
  "polkadotNetworkId": "ksmcc3",
  "state": {
    "project": "my-project-name"
  },
  "validators": {
    "telemetryUrl": "wss://telemetry-backend.w3f.community/submit",
    "additionalFlags": "--unsafe-pruning --pruning 1000",
    "dbSnapshot": {
      "url": "https://ksm-rocksdb.polkashots.io/kusama-7982125.RocksDb.7z",
      "checksum": "sha256:5fd69845611ef0e39f8e4f0981d4ab1c41d3ab27303ab4d49f2572fb1fd518e0"
    },
    "loggingFilter": "sync=trace,afg=trace,babe=debug",
    "nodes": [
      {
        "provider": "digitalocean",
        "machineType": "s-4vcpu-8gb-amd",
        "count": 1,
        "image": "ubuntu-20-04-x64",
        "location": "nyc3",
        "projectId": "my-project-ID-from-GCP-that-is-also-the-digital-ocean-project-name",
        "nodeName": "Veracity/example-node-name",
        "sshUser": "root"
      }
    ]
  }
}

```
