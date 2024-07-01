---
title: "TKG 서비스 클러스터 정의 완벽 가이드"
description: ""
coverImage: "/assets/img/2024-07-01-AcomprehensiveguidetoTKGServiceClusterdefinition_0.png"
date: 2024-07-01 21:10
ogImage: 
  url: /assets/img/2024-07-01-AcomprehensiveguidetoTKGServiceClusterdefinition_0.png
tag: Tech
originalTitle: "A comprehensive guide to TKG Service Cluster definition"
link: "https://medium.com/@navneet-verma/a-comprehensive-guide-to-tkg-service-cluster-definition-fce2d3903b10"
---


지금부터 TKG 클러스터 리소스 정의 매니페스트의 세계로 뛰어들 준비를 해보세요! 이 설계도는 노드 구성, 저장 정책 및 네트워크 설정을 포함한 완벽한 클러스터를 정의합니다. 사용자 정의 가능한 옵션과 확장된 기능 세트로, 새 클러스터를 프로비저닝하는 것이 이제까지보다 쉬워졌습니다. 이 문서는 이 개념을 해체하고 주요 측면을 강조하며 전체 클러스터 구성 프로세스를 어떻게 간소화하는지 보여줍니다.

```js
apiVersion: cluster.x-k8s.io/v1beta1
kind: Cluster
metadata:
  name: workload-vsphere-tkg4
spec:
  clusterNetwork:
    apiServerPort: 6443
    pods:
      cidrBlocks: ["192.168.32.0/20"]
    serviceDomain: "cluster.local"
    services:
      cidrBlocks: ["192.168.0.0/20"]
  paused: false
  topology:
    class: tanzukubernetescluster
    version: v1.28.8---vmware.1-fips.1-tkg.2
    controlPlane:
      metadata:
        annotations:
          run.tanzu.vmware.com/resolve-os-image: os-name=ubuntu
        labels:
          my-custom-label-key: my-custom-label-value
      replicas: 1
    workers:
      machineDeployments:
      - class: node-pool
        metadata:
          annotations:
            cluster.x-k8s.io/cluster-api-autoscaler-node-group-max-size: "3"
            cluster.x-k8s.io/cluster-api-autoscaler-node-group-min-size: "1"
            run.tanzu.vmware.com/resolve-os-image: os-name=ubuntu
          labels:
            my-custom-label-key: my-custom-label-value
        minReadySeconds: 0
        name: node-pool-1
        variables:
          overrides:
          - name: vmClass
            value: best-effort-medium

...

## spec

<div class="content-ad"></div>

apiVersion: cluster.x-k8s.io/v1beta1
kind: Cluster
metadata:
  name: 
spec:
  clusterNetwork:
  topology:
  paused: false

매니페스트 내의 개별 섹션과 오브젝트를 살펴보겠습니다. spec 섹션은 clusterNetwork와 topology 정의 두 가지 주요 부분으로 구성됩니다.

- paused 부울 필드는 필수가 아니며 기본값은 false입니다. 이 값을 true로 설정하면 클러스터 개선이 일시 중지됩니다.

## spec.clusterNetwork
```

<div class="content-ad"></div>

```yaml
clusterNetwork:
  apiServerPort: 6443
  pods:
    cidrBlocks: ["192.168.32.0/20"]
  serviceDomain: "cluster.local"
  services:
    cidrBlocks: ["192.168.0.0/20"]
```

이제 clusterNetwork 사항을 좀 더 자세히 살펴봅시다. 이 섹션에서는 클러스터 사양의 네트워킹 매개변수를 정의합니다.

- apiServerPort 필드는 기본값이 6443이며, API 서버가 작동할 포트를 지정합니다.
- pod.cidrBlocks 값은 256개의 IP(/24) 블록으로 나뉘며, 각 노드에 CNI에 의해 할당됩니다. CIDR 블록이 적절한 크기로 조정되도록 해야 합니다. 그렇지 않으면 CNI가 사용 가능한 IP 주소 공간을 고갈시킬 수 있어 새로운 노드를 추가하거나 노드를 롤아웃하는 데 실패할 수 있습니다.
- 클러스터는 서비스의 종류가 ClusterIP인 경우 services.cidrBlocks을 사용합니다.

## spec.topology


<div class="content-ad"></div>

```yaml
topology:
  class: tanzukubernetescluster
  version: v1.28.8---vmware.1-fips.1-tkg.2
  controlPlane:
  workers:
  variables:
```

위상학 섹션은 클러스터 오브젝트의 속성을 지정하는데, 버전, ClusterClass 관련 정보, 그리고 제어 평면 및 워커 노드에 대한 구체적인 내용을 포함합니다.

- class는 클러스터 구성 중 클러스터 API가 사용할 클러스터 클래스를 나타냅니다. 각 vSphere 네임스페이스에는 기본 ClusterClass인 tanzukubernetescluster가 있습니다. 사용자는 기본 ClusterClass를 활용하거나 복제하여 사용자 정의 ClusterClass를 만들 수 있습니다.

```shell
$ k get clusterclass -n namespace
NAME                     AGE
tanzukubernetescluster   3d6h
```


<div class="content-ad"></div>

- 이름에서 알 수 있듯이, 버전은 클러스터 API가 TKG 클러스터를 배포하는 데 사용할 TKr 버전을 지정합니다.

```js
$ kubectl get tkr -l '!run.tanzu.vmware.com/legacy-tkr'                                                                                                                  ─╯
NAME                                  VERSION                             READY   COMPATIBLE   CREATED
v1.23.15---vmware.1-tkg.4             v1.23.15+vmware.1-tkg.4             False   False        3일6시간
v1.23.8---vmware.2-tkg.2-zshippable   v1.23.8+vmware.2-tkg.2-zshippable   False   False        3일6시간
v1.24.9---vmware.1-tkg.4              v1.24.9+vmware.1-tkg.4              False   False        3일6시간
v1.25.7---vmware.3-fips.1-tkg.1       v1.25.7+vmware.3-fips.1-tkg.1       True    True         3일6시간
v1.26.13---vmware.1-fips.1-tkg.3      v1.26.13+vmware.1-fips.1-tkg.3      True    True         3일6시간
v1.26.5---vmware.2-fips.1-tkg.1       v1.26.5+vmware.2-fips.1-tkg.1       True    True         3일6시간
v1.27.11---vmware.1-fips.1-tkg.2      v1.27.11+vmware.1-fips.1-tkg.2      True    True         3일6시간
v1.28.8---vmware.1-fips.1-tkg.2       v1.28.8+vmware.1-fips.1-tkg.2       True    True         3일6시간
v1.29.4---vmware.3-fips.1-tkg.1       v1.29.4+vmware.3-fips.1-tkg.1       True    True         30시간
```

다음 섹션에서는 제어 플레인, 워커 및 변수에 대해 자세히 살펴볼 것입니다.

## spec.topology.controlPlane

<div class="content-ad"></div>

```yaml
controlPlane:
#      machineHealthCheck:
#        enable: true
#        maxUnhealthy: 100%
#        nodeStartupTimeout: 4h0m0s
#        unhealthyConditions:
#        - status: Unknown
#          timeout: 5m0s
#          type: Ready
#       - status: "False"
#          timeout: 12m0s
#          type: Ready
      metadata:
        annotations:
          run.tanzu.vmware.com/resolve-os-image: os-name=ubuntu
        labels:
          my-custom-label-key: my-custom-label-value
#      nodeDeletionTimeout: "10s"
#      nodeDrainTimeout: "0s"
#      nodeVolumeDetachTimeout: "0s"
      replicas: 1
```

이 섹션은 ClusterAPI를 사용하여 클러스터의 콘트롤 플레인을 생성하는 프로세스를 설명합니다.

- machineHealthCheck 매개변수를 사용하면 사용자가 이 콘트롤 플레인을위한 MachineHealthCheck 구성을 활성화, 비활성화 및 재정의 할 수 있습니다.
- metadata 섹션을 사용하면 콘트롤 플레인에 주석 및 레이블을 추가할 수 있습니다. 위 예제는 콘트롤 플레인 노드 배포에 Ubuntu 이미지를 사용하도록 주석을 추가합니다. 콘트롤 플레인 노드는 주석이 없는 Photon OS로 배포됩니다. 필요에 따라 추가 레이블을 추가할 수 있습니다.
- nodeDeletionTimeout, nodeDrainTimeout 및 nodeVolumeDetachTimeout을 사용하여 콘트롤 플레인, 드레인 및 볼륨 삭제 시간 초과의 기본 제어를 재정의할 수 있습니다.
- 마지막으로 레플리카 수 (보통 1 또는 3)는 클러스터의 콘트롤 플레인 노드 수를 지정합니다.

## spec.topology.workers

<div class="content-ad"></div>

```yaml
   workers:
     machineDeployments:
     - class: node-pool
#        failureDomain: zone1
#        machineHealthCheck:
#          enable: true
#          maxUnhealthy: 100%
#          nodeStartupTimeout: 4h0m0s
#          unhealthyConditions:
#          - status: Unknown
#            timeout: 5m0s
#            type: Ready
#          - status: "False"
#            timeout: 12m0s
#            type: Ready
       metadata:
         annotations:
           cluster.x-k8s.io/cluster-api-autoscaler-node-group-max-size: "3"
           cluster.x-k8s.io/cluster-api-autoscaler-node-group-min-size: "1"
           run.tanzu.vmware.com/resolve-os-image: os-name=ubuntu
         labels:
           my-custom-label-key: my-custom-label-value
       minReadySeconds: 0
       name: node-pool-1
#        nodeDeletionTimeout: "10s"
#        nodeDrainTimeout: "0s"
#        nodeVolumeDetachTimeout: "0s"
#        replicas: 1
       variables:
         overrides:
         - name: vmClass
           value: best-effort-medium
         - name: nodePoolLabels
           value: [{ "key": "my-nodepool-name", "value": "node-pool-1" }]
```

이 섹션은 클러스터의 워커 노드를 구성하는 다양한 구성 요소를 다룹니다. 여러 워커 노드를 machineDeployments 내에서 그룹화할 수 있습니다. 각 machineDeployment(node-pool-1 예제에서) 내에서 다음 구성을 설정할 수 있습니다: —

- 머신 배포는 존별 토폴로지 배포에서 failureDomain에 연결할 수 있습니다.
- machineHealthCheck 매개변수를 사용하면 머신 배포에 대한 ClusterClass의 machine 배포에 정의된 machineHealthCheck 구성을 활성화, 비활성화 및 재정의할 수 있습니다.
- metadata 섹션을 사용하여 에너테이션 및 레이블을 머신 배포에 추가할 수 있습니다. 위 예제에서는 작업자 노드 배포에 Ubuntu 이미지를 사용하도록 에너테이션이 추가되었습니다. 레이블은 에너테이션 없이 Photon OS로 배치됩니다. 오토스케일러 에너테이션은 또한 클러스터 오토스케일러가 노드를 1부터 3까지 확장하거나 축소하도록 지시합니다(이 기능은 8.0U3에서 소개되었습니다). 필요한 경우 추가 레이블을 추가할 수 있습니다.
- 사용자는 노드 삭제, 드레인 및 볼륨 삭제 시간 초과의 기본 제어를 nodeDeletionTimeout, nodeDrainTimeout 및 nodeVolumeDetachTimeout 값으로 재정의할 수 있습니다.
- 레플리카 수는 현재 머신 배포의 워커 노드 수를 지정합니다. 참고: 클러스터 오토스케일러를 사용할 때 이 값을 제공하지 않으면 레플리카를 사용하면 클러스터 오토스케일러 에너테이션이 제공되지 않습니다.
- 또한 변수는 machine 배포를 패치를 통해 사용자 정의하거나 재정의할 수 있습니다. 각 변수는 ClusterClass 상태 변수에서 변수 정의에 연결됩니다. 위 예제는 머신 배포의 vmClass 변수를 best-effort-medium으로 수정하고 nodePoolLabels를 my-nodepool-name: node-pool-1로 수정합니다.

## spec.topology.variables


<div class="content-ad"></div>

```yaml
변수:
  - name: ntp
    value: time.google.com
  - name: vmClass
    value: best-effort-small
  - name: storageClass
    value: tanzu
  - name: defaultStorageClass
    value: tanzu
  - name: clusterEncryptionConfigYaml
    value: |
      apiVersion: apiserver.config.k8s.io/v1
      kind: EncryptionConfiguration
      resources:
        - resources:
            - secrets
          providers:
            - aescbc:
                keys:
                  - name: key1
                    secret: QiMgJGYXudtljldVyl+AnXQQlk7r9iUXBfVKqdEfKm8=
            - identity: {}
  - name: nodePoolVolumes
    value:
    - capacity:
        storage: "15Gi"
      mountPath: "/var/lib/containerd"
      name: containerd
      storageClass: tanzu
    - capacity:
        storage: "15Gi"
      mountPath: "/var/lib/kubelet"
      name: kubelet
      storageClass: tanzu
  - name: controlPlaneVolumes
    value:
    - capacity:
        storage: "15Gi"
      mountPath: "/var/lib/containerd"
      name: containerd
      storageClass: tanzu
    - capacity:
        storage: "15Gi"
      mountPath: "/var/lib/kubelet"
      name: kubelet
      storageClass: tanzu
  - name: podSecurityStandard
    value:
      audit: restricted
      auditVersion: latest
      enforce: privileged
      enforceVersion: latest
      warn: privileged
      warnVersion: latest
  - name: kubeAPIServerFQDNs
    value:
      - workload-vsphere-tkg4.env1.lab.test
  - name: controlPlaneCertificateRotation
    value:
      activate: true
      daysBefore: 90
  - name: nodePoolLabels
    value:
      - key: "my-nodepool-name"
        value: "my-nodepool-value"
```

클러스터를 설정하는 동안 패치에 변수를 사용할 수 있습니다. 이러한 변수는 키/값 쌍으로 정의되며, 그 패치 정의는 spec.topology.class에서 언급된 ClusterClass에 지정됩니다. 위 예시는 대부분의 변수를 사용하는 방법을 보여줍니다. 기본 ClusterClass를 통해 사용 가능한 모든 표준 변수를 다 다루지는 않겠지만, 최근 도입된 몇 가지 새 변수에 대해 설명하겠습니다. 참고: 사용 가능한 모든 구성 가능한 변수 목록을 얻으려면 다음 명령을 실행하십시오:

```yaml
$ kubectl get clusterclass tanzukubernetescluster -o json|jq -r '.spec.variables[].name'|sort

clusterEncryptionConfigYaml
controlPlaneCertificateRotation
controlPlaneVolumes
defaultRegistrySecret
defaultStorageClass
defaultVolumeSnapshotClass
extensionCert
kubeAPIServerFQDNs
nodePoolLabels
nodePoolTaints
nodePoolVolumes
ntp
podSecurityStandard
proxy
storageClass
storageClasses
TKR_DATA
trust
user
vmClass
volumeSnapshotClasses
```

controlPlaneCertificateRotation: 이 기능은 최근 vSphere 8.0 버전에 추가되었습니다. 시스템이 제어 플레인 노드의 TLS 인증서를 만료되기 전에 교체하도록 허용합니다. 제어 플레인 인증서 교체는 새 제어 플레인 노드와 기존 노드 모두에 적용되며, 값은 7일에서 90일 사이로 설정할 수 있습니다.

<div class="content-ad"></div>

controlPlaneVolumes과 nodePoolVolumes: 이 기능을 사용하면 control plane 노드와 워커 노드를 위해 지속적인 볼륨을 설정할 수 있습니다. 현재, 이러한 지속적인 볼륨을 지원하는 마운트 포인트는 노드의 /var/lib/containerd 및 /var/lib/kubelet 디렉터리입니다. 사용자들은 variables.overrides 옵션을 사용하여 각각의 워커 머신 배포에 크기가 다른 볼륨을 가질 수 있습니다.

kubeAPIServerFQDNs: 8.0U2 버전부터 이 기능을 사용하여 FQDN을 가진 TKG 클러스터를 설정할 수 있습니다. 이 변수에는 최소한 하나의 FQDN 문자열이 포함되어 있으며, 이는 Kubernetes API 서버를 위한 TLS 인증서를 생성하는 데 사용됩니다. Kubernetes API 서버를 위한 TLS 인증서에는 kubeAPIServerFQDNs 변수에 지정된 모든 FQDN이 포함됩니다. 중요한 점은 시스템이 FQDN을 해결하려고 시도하지 않는다는 것입니다.

podSecurityStandard: 이 기능은 vSphere 8.0U3에서 소개되었습니다. TKr v1.26 이상에서는 기본적으로 어노테이션 레이블을 사용하여 네임스페이스 수준에서 pod 보안 제한이 강제됩니다. 또는 v1beta1 클러스터를 프로비저닝하거나 업데이트할 때 cluster-wide PSA를 구성하기 위해 podSecurityStandard 변수를 사용할 수 있습니다.