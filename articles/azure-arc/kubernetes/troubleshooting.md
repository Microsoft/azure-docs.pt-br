---
title: Solucionar problemas comuns de Kubernetes habilitado para Azure Arc (versão prévia)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Solução de problemas comuns de clusters do Kubernetes habilitado para Arc.
keywords: Kubernetes, Arc, Azure, contêineres
ms.openlocfilehash: 0827386eb6ec089cf7951e8fa513a77fc78aef22
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684082"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting-preview"></a>Solução de problemas de Kubernetes habilitado para Azure Arc (versão prévia)

Este documento fornece alguns cenários comuns da solução de problemas de conectividade, permissões e agentes.

## <a name="general-troubleshooting"></a>Solução de problemas gerais

### <a name="azure-cli-set-up"></a>Configuração da CLI do Azure
Antes de usar os comandos da CLI az connectedk8s ou az k8sconfiguration, verifique se az está configurado para funcionar com a assinatura correta do Azure.

```azurecli
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>Agentes azure-arc
Todos os agentes do Kubernetes habilitado para o Azure Arc são implantados como pods no namespace `azure-arc`. Em operações normais, todos os pods devem estar em execução e passar as verificações de integridade.

Primeiro, verifique a versão do Helm do Azure Arc:

```console
$ helm --namespace default status azure-arc
NAME: azure-arc
LAST DEPLOYED: Fri Apr  3 11:13:10 2020
NAMESPACE: default
STATUS: deployed
REVISION: 5
TEST SUITE: None
```

Se a versão do Helm não for encontrada ou estiver ausente, tente realizar a integração do cluster novamente.

Se a versão do Helm estiver presente e `STATUS: deployed` determinar o status dos agentes usando `kubectl`:

```console
$ kubectl -n azure-arc get deployments,pods
NAME                                       READY  UP-TO-DATE  AVAILABLE  AGE
deployment.apps/clusteridentityoperator     1/1       1          1       16h
deployment.apps/config-agent                1/1       1          1       16h
deployment.apps/cluster-metadata-operator   1/1       1          1       16h
deployment.apps/controller-manager          1/1       1          1       16h
deployment.apps/flux-logs-agent             1/1       1          1       16h
deployment.apps/metrics-agent               1/1       1          1       16h
deployment.apps/resource-sync-agent         1/1       1          1       16h

NAME                                            READY   STATUS  RESTART  AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

Todos os Pods devem mostrar `STATUS` como `Running` e `READY` deve ser `3/3` ou `2/2`. Busque os logs e descreva os pods que estão retornando `Error` ou `CrashLoopBackOff`. Se qualquer um desses pods estiver preso no `Pending` estado, pode ser devido a recursos insuficientes em nós de cluster. [Escalar verticalmente o cluster](https://kubernetes.io/docs/tasks/administer-cluster/) fará com que esses pods façam a transição para o `Running` estado.

## <a name="connecting-kubernetes-clusters-to-azure-arc"></a>Conectando clusters kubernetes ao arco do Azure

A conexão de clusters ao Azure requer acesso a uma assinatura do Azure e `cluster-admin` a um cluster de destino. Se não for possível acessar o cluster ou se não houver permissões suficientes, a integração falhará.

### <a name="insufficient-cluster-permissions"></a>Permissões de cluster insuficientes

Se o arquivo kubeconfig fornecido não tiver permissões suficientes para instalar os agentes do Azure Arc, o comando da CLI do Azure retornará um erro ao tentar chamar a API do Kubernetes.

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

O proprietário do cluster deve usar um usuário de Kubernetes com permissões de administrador de cluster.

### <a name="installation-timeouts"></a>Tempos limite de instalação

A instalação do agente do Azure Arc requer a execução de um conjunto de contêineres no cluster de destino. Se o cluster for executado em uma conexão lenta com a Internet, o pull da imagem de contêiner pode demorar mais do que o tempo limite da CLI do Azure.

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...
```

### <a name="helm-issue"></a>Problema de Helm

`v3.3.0-rc.1`A versão do Helm tem um [problema](https://github.com/helm/helm/pull/8527) em que a instalação/atualização do Helm (usada na extensão da CLI do connectedk8s) resulta na execução de todos os ganchos que levam ao seguinte erro:

```console
$ az connectedk8s connect -n shasbakstest -g shasbakstest
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding.
This operation might take a while...

Please check if the azure-arc namespace was deployed and run 'kubectl get pods -n azure-arc' to check if all the pods are in running state. A possible cause for pods stuck in pending state could be insufficientresources on the kubernetes cluster to onboard to arc.
ValidationError: Unable to install helm release: Error: customresourcedefinitions.apiextensions.k8s.io "connectedclusters.arc.azure.com" not found
```

Para se recuperar desse problema, siga estas etapas:

1. Exclua o recurso kubernetes habilitado para o Azure Arc de preocupação no portal do Azure.
2. Execute os seguintes comandos em seu computador:
    
    ```console
    kubectl delete ns azure-arc
    kubectl delete clusterrolebinding azure-arc-operator
    kubectl delete secret sh.helm.release.v1.azure-arc.v1
    ```

3. [Instale uma versão estável](https://helm.sh/docs/intro/install/) do Helm 3 em seu computador em vez da versão Release Candidate.
4. Execute o `az connectedk8s connect` comando com os valores apropriados para conectar o cluster ao arco do Azure.

## <a name="configuration-management"></a>Gerenciamento de configuração

### <a name="general"></a>Geral
Para ajudar a solucionar problemas na configuração do controle do código-fonte, execute os comandos az com a opção --debug.

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8sconfiguration create <parameters> --debug
```

### <a name="create-source-control-configuration"></a>Criar configuração de controle do código-fonte
A função de colaborador no recurso Microsoft.Kubernetes/connectedCluster é necessária e suficiente para criar o recurso Microsoft.KubernetesConfiguration/sourceControlConfiguration.

### <a name="configuration-remains-pending"></a>A configuração permanece `Pending`

```console
kubectl -n azure-arc logs -l app.kubernetes.io/component=config-agent -c config-agent
$ k -n pending get gitconfigs.clusterconfig.azure.com  -o yaml
apiVersion: v1
items:
- apiVersion: clusterconfig.azure.com/v1beta1
  kind: GitConfig
  metadata:
    creationTimestamp: "2020-04-13T20:37:25Z"
    generation: 1
    name: pending
    namespace: pending
    resourceVersion: "10088301"
    selfLink: /apis/clusterconfig.azure.com/v1beta1/namespaces/pending/gitconfigs/pending
    uid: d9452407-ff53-4c02-9b5a-51d55e62f704
  spec:
    correlationId: ""
    deleteOperator: false
    enableHelmOperator: false
    giturl: git@github.com:slack/cluster-config.git
    helmOperatorProperties: null
    operatorClientLocation: azurearcfork8s.azurecr.io/arc-preview/fluxctl:0.1.3
    operatorInstanceName: pending
    operatorParams: '"--disable-registry-scanning"'
    operatorScope: cluster
    operatorType: flux
  status:
    configAppliedTime: "2020-04-13T20:38:43.081Z"
    isSyncedWithAzure: true
    lastPolledStatusTime: ""
    message: 'Error: {exit status 1} occurred while doing the operation : {Installing
      the operator} on the config'
    operatorPropertiesHashed: ""
    publicKey: ""
    retryCountPublicKey: 0
    status: Installing the operator
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```
## <a name="monitoring"></a>Monitoramento

Azure Monitor para contêineres requer que o Daemonset seja executado no modo privilegiado. Para configurar com êxito um cluster kubernetes com botão canônico para monitoramento, execute o seguinte comando:

```console
juju config kubernetes-worker allow-privileged=true
```