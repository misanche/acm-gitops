# Introduction
This chart allows the creation of ArgoCD Applications dynamically.

## Usage
applications[]: Array of Applications
applications[].<name>: Name of the ArgoCD application
aplications[].<name>.enabled: Create or Remove the ArgoCD application
applications[].<name>.disable_app_finalizer: This parameter allows to not create the ArgoCD finalizers annotations.
applications[].<name>.namespace: Namespace to create the ArgoCD application. Default: cluster-ops
applications[].<name>.cluster_api: Desired cluster to deploy the resources. Default : https://kubernetes.default.svc
applications[].<name>.project: ArgoCD project for the application
applications[].<name>.no_helm: If your application is a Kustomize or yamls.
applications[].<name>.helm_values[]: Array of file directories set as values.
applications[].<name>.values: Inline values.
applications[].<name>.source_path: Location of the kustomize, helm chart.
applications[].<name>.source: Url of the source, helm repository.
applications[].<name>.source_ref: branch name or version.
applications[].<name>.chart_name: Name of the chart if url is set to helm repository
applications[].<name>.sync_policy: ArgoCD sync_policy defined.
applications[].<name>.ignore_differences: Custom ignore differences.
applications[].<name>.sources[]: This property allows you to define multiple sources for an argoCD application, This will be used to specify a helm chart out of the repo and use value files from our repository.

## Values.yaml Example
```
# Default branch
branch: &branch main
# Default Sync policy
sync_policy_true: &sync_policy_true
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
    - Validate=true

# Application array
applications:
  # Application app1 using helm chart and files as values.
  app1:
    enabled: true
    source: https://github.com/redhat/gitops.git
    source_path: base/components/app1
    source_ref: *branch
    sync_policy: *sync_policy_true
    namespace: cluster-ops
    destination: openshift-machine-api
    helm_values:
      - ../../../conf/cluster/autoscaler.yaml
      - ../../../clusters/cluster/cluster1/cluster.yaml
      - ../../../clusters/cluster/cluster1/autoscaler.yaml
  # Application app2 using custom yamls and custom sync policy
  app2:
    enabled: true
    source: https://github.com/redhat/gitops.git
    source_path: base/components/app2
    source_ref: *branch
    sync_policy: 
      syncPolicy:
        managedNamespaceMetadata:
          labels:
            openshift.io/cluster-monitoring: "true"
            argocd.argoproj.io/managed-by: cluster-ops
        automated:
          prune: true
          selfHeal: true
        syncOptions:
        - CreateNamespace=true
        - Validate=true
    namespace: cluster-ops
    destination: openshift-gatekeeper-system
    no_helm: true
  # Application app3 using kustomize and custom ignore differences
  app3:
    enabled: true
    source: https://github.com/redhat/gitops.git
    source_path: base/components/app3/overlay/cluster1/   
    source_ref: *branch
    sync_policy: 
      syncPolicy:
        automated:
          prune: true
          selfHeal: true
        syncOptions:
        - RespectIgnoreDifferences=true
        - Validate=true
    namespace: cluster-ops
    ignore_differences:
      - group: "" # core group is just empty string
        kind: "Namespace"
        jsonPointers:
          - /metadata/labels
          - /metadata/annotations  
    no_helm: true
  # Application app4 using helm char and values inline
  app4:
    enabled: true
    source: https://github.com/redhat/gitops.git
    source_path: base/components/app4
    source_ref: *branch
    sync_policy: *sync_policy_true
    namespace: cluster-ops
    destination: app4
    values:
      userMetrics: true
  #app5:
    enabled: true
    sources:
      - repoURL: https://github.com/redhat/gitops-gitops.git
        helm:
          releaseName: namespace
          valueFiles:
          - $values/clusters/cluster/servicemeshcluster1/namespaces-multirepo.yaml
        path: charts/namespace
        targetRevision: feature22966
      - repoURL: https://github.com/redhat/gitops.git
        targetRevision: feature22966
        ref: values
    sync_policy: *sync_policy_true
    namespace: cluster-ops
    destination: cluster-ops