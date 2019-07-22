# Examples
Following directory consists of blueprints examples tested with cloudify-opencontrail-plugin and input files with example values.  

## Prerequisites 
### Plugin upload 
Prior to any blueprint upload, You must upload the plugin, You may do that using cfy:\
     ```
     cfy plugins upload <path_to_plugin>/cloudify_opencontrail_plugin-<version>-py27-none-linux_x86_64.wgn -y <path_to_plugin>/plugin.yaml
     ``` 
     
### Secrets creation
Every blueprint uses inputs for collecting opencontrail REST API values. 
Those might be specified by custom values in inputs file, or taken from secrets store (current approach in inputs file).

To define needed secrets, following commands may be used:
```
    cfy secrets create opencontrail_user -s <username>
    cfy secrets create opencontrail_password -s <password>
    cfy secrets create opencontrail_tenant -s <tenant>
    cfy secrets create opencontrail_ip -s <ip>
    cfy secrets create opencontrail_port -s <port> # Default port for REST API is '8082'
    cfy secrets create opencontrail_domain -s <domain> # Default domain is 'default-domain'
```

## Separated resources 
[Blueprint](separated_resources/blueprint.yaml)

Creation of simple Contrail service with blueprint containing description of each Contrail resource as separate node.
In this example following resources have to exists on OpenContrail before deployment installation:
- **Virtual Router** for which Logical Interface should be provisioned (*vr_name* input) 
- **Physical Interface** on Virtual Router for which Logical Interface should be provisioned (*pif_name* input)

cfy commands:
1) Install blueprint
     ```
     cfy install separated_resources/blueprint.yaml -i separated_resources/inputs.yaml -b plugin_test
     ```
 
2) Check outputs (identifiers of already created resources) 
    ```
    cfy deployments outputs plugin_test
    ```

3) Uninstall blueprint
    ```
    cfy uninstall plugin_test
    ```
    
## Aggregated resources
[Blueprint](aggregated_resources/blueprint.yaml)

This example creates the same setup as in previous section, but it presents usage of nodes that aggregates separated resources (*Service* and *ServiceEndpoint* nodes).
The inputs are the same as in previous example.

cfy commands:
1) Install blueprint
    ```
    cfy install aggregated_resources/blueprint.yaml -i aggregated_resources/inputs.yaml -b plugin_test
    ```
    
2) Check outputs (identifiers of already created resources)
    ```
    cfy deployments outputs plugin_test
    ```
 
3) Try to heal ServiceEndpoint
    ```
    cfy node-instances list | grep plugin_test | grep service_endpoint_1 | awk '{ print $2 }' | xargs -i cfy executions start heal -d plugin_test -p node_instance_id={} 
    ```   
    
5) Uninstall blueprint
    ```
    cfy uninstall plugin_test
    ```
    
## Network Policy 
[Blueprint](network_policy/blueprint.yaml)

Creation of Network Policy that enable traffic flow between two (existing) networks.
In this example virtual networks specified by *right_network_name* and *left_network_name* must exist prior to installation.

cfy commands:
1) Install blueprint
     ```
     cfy install network_policy/blueprint.yaml -i network_policy/inputs.yaml -b network_policy_test
     ```
 
2) Uninstall blueprint
    ```
    cfy uninstall network_policy_test
    
## Service chain v2
[Blueprint](service_chain_v2/blueprint.yaml)

In OpenContrail 3.0, service chain v2 with usage of port tuple was introduced. 
This example presents how service resources can be created (Service template, service instance, port tuple), although this example might not show the real life use case.

cfy commands:
1) Install blueprint
     ```
     cfy install service_chain_v2/blueprint.yaml -i service_chain_v2/inputs.yaml -b service_chain
     ```
 
2) Uninstall blueprint
    ```
    cfy uninstall service_chain