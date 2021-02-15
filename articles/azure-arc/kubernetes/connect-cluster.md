---
title: Conectar um cluster kubernetes habilitado para o Azure Arc (versão prévia)
services: azure-arc
ms.service: azure-arc
ms.date: 02/09/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Conectar um cluster kubernetes habilitado para o Azure Arc com o Azure Arc
keywords: Kubernetes, Arc, Azure, K8s, containers
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: e68eccf998592aa7d1ebfea51e4ca66d577b3c7f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390548"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>Conectar um cluster kubernetes habilitado para o Azure Arc (versão prévia)

Este artigo aborda o processo de conexão de qualquer cluster kubernetes certificado de CNCF (nuvem Native Computing Foundation), como o AKS-Engine no Azure, o mecanismo de AKS em Azure Stack Hub, GKE, EKS e VMware vSphere cluster para o Azure Arc.

## <a name="before-you-begin"></a>Antes de começar

Verifique se você preparou os seguintes pré-requisitos:

* Um cluster kubernetes ativo e em execução. Se você não tiver um cluster kubernetes existente, poderá usar um dos seguintes guias para criar um cluster de teste:
  * Crie um cluster kubernetes usando [kubernetes no Docker (tipo)](https://kind.sigs.k8s.io/).
  * Crie um cluster kubernetes usando o Docker para [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) ou [Windows](https://docs.docker.com/docker-for-windows/#kubernetes).
* Um arquivo kubeconfig para acessar o cluster e a função de administrador de cluster no cluster para implantação de agentes kubernetes habilitados para Arc.
* O usuário ou a entidade de serviço usada com os comandos `az login` e `az connectedk8s connect` deve ter as permissões 'Ler' e 'Gravar' no tipo de recurso 'Microsoft.Kubernetes/connectedclusters'. A função "cluster kubernetes-integração de arco do Azure" tem essas permissões e pode ser usada para atribuições de função no usuário ou na entidade de serviço.
* Helm 3 para a integração do cluster usando uma extensão connectedk8s. [Instale a versão mais recente do Helm 3](https://helm.sh/docs/intro/install) para atender a esse requisito.
* CLI do Azure versão 2.15 + para instalar as extensões da CLI do kubernetes habilitadas para o Arc do Azure. [Instale o CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) ou atualize para a versão mais recente.
* Instale as extensões da CLI do kubernetes habilitadas para Arc:
  
  * Instale a extensão `connectedk8s`, que ajuda a conectar clusters do Kubernetes ao Azure:
  
  ```azurecli
  az extension add --name connectedk8s
  ```
  
   * Instalar a extensão `k8sconfiguration`:
  
  ```azurecli
  az extension add --name k8sconfiguration
  ```

  * Se você quiser atualizar essas extensões posteriormente, execute os seguintes comandos:
  
  ```azurecli
  az extension update --name connectedk8s
  az extension update --name k8sconfiguration
  ```

## <a name="supported-regions"></a>Regiões com suporte

* Leste dos EUA
* Europa Ocidental

## <a name="network-requirements"></a>Requisitos de rede

Os agentes do Azure Arc exigem que os seguintes protocolos/portas/URLs de saída funcionem:

* TCP na porta 443: `https://:443`
* TCP na porta 9418: `git://:9418`

| Ponto de extremidade (DNS)                                                                                               | Descrição                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | Necessário para que o agente se conecte ao Azure e registre o cluster.                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | Ponto de extremidade do plano de dados para o agente enviar o status por push e obter informações de configuração.                                      |
| `https://login.microsoftonline.com`                                                                            | Necessário para buscar e atualizar Azure Resource Manager tokens.                                                                                    |
| `https://mcr.microsoft.com`                                                                            | Necessário para efetuar pull de imagens de contêiner para agentes de arco do Azure.                                                                  |
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`                                                                            |  Necessário para efetuar pull de certificados de identidade gerenciados atribuídos pelo sistema.                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Registre os dois provedores de Kubernetes habilitados para o Azure Arc:

```console
az provider register --namespace Microsoft.Kubernetes

az provider register --namespace Microsoft.KubernetesConfiguration
```

O registro é um processo assíncrono e pode levar aproximadamente 10 minutos. Você pode monitorar o processo de registro com os seguintes comandos:

```console
az provider show -n Microsoft.Kubernetes -o table
```

```console
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Use um grupo de recursos para armazenar metadados para o cluster.

Primeiro, crie um grupo de recursos para manter o recurso de cluster conectado.

```console
az group create --name AzureArcTest -l EastUS -o table
```

**Saída:**

```console
Location    Name
----------  ------------
eastus      AzureArcTest
```

## <a name="connect-a-cluster"></a>Conectar um cluster

Em seguida, conectaremos nosso cluster kubernetes ao Azure usando `az connectedk8s connect` :

1. Verifique a conectividade com o cluster kubernetes por meio de um dos seguintes:
   * `KUBECONFIG`
   * `~/.kube/config`
   * `--kube-config`
1. Implante agentes Arc do Azure para kubernetes usando o Helm 3 no `azure-arc` namespace:

    ```console
    az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
    ```

**Saída:**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Helm release deployment succeeded

{
  "aadProfile": {
    "clientAppId": "",
    "serverAppId": "",
    "tenantId": ""
  },
  "agentPublicKeyCertificate": "...",
  "agentVersion": "0.1.0",
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
  "identity": {
    "principalId": null,
    "tenantId": null,
    "type": "None"
  },
  "kubernetesVersion": "v1.15.0",
  "location": "eastus",
  "name": "AzureArcTest1",
  "resourceGroup": "AzureArcTest",
  "tags": {},
  "totalNodeCount": 1,
  "type": "Microsoft.Kubernetes/connectedClusters"
}
```

## <a name="verify-connected-cluster"></a>Verifique o cluster conectado

Use o seguinte comando para listar seus clusters conectados:

```console
az connectedk8s list -g AzureArcTest -o table
```

**Saída:**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

Você também pode exibir esse recurso no [portal do Azure](https://portal.azure.com/). Abra o portal no seu navegador e navegue até o grupo de recursos e o recurso de kubernetes habilitado para Arc do Azure, com base nas entradas de nome do recurso e do grupo de recursos usadas anteriormente no `az connectedk8s connect` comando.  
> [!NOTE]
> Depois de integrar o cluster, ele leva cerca de 5 a 10 minutos para os metadados do cluster (versão do cluster, versão do agente, número de nós, etc.) para a superfície na página Visão geral do recurso kubernetes habilitado para Arc do Azure no portal do Azure.

## <a name="connect-using-an-outbound-proxy-server"></a>Conectar-se usando um servidor proxy de saída

Se o cluster estiver protegido por um servidor proxy de saída, CLI do Azure e os agentes kubernetes habilitados para Arc precisarão rotear suas solicitações por meio do servidor proxy de saída:

1. Verifique a versão da `connectedk8s` extensão instalada em seu computador:

    ```console
    az -v
    ```

    Você precisa da `connectedk8s` versão de extensão 0.2.5 + para configurar agentes com proxy de saída. Se você tiver a versão 0.2.3 ou mais antiga em seu computador, siga as [etapas de atualização](#before-you-begin) para obter a versão mais recente da extensão em seu computador.

2. Defina as variáveis de ambiente necessárias para CLI do Azure usar o servidor proxy de saída:

    * Se você estiver usando o bash, execute o seguinte comando com os valores apropriados:

        ```bash
        export HTTP_PROXY=<proxy-server-ip-address>:<port>
        export HTTPS_PROXY=<proxy-server-ip-address>:<port>
        export NO_PROXY=<cluster-apiserver-ip-address>:<port>
        ```

    * Se você estiver usando o PowerShell, execute o seguinte comando com os valores apropriados:

        ```powershell
        $Env:HTTP_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:HTTPS_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:NO_PROXY = "<cluster-apiserver-ip-address>:<port>"
        ```

3. Execute o comando Connect com os parâmetros de proxy especificados:

    ```console
    az connectedk8s connect -n <cluster-name> -g <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

> [!NOTE]
> * Especificar `excludedCIDR` em `--proxy-skip-range` é importante para garantir que a comunicação no cluster não seja interrompida para os agentes.
> * Enquanto `--proxy-http` , `--proxy-https` e `--proxy-skip-range` são esperados para a maioria dos ambientes de proxy de saída, `--proxy-cert` só é necessário se os certificados confiáveis do proxy precisarem ser injetados no repositório de certificados confiáveis do pods do agente.
> * A especificação de proxy acima é atualmente aplicada somente para agentes Arc e não para o pods de fluxo usado em sourceControlConfiguration. A equipe de kubernetes habilitada para Arc está trabalhando ativamente nesse recurso e estará disponível em breve.

## <a name="azure-arc-agents-for-kubernetes"></a>Agentes do Azure Arc para Kubernetes

O Kubernetes habilitado para Azure Arc implanta alguns operadores no namespace `azure-arc`. Você pode exibir essas implantações e pods usando:

```console
kubectl -n azure-arc get deployments,pods
```

**Saída:**

```console
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

O Kubernetes habilitado para Azure Arc consiste em alguns agentes (operadores) que são executados no cluster implantado no namespace `azure-arc`.

| Agentes (operadores)                                                                                               | Descrição                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `deployment.apps/config-agent`                                                                                 | Observa o cluster conectado para obter os recursos de configuração de controle do código-fonte aplicados no cluster e atualiza o estado de conformidade.                                                        |
| `deployment.apps/controller-manager` | Um operador de operadores que orquestra as interações entre os componentes Arc do Azure.                                      |
| `deployment.apps/metrics-agent`                                                                            | Coleta métricas de desempenho de outros agentes Arc.                                                                                    |
| `deployment.apps/cluster-metadata-operator`                                                                            | Coleta metadados do cluster, como a versão do cluster, a contagem de nós e a versão do agente Arc do Azure.                                                                  |
| `deployment.apps/resource-sync-agent`                                                                            |  Sincroniza os metadados de cluster mencionados acima para o Azure.                                                                  |
| `deployment.apps/clusteridentityoperator`                                                                            |  O kubernetes habilitado para Arc do Azure atualmente dá suporte à identidade atribuída pelo sistema. `clusteridentityoperator` mantém o certificado MSI (identidade de serviço gerenciado) usado por outros agentes para comunicação com o Azure.                                                                  |
| `deployment.apps/flux-logs-agent`                                                                            |  Coleta logs dos operadores de fluxo implantados como parte da configuração de controle do código-fonte.                                                                  |

## <a name="delete-a-connected-cluster"></a>Excluir um cluster conectado

Você pode excluir um recurso do `Microsoft.Kubernetes/connectedcluster` usando a CLI do Azure ou o portal do Azure.


* **Exclusão usando CLI do Azure**: Use o comando CLI do Azure a seguir para iniciar a exclusão do recurso kubernetes habilitado para arco do Azure.
  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```
  Este comando Remove o `Microsoft.Kubernetes/connectedCluster` recurso e todos os `sourcecontrolconfiguration` recursos associados no Azure. O CLI do Azure `helm uninstall` também usa para remover os agentes em execução no cluster.

* **Exclusão no portal do Azure**: a exclusão do recurso kubernetes habilitado para Arc do azure em portal do Azure exclui o `Microsoft.Kubernetes/connectedcluster` recurso e todos os `sourcecontrolconfiguration` recursos associados no Azure, mas *não* remove os agentes em execução no cluster. 

  Para remover os agentes em execução no cluster, execute o seguinte comando:

  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```

## <a name="next-steps"></a>Próximas etapas

* [Usar o GitOps em um cluster conectado](./use-gitops-connected-cluster.md)
* [Usar Azure Policy para controlar a configuração do cluster](./use-azure-policy.md)
