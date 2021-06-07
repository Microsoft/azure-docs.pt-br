---
title: 'Início Rápido: Implantar um cluster do AKS usando o PowerShell'
description: Saiba como criar rapidamente um cluster do Kubernetes, implantar um aplicativo e monitorar o desempenho no AKS (Serviço de Kubernetes do Azure) usando o PowerShell.
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b61c791390200beac4a18422a4de58dd94fa711
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103492890"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-powershell"></a>Início Rápido: Implantar um cluster do Serviço de Kubernetes do Azure usando o PowerShell

O AKS (Serviço de Kubernetes do Azure) é um serviço de Kubernetes gerenciado que permite implantar e gerenciar clusters rapidamente. Neste guia de início rápido, você vai:
* Implantar um cluster do AKS usando o PowerShell. 
* Executar um aplicativo de vários contêineres com um front-end da Web e uma instância do Redis no cluster. 
* Monitorar a integridade do cluster e dos pods que executam o aplicativo.

Para saber mais sobre como criar um pool de nós do Windows Server, confira [Criar um cluster do AKS compatível com contêineres do Windows Server][windows-container-powershell].

![Aplicativo de votação implantado no Serviço de Kubernetes do Azure](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

Este guia de início rápido pressupõe uma compreensão básica dos conceitos do Kubernetes. Para obter mais informações, confira [Principais conceitos do Kubernetes para o AKS (Serviço de Kubernetes do Azure)][kubernetes-concepts].

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Se estiver executando o PowerShell localmente, instale o módulo Az PowerShell e conecte-se à sua conta do Azure usando o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount). Para obter mais informações sobre como instalar o módulo Az PowerShell, confira [Instalar o Azure PowerShell][install-azure-powershell].

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se tiver várias assinaturas do Azure, selecione a ID da assinatura adequada para cobrança dos recursos usando o cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um [grupo de recursos do Azure](../azure-resource-manager/management/overview.md) é um grupo lógico no qual os recursos do Azure são implantados e gerenciados. Ao criar um grupo de recursos, você verá um prompt para especificar uma localização. Essa localização é: 
* A localização de armazenamento dos metadados do grupo de recursos.
* Onde seus recursos serão executados no Azure caso você não especifique outra região durante a criação de recursos. 

O exemplo a seguir cria um grupo de recursos chamado **myResourceGroup** na região **eastus**.

Crie um grupo de recursos usando o cmdlet [New-AzResourceGroup][new-azresourcegroup].

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location eastus
```

Saída do grupo de recursos criado com êxito:

```plaintext
ResourceGroupName : myResourceGroup
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup
```

## <a name="create-aks-cluster"></a>Criar cluster AKS

1. Gere um par de chaves SSH usando o utilitário de linha de comando `ssh-keygen`. 
    * Para obter mais detalhes, confira [Etapas rápidas: Criar e usar um par de chaves SSH pública e privada para VMs Linux no Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

1. Crie um cluster do AKS usando o cmdlet [New-AzAks][new-azaks]. O Azure Monitor para contêineres é habilitado por padrão.

    O exemplo a seguir cria um cluster chamado **myAKSCluster** com um nó. 

    ```azurepowershell-interactive
    New-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 1
    ```

Após alguns minutos, o comando será concluído e retornará informações sobre o cluster.

> [!NOTE]
> Quando você cria um cluster do AKS, um segundo grupo de recursos é criado automaticamente para armazenar os recursos do AKS. Para obter mais informações, confira [Por que dois grupos de recursos são criados com o AKS?](./faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Conectar-se ao cluster

Para gerenciar um cluster do Kubernetes, use o cliente de linha de comando do Kubernetes, [kubectl][kubectl]. `kubectl` já está instalado se você usa o Azure Cloud Shell. 

1. Instale `kubectl` localmente usando o cmdlet `Install-AzAksKubectl`:

    ```azurepowershell
    Install-AzAksKubectl
    ```

2. Configure `kubectl` para se conectar ao cluster do Kubernetes usando o cmdlet [Import-AzAksCredential][import-azakscredential]. O cmdlet a seguir baixa credenciais e configura a CLI do Kubernetes para usá-las.

    ```azurepowershell-interactive
    Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
    ```

3. Verifique a conexão com o cluster usando o comando [kubectl get][kubectl-get]. Esse comando retorna uma lista dos nós de cluster.

    ```azurepowershell-interactive
    .\kubectl get nodes
    ```

    A saída mostra o único nó criado nas etapas anteriores. Verifique se que o status do nó é *Pronto*:

    ```plaintext
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.15.10
    ```

## <a name="run-the-application"></a>Executar o aplicativo

Um [arquivo de manifesto do Kubernetes][kubernetes-deployment] define o estado desejado de um cluster, por exemplo, as imagens de contêiner a serem executadas. 

Neste início rápido, você usará um manifesto para criar todos os objetos necessários para executar o [aplicativo Azure Vote][azure-vote-app]. Esse manifesto inclui duas [implantações do Kubernetes][kubernetes-deployment]:
* Os aplicativos Azure Vote de exemplo em Python.
* Uma instância do Redis. 

Dois [Serviços Kubernetes][kubernetes-service] também são criados:
* Um serviço interno para a instância do Redis.
* Um serviço externo para acessar o aplicativo Azure Vote da Internet.

1. Crie um arquivo chamado `azure-vote.yaml`.
    * Se você usa o Azure Cloud Shell, esse arquivo pode ser criado usando `vi` ou `nano`, como se você estivesse trabalhando em um sistema físico ou virtual
1. Copie a seguinte definição YAML:

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: azure-vote-back
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: azure-vote-back
      template:
        metadata:
          labels:
            app: azure-vote-back
        spec:
          nodeSelector:
            "beta.kubernetes.io/os": linux
          containers:
          - name: azure-vote-back
            image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
            env:
            - name: ALLOW_EMPTY_PASSWORD
              value: "yes"
            resources:
              requests:
                cpu: 100m
                memory: 128Mi
              limits:
                cpu: 250m
                memory: 256Mi
            ports:
            - containerPort: 6379
              name: redis
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: azure-vote-back
    spec:
      ports:
      - port: 6379
      selector:
        app: azure-vote-back
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: azure-vote-front
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: azure-vote-front
      template:
        metadata:
          labels:
            app: azure-vote-front
        spec:
          nodeSelector:
            "beta.kubernetes.io/os": linux
          containers:
          - name: azure-vote-front
            image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
            resources:
              requests:
                cpu: 100m
                memory: 128Mi
              limits:
                cpu: 250m
                memory: 256Mi
            ports:
            - containerPort: 80
            env:
            - name: REDIS
              value: "azure-vote-back"
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: azure-vote-front
    spec:
      type: LoadBalancer
      ports:
      - port: 80
      selector:
        app: azure-vote-front
    ```

1. Implante o aplicativo usando o comando [kubectl apply][kubectl-apply] e especifique o nome do manifesto YAML:

    ```azurepowershell-interactive
    .\kubectl apply -f azure-vote.yaml
    ```

    A saída mostra as implantações e os serviços criados com êxito:

    ```plaintext
    deployment.apps/azure-vote-back created
    service/azure-vote-back created
    deployment.apps/azure-vote-front created
    service/azure-vote-front created
    ```

## <a name="test-the-application"></a>Testar o aplicativo

Quando o aplicativo é executado, um serviço de Kubernetes expõe o front-end do aplicativo à Internet. A conclusão desse processo pode levar alguns minutos.

Monitore o andamento usando o comando [kubectl get service][kubectl-get] com o argumento `--watch`.

```azurepowershell-interactive
.\kubectl get service azure-vote-front --watch
```

A saída **EXTERNAL-IP** do serviço `azure-vote-front` será mostrada inicialmente como *pendente*.

```plaintext
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quando o endereço **EXTERNAL-IP** for alterado de *pendente* para um endereço IP público real, use `CTRL-C` para interromper o processo de inspeção do `kubectl`. A seguinte saída de exemplo mostra um endereço IP público válido atribuído ao serviço:

```plaintext
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Para ver o aplicativo Azure Vote em ação, abra um navegador da Web no endereço IP externo do serviço.

![Aplicativo de votação implantado no Serviço de Kubernetes do Azure](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

Exiba as métricas de integridade dos pods e dos nós de cluster capturadas pelo Azure Monitor para contêineres no portal do Azure. 

## <a name="delete-the-cluster"></a>Excluir o cluster

Para evitar encargos do Azure, limpe os recursos desnecessários. Use o cmdlet [Remove-AzResourceGroup][remove-azresourcegroup] para remover o grupo de recursos, o serviço de contêiner e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

> [!NOTE]
> Quando você excluir o cluster, a entidade de serviço do Azure Active Directory usada pelo cluster do AKS não será removida. Para obter as etapas para remover a entidade de serviço, confira [Considerações sobre a entidade de serviço do AKS e sua exclusão][sp-delete].
> 
> Se você tiver usado uma identidade gerenciada, ela será gerenciada pela plataforma e não exigirá remoção.

## <a name="get-the-code"></a>Obter o código

Imagens de contêiner existentes foram usadas neste início rápido para criar uma implantação do Kubernetes. O código do aplicativo relacionado, o Dockerfile e o arquivo de manifesto Kubernetes estão [disponíveis no GitHub.][azure-vote-app]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou um cluster do Kubernetes e um aplicativo com vários contêineres nele. [Acesse o painel da Web do Kubernetes][kubernetes-dashboard] para seu cluster do AKS.

Para saber mais sobre o AKS e percorrer um código completo de exemplo de implantação, prossiga para o tutorial de cluster Kubernetes.

> [!div class="nextstepaction"]
> [Tutorial do AKS][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: ../dev-spaces/index.yml
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git

<!-- LINKS - internal -->
[windows-container-powershell]: windows-container-powershell.md
[kubernetes-concepts]: concepts-clusters-workloads.md
[install-azure-powershell]: /powershell/azure/install-az-ps
[new-azresourcegroup]: /powershell/module/az.resources/new-azresourcegroup
[new-azaks]: /powershell/module/az.aks/new-azaks
[import-azakscredential]: /powershell/module/az.aks/import-azakscredential
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[kubernetes-dashboard]: kubernetes-dashboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
