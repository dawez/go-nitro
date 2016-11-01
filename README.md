# An incomplete but mostly useful Go client for configuring Citrix NetScaler

## About
The [NITRO](https://docs.citrix.com/en-us/netscaler/11-1/nitro-api/nitro-rest.html) API is the REST-like API to the Citrix NetScaler. This project provides a Golang SDK that can be used to make configuration API calls to a Citrix NetScaler.

## Usage
Import the SDK from github.com/chiradeep/go-nitro/netscaler. Config objects are available at github.com/chiradeep/go-nitro/config. 
Instantiate a client using `NewNitroClient`. To initialize the client from environment variables (`NS_URL`, `NS_LOGIN`, `NS_PASSWORD`), use `NewNitroClientFromEnv`. Config object types can be passed in as strings ("lbvserver"), or looked up from `netscaler.<config object type>.Name()`
The general pattern for NetScaler config objects is some combination of  `AddResource`, `UpdateResource`, `BindResource`, `UnbindResource` and `DeleteResource`. See the [NITRO REST docs](https://docs.citrix.com/en-us/netscaler/11-1/nitro-api/nitro-rest/nitro-rest-general.html) for more information.

## Example

```
package main

import (
        "github.com/chiradeep/go-nitro/config/lb"
        "github.com/chiradeep/go-nitro/netscaler"
)

function main() {
        client, _ := netscaler.NewNitroClientFromEnv()
        lb1 := lb.Lbvserver{
                Name:        "sample_lb",
                Ipv46:       "10.71.136.50",
                Lbmethod:    "ROUNDROBIN",
                Servicetype: "HTTP",
                Port:        8000,
        }
        result, err := client.AddResource(netscaler.Lbvserver.Name(), "sample_lb", &lb1)
        if err == nil {
            client.SaveConfig()
        }
}

```

## Building
The `structs` for the config objects under `config/` are generated from JSON declarations in `jsonconfig`. The JSON itself is generated by reverse engineering the official NITRO Java SDK (see [https://github.com/chiradeep/json-nitro](https://github.com/chiradeep/json-nitro)).  To build the SDK, use `make build`

### Unit Tests
The unit tests are invoked with `make unit`. Note that they are actually functional tests and need a running NetScaler. The tests also need the environment variables `NS_URL`, `NS_LOGIN` and `NS_PASSWORD` to be set.


## TODO
Some REST operations are not yet supported:

* [Pagination & filtering] (https://docs.citrix.com/en-us/netscaler/11-1/nitro-api/nitro-rest/nitro-rest-general/nitro-rest-retrieve-resource-details.html)
* [Count] (https://docs.citrix.com/en-us/netscaler/11-1/nitro-api/nitro-rest/nitro-rest-general/nitro-rest-resource-count.html)
* [Unset] (https://docs.citrix.com/en-us/netscaler/11-1/nitro-api/nitro-rest/nitro-rest-general/nitro-rest-resetting-resource-properties.html)
* [Rename] (https://docs.citrix.com/en-us/netscaler/11-1/nitro-api/nitro-rest/nitro-rest-general/nitro-rest-rename-resource.html)
* [Idempotent Add/Update] (https://docs.citrix.com/en-us/netscaler/11-1/nitro-api/nitro-rest/nitro-rest-usage-scenarios/management-operations-idempotentAPI.html)
* [Link/Unlink](https://docs.citrix.com/en-us/netscaler/11-1/nitro-api/nitro-rest/api-reference/configuration/ssl/sslcertkey.html#link)
* [Import] (https://docs.citrix.com/en-us/netscaler/11-1/nitro-api/nitro-rest/api-reference/configuration/ssl/sslcertfile.html#import)

## Stats
No plan to support the stats API (`http://<netscaler-ip-address>/nitro/v1/stat/<resource-type>`)
`