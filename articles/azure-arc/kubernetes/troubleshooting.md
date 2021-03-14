---
title: Solucionar problemas comuns de kubernetes habilitados para Arc do Azure
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Solução de problemas comuns de clusters do Kubernetes habilitado para Arc.
keywords: Kubernetes, Arc, Azure, contêineres
ms.openlocfilehash: e1f4e84f16c6b584f1ffbd918a86c251f47efcca
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653993"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting"></a>Solução de problemas do kubernetes habilitado para Arc do Azure

Este documento fornece guias de solução de problemas com conectividade, permissões e agentes.

## <a name="general-troubleshooting"></a>Solução de problemas gerais

### <a name="azure-cli"></a>CLI do Azure

Antes de usar os `az connectedk8s` `az k8s-configuration` comandos do ou da CLI, verifique se CLI do Azure está definido para funcionar com a assinatura do Azure correta.

```azurecli
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>Agentes do Azure Arc

Todos os agentes do Kubernetes habilitado para o Azure Arc são implantados como pods no namespace `azure-arc`. Todos os pods devem estar em execução e passando por suas verificações de integridade.

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

Se a versão Helm não for encontrada ou ausente, tente [conectar o cluster ao arco do Azure](./connect-cluster.md) novamente.

Se a versão Helm estiver presente no `STATUS: deployed` , verifique o status dos agentes usando `kubectl` :

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

Todos os pods devem ser mostrados `STATUS` como `Running` com um `3/3` ou `2/2` sob a `READY` coluna. Busque logs e descreva os pods que retornam um `Error` ou `CrashLoopBackOff` . Se qualquer pods estiver preso no `Pending` estado, pode haver recursos insuficientes em nós de cluster. [Escalar verticalmente seu cluster](https://kubernetes.io/docs/tasks/administer-cluster/) pode fazer com que esses pods façam a transição para o `Running` estado.

## <a name="connecting-kubernetes-clusters-to-azure-arc"></a>Conectando clusters kubernetes ao arco do Azure

Conectar clusters ao Azure requer acesso a uma assinatura do Azure e `cluster-admin` acesso a um cluster de destino. Se você não conseguir acessar o cluster ou se tiver permissões insuficientes, a conexão do cluster ao arco do Azure falhará.

### <a name="insufficient-cluster-permissions"></a>Permissões de cluster insuficientes

Se o arquivo kubeconfig fornecido não tiver permissões suficientes para instalar os agentes Arc do Azure, o comando CLI do Azure retornará um erro.

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

O usuário que está conectando o cluster ao arco do Azure deve ter `cluster-admin` a função atribuída a ele no cluster.

### <a name="installation-timeouts"></a>Tempos limite de instalação

Conectar um cluster kubernetes ao kubernetes habilitado para Arc do Azure requer a instalação de agentes Arc do Azure no cluster. Se o cluster estiver sendo executado em uma conexão de Internet lenta, o pull de imagem de contêiner para agentes poderá levar mais tempo do que o CLI do Azure tempo limite.

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...
```

### <a name="helm-issue"></a>Problema de Helm

`v3.3.0-rc.1`A versão Helm tem um [problema](https://github.com/helm/helm/pull/8527) em que o Helm instalar/atualizar (usado pela `connectedk8s` extensão da CLI) resulta na execução de todos os ganchos que levam ao seguinte erro:

```console
$ az connectedk8s connect -n shasbakstest -g shasbakstest
Ensure that you have the latest helm version installed before proceeding.
This operation might take a while...

Please check if the azure-arc namespace was deployed and run 'kubectl get pods -n azure-arc' to check if all the pods are in running state. A possible cause for pods stuck in pending state could be insufficientresources on the kubernetes cluster to onboard to arc.
ValidationError: Unable to install helm release: Error: customresourcedefinitions.apiextensions.k8s.io "connectedclusters.arc.azure.com" not found
```

Para se recuperar desse problema, siga estas etapas:

1. Exclua o recurso kubernetes habilitado para arco do Azure na portal do Azure.
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
Para ajudar a solucionar problemas com o recurso de configuração, execute comandos AZ com o `--debug` parâmetro especificado.

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8s-configuration create <parameters> --debug
```

### <a name="create-configurations"></a>Criar configurações

Permissões de gravação no recurso kubernetes habilitado para Arc do Azure ( `Microsoft.Kubernetes/connectedClusters/Write` ) são necessárias e suficientes para a criação de configurações nesse cluster.

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