# Config-Gen

This is a simple generator tool that will read files for a specific struct and generate a yaml representation of it with comments to help keep things in sync. This is meant to be used as part of the `//go:generate` command but can also be installed and used as a stand alone binary.

### Install

```sh
go install .
```

### Usage

CLI flags.

```sh
Usage:
  config-gen [flags]

Flags:
  -a, --api-output string              output path for openapi spec if desired
  -f, --file string                    default is stdin
  -h, --help                           help for config-gen
  -o, --output string                  output path, default is stdout
  -t, --template string                template file to use
  -v, --v Level                        number for the log level verbosity
```

Use as a go generate command example
```go
//go:generate sh -c "controller-gen crd paths=../../hack/config-gen/configcrd output:stdout | go run -mod vendor ../../hack/config-gen -a ../../cockpit-plugin/packaging/config-openapi-spec.json -o ../../packaging/microshift/config.yaml"
//go:generate sh -c "controller-gen crd paths=../../hack/config-gen/configcrd output:stdout | go run -mod vendor ../../hack/config-gen -o ../../docs/howto_config.md -t ../../docs/howto_config.md"
```

Use the example test to see it in action, run the command from the `hack/config-gen` directory.

```sh
controller-gen crd paths=../../hack/config-gen/configcrd output:stdout | go run -mod vendor ../../hack/config-gen
```

The sample output should be.
```yaml
#!! Do Not Edit
#!! This is a generated file
apiServer:
    # Kube apiserver advertise address to work around the certificates issue when requiring external access using the node IP. This will turn into the IP configured in the endpoint slice for kubernetes service. Must be a reachable IP from pods. Defaults to service network CIDR first address.
    advertiseAddress: ""
    # SubjectAltNames added to API server certs
    subjectAltNames:
        - ""
debugging:
    # Valid values are: "Normal", "Debug", "Trace", "TraceAll". Defaults to "Normal".
    logLevel: Normal
dns:
    # baseDomain is the base domain of the cluster. All managed DNS records will be sub-domains of this base. 
    #  For example, given the base domain `example.com`, router exposed domains will be formed as `*.apps.example.com` by default, and API service will have a DNS entry for `api.example.com`, as well as "api-int.example.com" for internal k8s API access. 
    #  Once set, this field cannot be changed.
    # example:
    #   microshift.example.com
    baseDomain: example.com
etcd:
    # Set a memory limit on the etcd process; etcd will begin paging memory when it gets to this value. 0 means no limit.
    memoryLimitMB: 0
network:
    # IP address pool to use for pod IPs. This field is immutable after installation.
    clusterNetwork:
        - # The complete block for pod IPs.
          cidr: 10.42.0.0/16
    # IP address pool for services. Currently, we only support a single entry here. This field is immutable after installation.
    serviceNetwork:
        - ""
    # The port range allowed for Services of type NodePort. If not specified, the default of 30000-32767 will be used. Such Services without a NodePort specified will have one automatically allocated from this range. This parameter can be updated after the cluster is installed.
    serviceNodePortRange: 30000-32767
node:
    # If non-empty, will use this string to identify the node instead of the hostname
    hostnameOverride: ""
    # IP address of the node, passed to the kubelet. If not specified, kubelet will use the node's default IP address.
    nodeIP: ""
```
