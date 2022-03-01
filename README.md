![build](https://github.com/targetaidev/sideweed/workflows/CI/badge.svg) ![license](https://img.shields.io/badge/license-AGPL%20V3-blue)

*sideweed* is a high-performance sidecar load-balancer. By attaching a tiny load balancer as a sidecar to each of the client application processes, you can eliminate the centralized loadbalancer bottleneck and DNS failover management. *sideweed* automatically avoids sending traffic to the failed servers by checking their health via the readiness API and HTTP error returns.

# Install

## Binary Releases

Download the latest binary from https://github.com/targetaidev/sideweed/releases and unzip a single binary file.

## Build from source

```
go install -v github.com/targetaidev/sideweed@latest
```

> You will need a working Go environment. Therefore, please follow [How to install Go](https://golang.org/doc/install).
> Minimum version required is go1.17

# Usage

```
NAME:
  sideweed - High-Performance sidecar load-balancer

USAGE:
  sideweed - [FLAGS] POOL1 [POOL2..]

FLAGS:
  --address value, -a value           listening address (default: ":8080")
  --health-path value, -p value       health check path
  --read-health-path value, -r value  health check path for read access - valid only for failover pool
  --health-port value                 health check port (default: 0)
  --health-duration value, -d value   health check duration in seconds (default: 5)
  --insecure, -i                      disable TLS certificate verification
  --log, -l                           enable logging
  --trace value, -t value             enable request tracing - valid values are [all,application,cluster] (default: "all")
  --quiet, -q                         disable console messages
  --json                              output logs and trace in json format
  --debug                             output verbose trace
  --cacert value                      CA certificate to verify peer against
  --client-cert value                 client certificate file
  --client-key value                  client private key file
  --cert value                        server certificate file
  --key value                         server private key file
  --help, -h                          show help
  --version, -v                       print the version

POOL:
  Each POOL is a comma separated list of servers of that pool: http://172.17.0.{2...5},http://172.17.0.{6...9}.
  If all servers in a POOL are down, then the traffic is routed to the next pool - POOL2.
```

## Examples

### Load balance across a web service using DNS provided IPs
```
$ sideweed --health-path=/health/endpoint http://myapp.myorg.dom
```

### Load balance across servers (http://server1:9000 to http://server4:9000)
```
$ sideweed --health-path=/health/endpoint --address :8000 http://server{1...4}:9000
```

### Two pools with 4 servers each
```
$ sideweed --health-path=/health/endpoint http://pool1-server{1...4}:9000 http://pool2-server{1...4}:9000
```
