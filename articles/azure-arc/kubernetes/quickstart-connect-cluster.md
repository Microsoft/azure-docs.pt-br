---
title: 'Guia de início rápido: Conectar um cluster existente do Kubernetes ao Azure Arc'
description: Neste guia de início rápido, saiba como conectar um cluster do Kubernetes habilitado para Azure Arc.
author: mgoedtel
ms.author: magoedte
ms.service: azure-arc
ms.topic: quickstart
ms.date: 03/03/2021
ms.custom: template-quickstart, references_regions, devx-track-azurecli
keywords: Kubernetes, Arc, Azure, cluster
ms.openlocfilehash: 21ec5000ed7ef9df1805fa6ec43e20efc0f82182
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481235"
---
# <a name="quickstart-connect-an-existing-kubernetes-cluster-to-azure-arc"></a>Guia de início rápido: Conectar um cluster existente do Kubernetes ao Azure Arc 

Neste guia de início rápido, aproveitaremos os benefícios do Kubernetes habilitado para Azure Arc e conectaremos um cluster existente do Kubernetes ao Azure Arc. Para obter uma abordagem conceitual sobre como conectar clusters ao Azure Arc, confira o [artigo Arquitetura do Agente do Kubernetes habilitado para Azure Arc](./conceptual-agent-architecture.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

* Um cluster funcional do Kubernetes. Se você não tiver um, poderá criar um cluster usando uma destas opções:
    * [KIND (Kubernetes no Docker)](https://kind.sigs.k8s.io/)
    * Criar um cluster do Kubernetes usando o Docker para [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) ou para [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)
    * Cluster do Kubernetes autogerenciado usando a [API de Cluster](https://cluster-api.sigs.k8s.io/user/quick-start.html)

    >[!NOTE]
    > O cluster precisa ter pelo menos um nó de sistema operacional e arquitetura do tipo `linux/amd64`. Clusters com apenas `linux/arm64` nós ainda não têm suporte.
    
* Um arquivo `kubeconfig` e o contexto apontando para o seu cluster.
* Permissões de 'Leitura' e 'Gravação' no Azure Arc habilitaram o tipo de recurso do Kubernetes (`Microsoft.Kubernetes/connectedClusters`).

* Instale a [última versão do Helm 3](https://helm.sh/docs/intro/install).

- [Instale ou atualize a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) para uma versão >= 2.16.0
* Instale a extensão `connectedk8s` da CLI do Azure, com versão >= 1.0.0:
  
  ```azurecli
  az extension add --name connectedk8s
  ```

>[!TIP]
> Se a extensão `connectedk8s` já estiver instalada, atualize-a para a versão mais recente usando o comando a seguir – `az extension update --name connectedk8s`


>[!NOTE]
>A lista de regiões com suporte no kubernetes habilitado para o Azure Arc pode ser encontrada [aqui](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc).

>[!NOTE]
> Se você quiser usar locais personalizados no cluster, use as regiões Leste dos EUA ou Oeste da Europa para conectar seu cluster, uma vez que os locais personalizados só estão disponíveis nessas regiões a partir de agora. Todos os outros recursos do Kubernetes habilitados para o Azure Arc estão disponíveis em todas as regiões listadas acima.

## <a name="meet-network-requirements"></a>Atender aos requisitos de rede

>[!IMPORTANT]
>Para funcionar, os agentes do Azure Arc exigem os seguintes protocolos/portas/URLs de saída:
>* TCP na porta 443: `https://:443`
>* TCP na porta 9418: `git://:9418`
  
| Ponto de extremidade (DNS) | Descrição |  
| ----------------- | ------------- |  
| `https://management.azure.com`                                                                                 | Necessário para que o agente se conecte ao Azure e registre o cluster.                                                        |  
| `https://<region>.dp.kubernetesconfiguration.azure.com` | Ponto de extremidade do plano de dados para o agente enviar o status por push e buscar informações de configuração.                                      |  
| `https://login.microsoftonline.com`                                                                            | Necessário para buscar e atualizar tokens do Azure Resource Manager.                                                                                    |  
| `https://mcr.microsoft.com`                                                                            | Necessário para efetuar pull de imagens de contêiner para agentes do Azure Arc.                                                                  |  
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`, `https://wcus.his.arc.azure.com`, `https://scus.his.arc.azure.com`, `https://sea.his.arc.azure.com`, `https://uks.his.arc.azure.com`, `https://wus2.his.arc.azure.com`, `https://ae.his.arc.azure.com`, `https://eus2.his.arc.azure.com`, `https://ne.his.arc.azure.com` |  Necessário para efetuar pull de certificados da MSI (Identidade de Serviço Gerenciada) atribuídos ao sistema.                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Registrar os dois provedores do Kubernetes habilitado para Azure Arc

1. Digite os seguintes comandos:
    ```azurecli
    az provider register --namespace Microsoft.Kubernetes
    az provider register --namespace Microsoft.KubernetesConfiguration
    az provider register --namespace Microsoft.ExtendedLocation
    ```
2. Monitore o processo de registro. O registro pode levar até 10 minutos.
    ```azurecli
    az provider show -n Microsoft.Kubernetes -o table
    az provider show -n Microsoft.KubernetesConfiguration -o table
    az provider show -n Microsoft.ExtendedLocation -o table
    ```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos:  

```console
az group create --name AzureArcTest -l EastUS -o table
```

```output
Location    Name
----------  ------------
eastus      AzureArcTest
```

## <a name="connect-an-existing-kubernetes-cluster"></a>Conectar um cluster existente do Kubernetes

1. Conecte seu cluster do Kubernetes ao Azure Arc usando o seguinte comando:
    ```console
    az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
    ```

    ```output
    Helm release deployment succeeded

    {
      "aadProfile": {
        "clientAppId": "",
        "serverAppId": "",
        "tenantId": ""
      },
      "agentPublicKeyCertificate": "xxxxxxxxxxxxxxxxxxx",
      "agentVersion": null,
      "connectivityStatus": "Connecting",
      "distribution": "gke",
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
      "identity": {
        "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "type": "SystemAssigned"
      },
      "infrastructure": "gcp",
      "kubernetesVersion": null,
      "lastConnectivityTime": null,
      "location": "eastus",
      "managedIdentityCertificateExpirationTime": null,
      "name": "AzureArcTest1",
      "offering": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "AzureArcTest",
      "tags": {},
      "totalCoreCount": null,
      "totalNodeCount": null,
      "type": "Microsoft.Kubernetes/connectedClusters"
    }
    ```

> [!TIP]
> O comando acima sem o parâmetro location especificado cria o recurso do Kubernetes habilitado para Azure Arc no mesmo local que o grupo de recursos. Para criar o recurso do Kubernetes habilitado para Azure Arc em um local diferente, especifique `--location <region>` ou `-l <region>` ao executar o comando `az connectedk8s connect`.

## <a name="verify-cluster-connection"></a>Verificar a conexão do cluster

Veja uma lista dos seus clusters conectados com o seguinte comando:  

```console
az connectedk8s list -g AzureArcTest -o table
```

```output
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

> [!NOTE]
> Após a integração do cluster, levará cerca de 5 a 10 minutos para que os metadados do cluster (versão do cluster, versão do agente, número de nós etc.) sejam exibidos na página de visão geral do recurso do Kubernetes habilitado para Azure Arc no portal do Azure.

## <a name="connect-using-an-outbound-proxy-server"></a>Conectar-se usando um servidor proxy de saída

Se o cluster estiver protegido por um servidor proxy de saída, a CLI do Azure e os agentes do Kubernetes habilitado para Azure Arc precisarão rotear as solicitações por meio do servidor proxy de saída. 


1. Defina as variáveis de ambiente necessárias para a CLI do Azure usar o servidor proxy de saída:

    * Se você estiver usando o Bash, execute o seguinte comando com valores apropriados:

        ```bash
        export HTTP_PROXY=<proxy-server-ip-address>:<port>
        export HTTPS_PROXY=<proxy-server-ip-address>:<port>
        export NO_PROXY=<cluster-apiserver-ip-address>:<port>
        ```

    * Se você estiver usando o PowerShell, execute o seguinte comando com valores apropriados:

        ```powershell
        $Env:HTTP_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:HTTPS_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:NO_PROXY = "<cluster-apiserver-ip-address>:<port>"
        ```

2. Execute o comando connect com os parâmetros de proxy especificados:

    ```console
    az connectedk8s connect -n <cluster-name> -g <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

> [!NOTE]
> * Especifique `excludedCIDR` em `--proxy-skip-range` para garantir que a comunicação no cluster não seja interrompida para os agentes.
> * `--proxy-http`, `--proxy-https` e `--proxy-skip-range` são esperados para a maioria dos ambientes de proxy de saída. `--proxy-cert` *só* será necessário se você precisar injetar certificados confiáveis esperados pelo proxy no repositório de certificados confiáveis dos pods do agente.

## <a name="view-azure-arc-agents-for-kubernetes"></a>Exibir agentes do Azure Arc para Kubernetes

O Kubernetes habilitado para Azure Arc implanta alguns operadores no namespace `azure-arc`. 

1. Veja essas implantações e os pods usando:

    ```console
    kubectl -n azure-arc get deployments,pods
    ```

1. Verifique se todos os pods estão em um estado `Running`.

    ```output
    NAME                                        READY      UP-TO-DATE  AVAILABLE  AGE
    deployment.apps/cluster-metadata-operator     1/1             1        1      16h
    deployment.apps/clusteridentityoperator       1/1             1        1      16h
    deployment.apps/config-agent                  1/1             1        1      16h
    deployment.apps/controller-manager            1/1             1        1      16h
    deployment.apps/flux-logs-agent               1/1             1        1      16h
    deployment.apps/metrics-agent                 1/1             1        1      16h
    deployment.apps/resource-sync-agent           1/1             1        1      16h

    NAME                                           READY    STATUS   RESTART AGE
    pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
    pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
    pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
    pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
    pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
    pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
    pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
    ```

## <a name="clean-up-resources"></a>Limpar os recursos

Exclua o recurso do Kubernetes habilitado para Azure Arc, todos os recursos de configuração associados *e* todos os agentes em execução no cluster usando o seguinte comando na CLI do Azure:

```azurecli
az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
```

>[!NOTE]
>A exclusão do recurso do Kubernetes habilitado para Azure Arc pelo portal do Azure remove todos os recursos de configuração associados, mas *não* remove nenhum agente em execução no cluster. A melhor prática é excluir o recurso do Kubernetes habilitado para Azure Arc usando `az connectedk8s delete` em vez do portal do Azure.

## <a name="next-steps"></a>Próximas etapas

Prossiga para o próximo artigo para saber como implantar configurações no cluster conectado do Kubernetes usando o GitOps.
> [!div class="nextstepaction"]
> [Implantar configurações usando o GitOps](tutorial-use-gitops-connected-cluster.md)
