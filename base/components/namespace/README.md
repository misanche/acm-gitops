# Introduction
This chart allows you to crate namespaces.

# HOW TO USE

This is the structure that follows:
```
Namespaces: Dict array namespaces.

namespaces.<name>: Name of the namespace
namespaces[].<name>.servicemesh: Configuration needed for the servicemesh setup
namespaces[].<name>.servicemesh.enabled: Boolean, true or false to enable
namespaces[].<name>.servicemesh.name: Name of the service mesh.
namespaces[].<name>.assigmentCode: Assigment Code
namespaces[].<name>.crq: Crq
namespaces[].<name>.deliveryManager: Delivery Manager
namespaces[].<name>.ritm: RITM
namespaces[].<name>.serviceRuntimeManager: Service Runtime Manager
namespaces[].<name>.technicalContact: Technical Contact
namespaces[].<name>.displayName: Display Name
namespaces[].<name>.requester: Requester
namespaces[].<name>.annotations[]: Key/Properties for each annotation
namespaces[].<name>.labels: Key/Properties for each label
namespaces[].<name>.limit_range[]: Array of limit ranges defined by Containers,...
namespaces[].<name>.resource_quota[]: Key/Properties for each quota to be set
```

## Values.yaml Example
```
namespaces:
  namespace-creation-test:
    description: Openshift Project delivered via servicenow
    displayName: sma-finance-dev
    requester: XXXX
    annotations:
    labels:
      label1: test
    limit_range:
      - type: Container
        default:
          cpu: 512m
          memory: 512Mi
        defaultRequest:
          cpu: 100m
          memory: 200Mi
    resource_quota:
      configmaps: 100
      persistentvolumeclaims: 100
      replicationcontrollers: 100
      secrets: 100
      services: 100
      services.loadbalancers: 100
´´´