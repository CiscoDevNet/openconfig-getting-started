# Getting Started With OpenConfig BGP in Cisco IOS XR
The OpenConfig BGP (OC-BGP) model is an alternative to configure and operate BGP in recent Cisco IOS XR releases.  This model provides coverage for a subset of the BGP functionality in Cisco IOS XR.  Configuration outside its scope can be implemented using the native XR models or using the CLI.  Similarly, some portions of the OC-BGP model are not supported in Cisco IOS XR.  Those gaps are documented as model deviations.  The OC-BGP model is closely related to the OpenConfig routing policy (OC-RPOL).  The latter model can be used to implement the policy component of the BGP protocol.  While the OC-BGP and the OC-RPOL models are expected to be used simultaneously in most cases, it is possible to configure BGP and routing policies and using different models (OpenConfig or native models).

## Files Accompanying This Document
```
config/ - Configuration examples (JSON and XML)
oper/ - Examples of operational data (JSON and XML)
filters/ - Examples of model filters
netconf/ - Examples of NETCONF RPCs using this model
tree/ - Tree representation of the model after deviations
```

## Model Support
Initial support for the OC-BGP model was introduced in release 5.3.2.  The examples in this document use the extended model support introduced in release 6.0.0 which is based on model revision 2015-05-15.  These are the modules supported:
```
bgp-multiprotocol.yang
bgp-operational.yang
bgp-policy.yang
bgp-types.yang
bgp.yang
```
The portions of the OC-BGP model that are not supported are documented in the following deviations:
```
cisco-xr-bgp-deviations.yang
cisco-xr-bgp-policy-deviations.yang
```
The entire list of models supported in IOS XR is available in the [GitHub repository](https://github.com/YangModels/yang/tree/master/vendor/cisco).  The model files can also be retrieved from a Cisco IOS XR device using the NETCONF get-schema operation.

The tree representations of the OC-BGP models after applying the Cisco IOS XR deviations can be found at:
```
tree/bgp-2015-05-15-cisco-xr-devs-tree.txt
```

## Data Examples in this Guide
All examples in this guide are presented in JSON and XML format.  As of release 6.0.0, YANG models can be exercised using NETCONF and gRPC.  You can use JSON examples with a gRPC client and the XML examples with a NETCONF client.  The configuration examples in this guide use the policy examples in the [OC-RPOL guide](../policy).

---
## Filtering OC-BGP Data
You can specify filters when retrieving configuration and operational data. The OC-BGP model defines both configuration and operational (state) data. Therefore, the type of data retrieved when using a filter depends on the operation invoked. An empty filter may be used when retrieving configuration. It results on configuration data for all device models (Openconfig and non-OpenConfig) being retrieved.  An empty filter is rejected when retrieving operational data.

The OC-BGP model has three top-level components: `global`, `peer-groups` and `neighbors`.  The most general filter you can define includes the entire data tree for the model (all three top-level components):

XML
```xml
<bgp xmlns="http://openconfig.net/yang/bgp"/>
```
JSON
```json
{
    "bgp:bgp": [
        null
    ]
}
```
The following filter retrieves all OC-BGP `global` data:

XML
```xml
<bgp xmlns="http://openconfig.net/yang/bgp">
  <global/>
</bgp>
```
JSON
```json
{
    "bgp:bgp": {
        "global": [
            null
        ]
    }
}
```
Similarly, the following filter retrieves all OC-BGP data for `neighbors`:

XML
```xml
<bgp xmlns="http://openconfig.net/yang/bgp">
  <neighbors/>
</bgp>
```
JSON
```json
{
    "bgp:bgp": {
        "neighbors": [
            null
        ]
    }
}
```
Finally, the following filter retrieves all OC-BGP data for `peer-groups`:

XML
```xml
<bgp xmlns="http://openconfig.net/yang/bgp">
  <peer-groups/>
</bgp>
```
JSON
```json
{
    "bgp:bgp": {
        "peer-groups": [
            null
        ]
    }
}
```

---
## Getting Started with OC-BGP Configuration
### Basic iBGP configuration
The following configuration example defines an IBGP peer group and associates that group with a BGP neighbor.  The group enables IPv4 routing, sets the address of interface Loopback0 as the source address and applies an output policy:

XML
```xml
<?xml version="1.0"?>
<bgp xmlns="http://openconfig.net/yang/bgp">
  <global>
    <config>
      <as>65001</as>
    </config>
    <afi-safis>
      <afi-safi>
        <afi-safi-name>ipv4-unicast</afi-safi-name>
        <config>
          <afi-safi-name>ipv4-unicast</afi-safi-name>
          <enabled>true</enabled>
        </config>
      </afi-safi>
    </afi-safis>
  </global>
  <peer-groups>
    <peer-group>
      <peer-group-name>IBGP</peer-group-name>
      <config>
        <peer-group-name>IBGP</peer-group-name>
        <peer-as>65001</peer-as>
      </config>
      <transport>
        <config>
          <local-address>Loopback0</local-address>
        </config>
      </transport>
      <afi-safis>
        <afi-safi>
          <afi-safi-name>ipv4-unicast</afi-safi-name>
          <config>
            <afi-safi-name>ipv4-unicast</afi-safi-name>
            <enabled>true</enabled>
          </config>
          <apply-policy>
            <config>
              <export-policy>POLICY2</export-policy>
            </config>
          </apply-policy>
        </afi-safi>
      </afi-safis>
    </peer-group>
  </peer-groups>
  <neighbors>
    <neighbor>
      <neighbor-address>172.16.255.3</neighbor-address>
      <config>
        <neighbor-address>172.16.255.3</neighbor-address>
        <peer-group>IBGP</peer-group>
      </config>
    </neighbor>
  </neighbors>
</bgp>
```
JSON
```json
{
 "bgp:bgp": {
  "global": {
   "config": {
    "as": 65001
   },
   "afi-safis": {
    "afi-safi": [
     {
      "afi-safi-name": "ipv4-unicast",
      "config": {
       "afi-safi-name": "ipv4-unicast",
       "enabled": true
      }
     }
    ]
   }
  },
  "peer-groups": {
   "peer-group": [
    {
     "peer-group-name": "IBGP",
     "config": {
      "peer-group-name": "IBGP",
      "peer-as": 65001
     },
     "transport": {
      "config": {
       "local-address": "Loopback0"
      }
     },
     "afi-safis": {
      "afi-safi": [
       {
        "afi-safi-name": "ipv4-unicast",
        "config": {
         "afi-safi-name": "ipv4-unicast",
         "enabled": true
        },
        "apply-policy": {
         "config": {
          "export-policy": [
           "POLICY2"
          ]
         }
        }
       }
      ]
     }
    }
   ]
  },
  "neighbors": {
   "neighbor": [
    {
     "neighbor-address": "172.16.255.3",
     "config": {
      "neighbor-address": "172.16.255.3",
      "peer-group": "IBGP"
     }
    }
   ]
  }
 }
}
```
The previous OC-BGP configuration results in the following CLI configuration:
```
router bgp 65001
 address-family ipv4 unicast
 !
 neighbor-group IBGP
  remote-as 65001
  update-source Loopback0
  address-family ipv4 unicast
   route-policy POLICY2 out
  !
 !
 neighbor 172.16.255.3
  use neighbor-group IBGP
 !
!
```

In this particular case, the session is established with the iBGP neighbor and two prefixes are received. The following sample output illustrates the entire operational state associated with this particular iBGP neighbor. Note that the XML output includes configuration data while the JSON output does not.  The difference in behavior is the result of the difference in semantics between the NETCONF GET RPC and the GET-OPER RPC in Cisco IDL for gRPC:

XML
```xml
<?xml version="1.0"?>
<bgp xmlns="http://openconfig.net/yang/bgp">
  <global>
    <config>
      <as>65001</as>
    </config>
    <state>
      <as>65001</as>
      <total-paths>2</total-paths>
      <total-prefixes>2</total-prefixes>
    </state>
    <afi-safis>
      <afi-safi>
        <afi-safi-name>ipv4-unicast</afi-safi-name>
        <config>
          <afi-safi-name>ipv4-unicast</afi-safi-name>
          <enabled>true</enabled>
        </config>
        <state>
          <afi-safi-name>ipv4-unicast</afi-safi-name>
          <enabled>true</enabled>
          <total-paths>2</total-paths>
          <total-prefixes>2</total-prefixes>
        </state>
      </afi-safi>
    </afi-safis>
  </global>
  <peer-groups>
    <peer-group>
      <peer-group-name>IBGP</peer-group-name>
      <config>
        <peer-group-name>IBGP</peer-group-name>
        <peer-as>65001</peer-as>
      </config>
      <state>
        <peer-group-name>IBGP</peer-group-name>
        <peer-as>65001</peer-as>
      </state>
      <transport>
        <config>
          <local-address>Loopback0</local-address>
        </config>
        <state>
          <local-address>Loopback0</local-address>
        </state>
      </transport>
      <afi-safis>
        <afi-safi>
          <afi-safi-name>ipv4-unicast</afi-safi-name>
          <config>
            <afi-safi-name>ipv4-unicast</afi-safi-name>
            <enabled>true</enabled>
          </config>
          <state>
            <afi-safi-name>ipv4-unicast</afi-safi-name>
            <enabled>true</enabled>
          </state>
          <apply-policy>
            <config>
              <export-policy>POLICY2</export-policy>
            </config>
            <state>
              <export-policy>POLICY2</export-policy>
            </state>
          </apply-policy>
        </afi-safi>
      </afi-safis>
    </peer-group>
  </peer-groups>
  <neighbors>
    <neighbor>
      <neighbor-address>172.16.255.3</neighbor-address>
      <config>
        <neighbor-address>172.16.255.3</neighbor-address>
        <peer-group>IBGP</peer-group>
      </config>
      <state>
        <neighbor-address>172.16.255.3</neighbor-address>
        <peer-group>IBGP</peer-group>
        <queues>
          <input>0</input>
          <output>0</output>
        </queues>
        <session-state>bgp-st-estab</session-state>
        <supported-capabilities>MPBGP</supported-capabilities>
        <messages>
          <sent>
            <NOTIFICATION>0</NOTIFICATION>
            <UPDATE>1</UPDATE>
          </sent>
          <received>
            <NOTIFICATION>0</NOTIFICATION>
            <UPDATE>3</UPDATE>
          </received>
        </messages>
      </state>
      <transport>
        <state>
          <local-port>21344</local-port>
          <remote-address>172.16.255.3</remote-address>
          <remote-port>179</remote-port>
        </state>
      </transport>
      <timers>
        <state>
          <negotiated-hold-time>180</negotiated-hold-time>
        </state>
      </timers>
      <afi-safis>
        <afi-safi>
          <afi-safi-name>ipv4-unicast</afi-safi-name>
          <state>
            <active>true</active>
            <prefixes>
              <received>2</received>
              <sent>0</sent>
            </prefixes>
          </state>
        </afi-safi>
      </afi-safis>
      <graceful-restart>
        <state>
          <peer-restart-time>120</peer-restart-time>
        </state>
      </graceful-restart>
    </neighbor>
  </neighbors>
</bgp>
```
JSON
```json
{
 "bgp:bgp": {
  "global": {
   "state": {
    "as": 65001,
    "total-paths": 2,
    "total-prefixes": 2
   },
   "afi-safis": {
    "afi-safi": [
     {
      "afi-safi-name": "ipv4-unicast",
      "state": {
       "afi-safi-name": "ipv4-unicast",
       "enabled": true,
       "total-paths": 2,
       "total-prefixes": 2
      }
     }
    ]
   }
  },
  "peer-groups": {
   "peer-group": [
    {
     "peer-group-name": "IBGP",
     "state": {
      "peer-group-name": "IBGP",
      "peer-as": 65001
     },
     "transport": {
      "state": {
       "local-address": "Loopback0"
      }
     },
     "afi-safis": {
      "afi-safi": [
       {
        "afi-safi-name": "ipv4-unicast",
        "state": {
         "afi-safi-name": "ipv4-unicast",
         "enabled": true
        },
        "apply-policy": {
         "state": {
          "export-policy": [
           "POLICY2"
          ]
         }
        }
       }
      ]
     }
    }
   ]
  },
  "neighbors": {
   "neighbor": [
    {
     "neighbor-address": "172.16.255.3",
     "state": {
      "neighbor-address": "172.16.255.3",
      "peer-group": "IBGP",
      "queues": {
       "input": 0,
       "output": 0
      },
      "session-state": "bgp-st-estab",
      "supported-capabilities": [
       "MPBGP"
      ],
      "messages": {
       "sent": {
        "NOTIFICATION": 0,
        "UPDATE": 1
       },
       "received": {
        "NOTIFICATION": 0,
        "UPDATE": 3
       }
      }
     },
     "transport": {
      "state": {
       "local-port": 21344,
       "remote-address": "172.16.255.3",
       "remote-port": 179
      }
     },
     "timers": {
      "state": {
       "negotiated-hold-time": 180
      }
     },
     "afi-safis": {
      "afi-safi": [
       {
        "afi-safi-name": "ipv4-unicast",
        "state": {
         "active": true,
         "prefixes": {
          "received": 2,
          "sent": 0
         }
        }
       }
      ]
     },
     "graceful-restart": {
      "state": {
       "peer-restart-time": 120
      }
     }
    }
   ]
  }
 }
}
```
### Basic eBGP Configuration
The following configuration example defines an EBGP peer group and associates that group with a BGP neighbor.  The group enables IPv4 routing and applies a routing policy in both directions:

XML
```xml
<?xml version="1.0"?>
<bgp xmlns="http://openconfig.net/yang/bgp">
  <global>
    <config>
      <as>65001</as>
    </config>
    <afi-safis>
      <afi-safi>
        <afi-safi-name>ipv4-unicast</afi-safi-name>
        <config>
          <afi-safi-name>ipv4-unicast</afi-safi-name>
          <enabled>true</enabled>
        </config>
      </afi-safi>
    </afi-safis>
  </global>
  <peer-groups>
    <peer-group>
      <peer-group-name>EBGP</peer-group-name>
      <config>
        <peer-group-name>EBGP</peer-group-name>
        <peer-as>65002</peer-as>
      </config>
      <afi-safis>
        <afi-safi>
          <afi-safi-name>ipv4-unicast</afi-safi-name>
          <config>
            <afi-safi-name>ipv4-unicast</afi-safi-name>
            <enabled>true</enabled>
          </config>
          <apply-policy>
            <config>
              <import-policy>POLICY3</import-policy>
              <export-policy>POLICY1</export-policy>
            </config>
          </apply-policy>
        </afi-safi>
      </afi-safis>
    </peer-group>
  </peer-groups>
  <neighbors>
    <neighbor>
      <neighbor-address>192.168.1.1</neighbor-address>
      <config>
        <neighbor-address>192.168.1.1</neighbor-address>
        <peer-group>EBGP</peer-group>
      </config>
    </neighbor>
  </neighbors>
</bgp>
```
JSON
```json
{
 "bgp:bgp": {
  "global": {
   "config": {
    "as": 65001
   },
   "afi-safis": {
    "afi-safi": [
     {
      "afi-safi-name": "ipv4-unicast",
      "config": {
       "afi-safi-name": "ipv4-unicast",
       "enabled": true
      }
     }
    ]
   }
  },
  "peer-groups": {
   "peer-group": [
    {
     "peer-group-name": "EBGP",
     "config": {
      "peer-group-name": "EBGP",
      "peer-as": 65002
     },
     "afi-safis": {
      "afi-safi": [
       {
        "afi-safi-name": "ipv4-unicast",
        "config": {
         "afi-safi-name": "ipv4-unicast",
         "enabled": true
        },
        "apply-policy": {
         "config": {
          "import-policy": [
           "POLICY3"
          ],
          "export-policy": [
           "POLICY1"
          ]
         }
        }
       }
      ]
     }
    }
   ]
  },
  "neighbors": {
   "neighbor": [
    {
     "neighbor-address": "192.168.1.1",
     "config": {
      "neighbor-address": "192.168.1.1",
      "peer-group": "EBGP"
     }
    }
   ]
  }
 }
}
```
The OC-BGP configuration above results in the following CLI configuration:
```
router bgp 65001
 address-family ipv4 unicast
 !
 neighbor-group EBGP
  remote-as 65002
  address-family ipv4 unicast
   route-policy POLICY3 in
   route-policy POLICY1 out
  !
 !
 neighbor 192.168.1.1
  use neighbor-group EBGP
 !
!
```
## Configuring BGP Functionality Outside OC-BGP
Some of the BGP functionality in Cisco IOS XR is not configurable using OC-BGP.  This situation can occur because the OC-BGP model does not define the functionality, defines it at a different granularity and cannot be mapped, or defines it using a completely different configuration approach (e.g. a routing policy).  In these cases, the functionality can be configured using native models or the CLI.  The same two mechanism are available for any BGP operational data not included in the OC-BGP model.  The CLI commands and their data can be exchanged using gRPC or the interactive command line interpreter.

As an example, granular control for the storage of received BGP updates cannot be configured in Cisco IOS XR using the OC-BGP model.  Such control allows users to configure different behaviors depending on the capability of the neighbor to refresh routes and the user preference for memory consumption.  The following CLI enables the local storage of updates received from a neighbor regardless of its capability to refresh routes:
```
router bgp 65001
 neighbor-group IBGP
  address-family ipv4 unicast
   soft-reconfiguration inbound always
  !
 !
!
```
Such configuration can be realized using the native BGP model in Cisco IOS XR:
XML
```xml
<?xml version="1.0"?>
<bgp xmlns="http://cisco.com/ns/yang/Cisco-IOS-XR-ipv4-bgp-cfg">
  <instance>
    <instance-name>default</instance-name>
    <instance-as>
      <as>0</as>
      <four-byte-as>
        <as>65001</as>
        <bgp-running/>
        <default-vrf>
          <bgp-entity>
            <neighbor-groups>
              <neighbor-group>
                <neighbor-group-name>IBGP</neighbor-group-name>
                <create/>
                <neighbor-group-afs>
                  <neighbor-group-af>
                    <af-name>ipv4-unicast</af-name>
                    <activate/>
                    <soft-reconfiguration>
                      <inbound-soft>true</inbound-soft>
                      <soft-always>true</soft-always>
                    </soft-reconfiguration>
                  </neighbor-group-af>
                </neighbor-group-afs>
              </neighbor-group>
            </neighbor-groups>
          </bgp-entity>
        </default-vrf>
      </four-byte-as>
    </instance-as>
  </instance>
</bgp>
```
JSON
```json
{
 "Cisco-IOS-XR-ipv4-bgp-cfg:bgp": {
  "instance": [
   {
    "instance-name": "default",
    "instance-as": [
     {
      "as": 0,
      "four-byte-as": [
       {
        "as": 65001,
        "bgp-running": [
         null
        ],
        "default-vrf": {
         "bgp-entity": {
          "neighbor-groups": {
           "neighbor-group": [
            {
             "neighbor-group-name": "IBGP",
             "create": [
              null
             ],
             "neighbor-group-afs": {
              "neighbor-group-af": [
               {
                "af-name": "ipv4-unicast",
                "activate": [
                 null
                ],
                "soft-reconfiguration": {
                 "inbound-soft": true,
                 "soft-always": true
                }
               }
              ]
             }
            }
           ]
          }
         }
        }
       }
      ]
     }
    ]
   }
  ]
 }
}
```
Setting the next-hop attribute to the local router address is an example of BGP functionality that cannot be configured excusively with the OC-BGP model and requires policy configuration.  Cisco IOS XR allows you to set the next hop to the local address for a neighbor group using the following CLI configuration:
```
router bgp 65001
 neighbor-group IBGP
  address-family ipv4 unicast
   next-hop-self
  !
 !
!
```
Similar functionality can be achieved using an OC-RPOL policy and associating it with a peer group using the OC-BGP model.

XML
```xml
<?xml version="1.0"?>
<routing-policy xmlns="http://openconfig.net/yang/routing-policy">
  <policy-definitions>
    <policy-definition>
      <name>POLICY4</name>
      <statements>
        <statement>
          <name>next-hop-self</name>
          <actions>
            <bgp-actions xmlns="http://openconfig.net/yang/bgp-policy">
              <set-next-hop>SELF</set-next-hop>
            </bgp-actions>
            <accept-route/>
          </actions>
        </statement>
      </statements>
    </policy-definition>
  </policy-definitions>
</routing-policy>

<bgp xmlns="http://openconfig.net/yang/bgp">
  <global>
    <config>
      <as>65001</as>
    </config>
  </global>
  <peer-groups>
    <peer-group>
      <peer-group-name>IBGP</peer-group-name>
      <config>
        <peer-group-name>IBGP</peer-group-name>
      </config>
      <afi-safis>
        <afi-safi>
          <afi-safi-name>ipv4-unicast</afi-safi-name>
          <config>
            <afi-safi-name>ipv4-unicast</afi-safi-name>
            <enabled>true</enabled>
          </config>
          <apply-policy>
            <config>
              <export-policy>POLICY4</export-policy>
            </config>
          </apply-policy>
        </afi-safi>
      </afi-safis>
    </peer-group>
  </peer-groups>
</bgp>
```
JSON
```json
{
 "routing-policy:routing-policy": {
   "policy-definitions": {
    "policy-definition": [
    {
     "name": "POLICY4",
     "statements": {
      "statement": [
       {
        "name": "next-hop-self",
        "actions": {
         "bgp-policy:bgp-actions": {
          "set-next-hop": "SELF"
         },
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
 },
 "bgp:bgp": {
  "global": {
   "config": {
    "as": 65001
   }
  },
  "peer-groups": {
   "peer-group": [
    {
     "peer-group-name": "IBGP",
     "config": {
      "peer-group-name": "IBGP"
     },
     "afi-safis": {
      "afi-safi": [
       {
        "afi-safi-name": "ipv4-unicast",
        "config": {
         "afi-safi-name": "ipv4-unicast",
         "enabled": true
        },
        "apply-policy": {
         "config": {
          "export-policy": [
           "POLICY4"
          ]
         }
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
The configuration above results in the following CLI configuration which is equivalent to the previous CLI example:
```
route-policy POLICY4
  #statement-name next-hop-self
  set next-hop self
  done
end-policy
!
router bgp 65001
 neighbor-group IBGP
  address-family ipv4 unicast
   route-policy POLICY4 out
  !
 !
!
```

### Caveats
OpenConfig models are not compatible with FlexCLI (apply groups).

---
