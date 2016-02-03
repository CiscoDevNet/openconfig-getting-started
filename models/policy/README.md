# Getting Started With OpenConfig Routing Policy in Cisco IOS XR
The OpenConfig routing policy (OC-RPOL) model is an alternative to configure and operate routing policy in recent Cisco IOS XR releases.  This model provides coverage for a subset of the routing policy functionality in Cisco IOS XR.  Configuration outside its scope can be implemented using the native XR models or using the CLI.  Similarly, some portions of the OC-RPOL model are not supported in Cisco IOS XR.  Those gaps are documented as model deviations.  The model is closely related to the OpenConfig BGP (OC-BGP) model which can be used to manage the BGP protocol on Cisco IOS XR.  While the OC-RPOL and the OC-BGP models are expected to be used simultaneously in most cases, it is possible to configure routing policies and BGP using different models (OpenConfig or native models).

## Files Accompanying This Document
```
config/ - Configuration examples (JSON and XML)
oper/ - Examples of operational data (JSON and XML)
filters/ - Examples of model filters
netconf/ - Examples of NETCONF RPCs using this model
tree/ - Tree representation of the model after deviations
```

## Model Support
Initial support for the OC-RPOL model was introduced in release 5.3.2.  The examples in this document use the extended model support introduced in release 6.0.0 which is based on model revision 2015-05-15.  These are the modules supported:
```
policy-types.yang
routing-policy.yang
```
The portions of the OC-RPOL model that are not supported are documented in the following deviations:
```
cisco-xr-routing-policy-deviations.yang
```
The entire list of models supported in IOS XR is available in the [GitHub repository](https://github.com/YangModels/yang/tree/master/vendor/cisco).  The model files can also be retrieved from a Cisco IOS XR device using the NETCONF get-schema operation.

The tree representations of the OC-RPOL models after applying the Cisco IOS XR deviations can be found at:
```
tree/routing-policy-2015-05-15-cisco-xr-devs-tree.txt
```

## Data Examples in this Guide
All examples in this guide are presented in JSON and XML format.  As of release 6.0.0, YANG models can be exercised using NETCONF and gRPC.  You can use JSON examples with a gRPC client and the XML examples with a NETCONF client.   The configuration examples in this guide are applied in the [OC-BGP guide](../bgp). 

---
## Filtering OC-RPOL Data
You can specify filters when retrieving configuration and operational data. An empty filter may be used when retrieving configuration. It results on data for all device models (Openconfig and non-OpenConfig) being retrieved.  An empty filter is rejected when retrieving operational data.

The OC-RPOL model has two top-level components: `defined-sets` and `policy-definitions`.  The most general filter you can define includes the entire data tree for the model (both set and policy definitions):

XML
```xml
<routing-policy xmlns="http://openconfig.net/yang/routing-policy"/>
```
JSON
```json
{
    "routing-policy:routing-policy": [
        null
    ]
}
```
The following filter retrieves all OC-RPOL data for `defined-sets`:

XML
```xml
<routing-policy xmlns="http://openconfig.net/yang/routing-policy">
  <defined-sets/>
</routing-policy>
```
JSON
```json
{
    "routing-policy:routing-policy": {
        "defined-sets": [
            null
        ]
    }
}
```
Similarly, the following model path retrieves all OC-RPOL data for `policy-definitions`:

XML
```xml
<routing-policy xmlns="http://openconfig.net/yang/routing-policy">
  <policy-definitions/>
</routing-policy>
```
JSON
```json
{
    "routing-policy:routing-policy": {
        "policy-definitions": [
            null
        ]
    }
}
```

---
## Getting Started with OC-RPOL Configuration
The OC-RPOL model specifies set and policy definitions.  Set definitions may include prefix, neighbor, tag or AS-path sets.  Policy definitions specify a list of policies where each policy contains a list of statement with optional conditions and a mandatory action.  The following configuration example defines a routing policy that accepts all prefixes:

XML
```xml
<?xml version="1.0"?>
<routing-policy xmlns="http://openconfig.net/yang/routing-policy">
  <policy-definitions>
    <policy-definition>
      <name>POLICY1</name>
      <statements>
        <statement>
          <name>accept</name>
          <actions>
            <accept-route/>
          </actions>
        </statement>
      </statements>
    </policy-definition>
  </policy-definitions>
</routing-policy>
```
JSON
```json
{
 "routing-policy:routing-policy": {
   "policy-definitions": {
    "policy-definition": [
     {   
      "name": "POLICY1",
      "statements": {
       "statement": [
        {
         "name": "accept route",
         "actions": {
          "accept-route": [
           null
         ]
        }
       }
      ]
     }
    }
   ]
  }
 }
}
```
The OC-RPOL configuration above results in the following CLI configuration:
```
route-policy POLICY1
  #statement-name accept route
  done
end-policy
!
```
The following configuration example defines an AS-path set, a community set and a policy.  The latter accepts the prefixes matching the community set or the AS-path set.  All other prefixes are dropped.  The prefixes matching the AS-path also have their BGP local preference set to a value of 50:

XML
```xml
<?xml version="1.0"?>
<routing-policy xmlns="http://openconfig.net/yang/routing-policy">
  <defined-sets>
    <bgp-defined-sets xmlns="http://openconfig.net/yang/bgp-policy">
      <as-path-sets>
        <as-path-set>
          <as-path-set-name>AS-PATH-SET1</as-path-set-name>
          <as-path-set-member>^65172</as-path-set-member>
        </as-path-set>
      </as-path-sets>
      <community-sets>
        <community-set>
          <community-set-name>COMMUNITY-SET1</community-set-name>
          <community-member>ios-regex '^65172:17...$'</community-member>
          <community-member>65172:16001</community-member>
        </community-set>
      </community-sets>
    </bgp-defined-sets>
  </defined-sets>
  <policy-definitions>
    <policy-definition>
      <name>POLICY2</name>
      <statements>
        <statement>
          <name>community-set1</name>
          <conditions>
            <bgp-conditions xmlns="http://openconfig.net/yang/bgp-policy">
              <match-community-set>
                <community-set>COMMUNITY-SET1</community-set>
                <match-set-options>ALL</match-set-options>
              </match-community-set>
            </bgp-conditions>
          </conditions>
          <actions>
            <accept-route/>
          </actions>
        </statement>
        <statement>
          <name>as-path-set1</name>
          <conditions>
            <bgp-conditions xmlns="http://openconfig.net/yang/bgp-policy">
              <match-as-path-set>
                <as-path-set>AS-PATH-SET1</as-path-set>
                <match-set-options>ANY</match-set-options>
              </match-as-path-set>
            </bgp-conditions>
          </conditions>
          <actions>
            <bgp-actions xmlns="http://openconfig.net/yang/bgp-policy">
              <set-local-pref>50</set-local-pref>
            </bgp-actions>
            <accept-route/>
          </actions>
        </statement>
        <statement>
          <name>reject</name>
          <actions>
            <reject-route/>
          </actions>
        </statement>
      </statements>
    </policy-definition>
  </policy-definitions>
</routing-policy>
```
JSON
```json
{
 "routing-policy:routing-policy": {
  "defined-sets": {
   "bgp-policy:bgp-defined-sets": {
    "as-path-sets": {
     "as-path-set": [
      {
       "as-path-set-name": "AS-PATH-SET1",
       "as-path-set-member": [
        "^65172"
       ]
      }
     ]
    },
    "community-sets": {
     "community-set": [
      {
       "community-set-name": "COMMUNITY-SET1",
       "community-member": [
        "ios-regex '^65172:17...$'",
        "65172:16001"
       ]
      }
     ]
    }
   }
  },
  "policy-definitions": {
   "policy-definition": [
    {
     "name": "POLICY2",
     "statements": {
      "statement": [
       {
        "name": "community-set1",
        "conditions": {
         "bgp-policy:bgp-conditions": {
          "match-community-set": {
           "community-set": "COMMUNITY-SET1",
           "match-set-options": "ALL"
          }
         }
        },
        "actions": {
         "accept-route": [
          null
         ]
        }
       },
       {
        "name": "as-path-set1",
        "conditions": {
         "bgp-policy:bgp-conditions": {
          "match-as-path-set": {
           "as-path-set": "AS-PATH-SET1",
           "match-set-options": "ANY"
          }
         }
        },
        "actions": {
         "bgp-policy:bgp-actions": {
          "set-local-pref": 50
         },
         "accept-route": [
          null
         ]
        }
       },
       {
        "name": "reject route",
        "actions": {
         "reject-route": [
          null
         ]
        }
       }
      ]
     }
    }
   ]
  }
 }
}
```
The previous OC-RPOL configuration results in the following CLI configuration:
```
as-path-set AS-PATH-SET1
  ios-regex '^65172'
end-set
!
community-set COMMUNITY-SET1
  ios-regex '^65172:17...$',
  65172:16001
end-set
!
route-policy POLICY2
  #statement-name community-set1
  if community matches-every COMMUNITY-SET1 then
    done
  endif
  #statement-name as-path-set1
  if as-path in AS-PATH-SET1 then
    set local-preference 50
    done  
  endif   
  #statement-name reject route
  drop    
end-policy
!         
```
The following configuration example defines a prefix set, a community set and a policy.  The latter accepts the prefixes matching the prefix set and drops all other prefixes.  The prefixes matching the prefix set also have their BGP local preference and a community set:

XML
```xml
<?xml version="1.0"?>
<routing-policy xmlns="http://openconfig.net/yang/routing-policy">
  <defined-sets>
    <prefix-sets>
      <prefix-set>
        <prefix-set-name>PREFIX-SET1</prefix-set-name>
        <prefix>
          <ip-prefix>10.0.0.0/16</ip-prefix>
          <masklength-range>24..32</masklength-range>
        </prefix>
        <prefix>
          <ip-prefix>172.0.0.0/8</ip-prefix>
          <masklength-range>16..32</masklength-range>
        </prefix>
      </prefix-set>
    </prefix-sets>
    <bgp-defined-sets xmlns="http://openconfig.net/yang/bgp-policy">
      <community-sets>
        <community-set>
          <community-set-name>COMMUNITY-SET2</community-set-name>
          <community-member>65172:17001</community-member>
        </community-set>
      </community-sets>
    </bgp-defined-sets>
  </defined-sets>
  <policy-definitions>
    <policy-definition>
      <name>POLICY3</name>
      <statements>
        <statement>
          <name>prefix-set1</name>
          <conditions>
            <match-prefix-set>
              <prefix-set>PREFIX-SET1</prefix-set>
              <match-set-options>ANY</match-set-options>
            </match-prefix-set>
          </conditions>
          <actions>
            <bgp-actions xmlns="http://openconfig.net/yang/bgp-policy">
              <set-local-pref>1000</set-local-pref>
              <set-community>
                <community-set-ref>COMMUNITY-SET2</community-set-ref>
              </set-community>
            </bgp-actions>
            <accept-route/>
          </actions>
        </statement>
        <statement>
          <name>reject</name>
          <actions>
            <reject-route/>
          </actions>
        </statement>
      </statements>
    </policy-definition>
  </policy-definitions>
</routing-policy>
```
JSON
```json
{
 "routing-policy:routing-policy": {
  "defined-sets": {
   "prefix-sets": {
    "prefix-set": [
     {
      "prefix-set-name": "PREFIX-SET1",
      "prefix": [
       {
        "ip-prefix": "10.0.0.0/16",
        "masklength-range": "24..32"
       },
       {
        "ip-prefix": "10.1.0.0/19",
        "masklength-range": "30..32"
       }
      ]
     }
    ]
   },
   "bgp-policy:bgp-defined-sets": {
    "community-sets": {
     "community-set": [
      {   
       "community-set-name": "COMMUNITY-SET2",
       "community-member": [
        "65172:17001"
       ]
      }   
     ]   
    }   
   }   
  },
  "policy-definitions": {
   "policy-definition": [
    {
     "name": "POLICY3",
     "statements": {
      "statement": [
       {
        "name": "prefix-set1",
        "conditions": {
         "match-prefix-set": {
          "prefix-set": "PREFIX-SET1",
          "match-set-options": "ANY"
         }
        },
        "actions": {
         "bgp-policy:bgp-actions": {
          "set-local-pref": 1000,
          "set-community": {
           "community-set-ref": "COMMUNITY-SET2"
          }
         },
         "accept-route": [
          null
         ]
        }
       },
       {
        "name": "reject",
        "actions": {
         "reject-route": [
          null
         ]
        }
       }
      ]
     }
    }
   ]
  }
 }
}
```
The OC-RPOL configuration above results in the following CLI configuration:
```
prefix-set PREFIX-SET1
  10.0.0.0/16 ge 24 le 32,
  172.0.0.0/8 ge 16 le 32
end-set
!
community-set COMMUNITY-SET2
  65172:17001
end-set
!
route-policy POLICY3
  #statement-name prefix-set1
  if destination in PREFIX-SET1 then
    set local-preference 1000
    set community COMMUNITY-SET2
    done
  endif
  #statement-name reject
  drop
end-policy
!
```

### Caveats
* When a route policy is defined or modified via the OC-RPOL model, the entire policy must be specified.
* The BGP implementation in Cisco IOS XR only accepts a single policy at a given attachment point.  If a configuration specifies a list of policies, the last entry in the list is applied and the other policies are ignored.  In order to apply multiple policies, the configuration must use a parent policy that groups the other policies.  That parent policy can then be attached at the desired attachment point.
* OpenConfig models are not compatible with FlexCLI (apply groups).

---
