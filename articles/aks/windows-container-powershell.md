---
title: Criar um contêiner do Windows Server em um cluster AKS usando o PowerShell
description: Saiba como criar rapidamente um cluster do Kubernetes, implantar um aplicativo em um contêiner do Windows Server no AKS (Serviço de Kubernetes do Azure) usando o PowerShell.
services: container-service
ms.topic: article
ms.date: 05/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 56fc11583bcdd271d0225de90ef7ab06bcf87cbf
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625107"
---
# <a name="create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-powershell"></a>Criar um contêiner do Windows Server em um cluster AKS (Serviço de Kubernetes do Azure) usando o PowerShell

O AKS (Serviço de Kubernetes do Azure) é um serviço de Kubernetes gerenciado que permite implantar e gerenciar clusters rapidamente. Neste artigo, você implanta um cluster AKS usando o PowerShell. Também implanta um aplicativo de exemplo `ASP.NET` em um contêiner do Windows Server para o cluster.

![Imagem de navegação para o aplicativo ASP.NET de exemplo](media/windows-container-powershell/asp-net-sample-app.png)

Este artigo pressupõe uma compreensão básica dos conceitos do Kubernetes. Para obter mais informações, confira [Principais conceitos do Kubernetes para o AKS (Serviço de Kubernetes do Azure)][kubernetes-concepts].

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Se você optar por usar o PowerShell localmente, este artigo exigirá que você instale o módulo Az PowerShell e conecte-se à sua conta do Azure usando o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount). Para obter mais informações sobre como instalar o módulo Az PowerShell, confira [Instalar o Azure PowerShell][install-azure-powershell]. Você também deve instalar o módulo do PowerShell AZ. AKs: 

```azurepowershell-interactive
Install-Module Az.Aks
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se tiver várias assinaturas do Azure, escolha a que for adequada para cobrança do recurso. Selecione uma ID de assinatura específica usando o cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="limitations"></a>Limitações

As seguintes limitações se aplicam quando você cria e gerencia clusters do AKS com suporte a vários pools de nós:

* Não é possível excluir o primeiro pool de nós.

As seguintes limitações adicionais se aplicam aos pools de nós do Windows Server:

* O cluster AKS pode ter um máximo de dez pools de nós.
* O cluster AKS pode ter um máximo de 100 nós em cada pool de nós.
* O nome do pool de nós do Windows Server tem um limite de seis caracteres.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um [grupo de recursos do Azure](../azure-resource-manager/management/overview.md) é um grupo lógico no qual os recursos do Azure são implantados e gerenciados. Ao criar um grupo de recursos, você é solicitado a especificar um local. Essa é a localização na qual os metadados do grupo de recursos são armazenados e na qual os recursos são executados no Azure, caso você não especifique outra região durante a criação de recursos. Crie um grupo de recursos usando o cmdlet [New-AzResourceGroup][new-azresourcegroup].

O exemplo a seguir cria um grupo de recursos chamado **myResourceGroup** no local **eastus**.

> [!NOTE]
> Este artigo usa a sintaxe do PowerShell para os comandos neste tutorial. Se você estiver usando Azure Cloud Shell, verifique se a lista suspensa no canto superior esquerdo da janela Cloud Shell está definida como **PowerShell**.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location eastus
```

A seguinte saída de exemplo mostra o grupo de recursos criado com êxito:

```plaintext
ResourceGroupName : myResourceGroup
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup
```

## <a name="create-an-aks-cluster"></a>Criar um cluster AKS

Use o utilitário de linha de comando `ssh-keygen` para gerar um par de chaves SSH. Para obter mais detalhes, confira [Etapas rápidas: Criar e usar um par de chaves SSH pública e privada para VMs Linux no Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

Para executar um cluster AKS que dá suporte a pools de nós para contêineres do Windows Server, o cluster precisa usar uma política de rede que usa plug-in de rede [CNI do Azure][azure-cni-about] (avançado). Para obter informações mais detalhadas para ajudar a planejar os intervalos de sub-rede e as considerações de rede necessárias, confira [configurar a rede do CNI do Azure][use-advanced-networking]. Use o cmdlet [New-AzAks][new-azaks] abaixo para criar um cluster AKS chamado **myAKSCluster**. O exemplo a seguir criará os recursos de rede necessários se eles não existirem.

> [!NOTE]
> Para verificar se o seu cluster opera de maneira confiável, execute pelo menos 2 (dois) nós no pool de nós padrão.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 2 -KubernetesVersion 1.16.7 -NetworkPlugin azure -NodeVmSetType VirtualMachineScaleSets -WindowsProfileAdminUserName akswinuser -WindowsProfileAdminUserPassword $Password
```

> [!Note]
> Se não for possível criar o cluster AKS porque a versão não tem suporte nessa região, você poderá usar o comando `Get-AzAksVersion -Location eastus` para localizar a lista de versões com suporte nessa região.

Após alguns minutos, o comando será concluído e retornará informações sobre o cluster. Ocasionalmente, o cluster pode levar mais de alguns minutos para ser provisionado. Aguarde até dez minutos nesses casos.

## <a name="add-a-windows-server-node-pool"></a>Adicionar um pool de nós do Windows Server

Por padrão, um cluster AKS é criado com um pool de nós que pode executar contêineres do Linux. Use o cmdlet `New-AzAksNodePool` para adicionar um pool de nós que pode executar contêineres do Windows Server juntamente ao pool de nós do Linux.

```azurepowershell-interactive
New-AzAksNodePool -ResourceGroupName myResourceGroup -ClusterName myAKSCluster -VmSetType VirtualMachineScaleSets -OsType Windows -Name npwin -KubernetesVersion 1.16.7
```

O comando acima cria um pool de nós chamado **npwin** e o adiciona ao **myAKSCluster**. Ao criar um pool de nós para executar contêineres do Windows Server, o valor padrão de **VmSize** é **Standard_D2s_v3**. Se você optar por definir o parâmetro **VmSize**, confira a lista de [tamanhos de VM restritos][restricted-vm-sizes]. O tamanho mínimo recomendado é **Standard_D2s_v3**. O comando anterior também usa a sub-rede padrão na vnet padrão criada durante a execução de `New-AzAks`.

## <a name="connect-to-the-cluster"></a>Conectar-se ao cluster

Para gerenciar um cluster do Kubernetes, use [kubectl][kubectl], o cliente de linha de comando do Kubernetes. Se você usar o Azure Cloud Shell, o `kubectl` já estará instalado. Para instalar `kubectl` localmente, use o cmdlet `Install-AzAksKubectl`:

```azurepowershell-interactive
Install-AzAksKubectl
```

Para configurar o `kubectl` para se conectar ao cluster do Kubernetes, use o cmdlet [Import-AzAksCredential][import-azakscredential]. Este comando baixa as credenciais e configura a CLI do Kubernetes para usá-las.

```azurepowershell-interactive
Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
```

Para verificar a conexão com o cluster, use o comando [kubectl get][kubectl-get] para retornar uma lista dos nós de cluster.

```azurepowershell-interactive
kubectl get nodes
```

A saída de exemplo a seguir mostra todos os nós no cluster. Verifique se o status de todos os nós é **Pronto**:

```plaintext
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.16.7
aksnpwin987654                      Ready    agent   108s   v1.16.7
```

## <a name="run-the-application"></a>Executar o aplicativo

Um arquivo de manifesto do Kubernetes define um estado desejado para o cluster, como as imagens de contêiner a serem executadas. Neste artigo, um manifesto é usado para criar todos os objetos necessários para executar o aplicativo de exemplo ASP.NET em um contêiner do Windows Server. Esse manifesto inclui uma [implantação do Kubernetes][kubernetes-deployment] para o aplicativo de exemplo ASP.NET e um [serviço do Kubernetes][kubernetes-service] externo para acessar ao aplicativo da Internet.

O aplicativo de exemplo ASP.NET é fornecido como parte das [Amostras .NET Framework][dotnet-samples] e é executado em um contêiner do Windows Server. O AKS requer que os contêineres do Windows Server sejam baseados em imagens do **Windows Server 2019** ou posterior. O arquivo de manifesto Kubernetes também deve definir um [seletor de nó][node-selector] para instruir o cluster AKS a executar o pod de seu aplicativo de exemplo ASP.NET em um nó que possa executar contêineres do Windows Server.

Crie um arquivo chamado `sample.yaml` e copie a definição YAML a seguir. Se você usar o Azure Cloud Shell, esse arquivo poderá ser criado usando `vi` ou `nano`, como se você estivesse trabalhando em um sistema físico ou virtual:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

Implante o aplicativo usando o comando [kubectl apply][kubectl-apply] e especifique o nome do manifesto YAML:

```azurepowershell-interactive
kubectl apply -f sample.yaml
```

A seguinte saída de exemplo mostra a Implantação e o Serviço criado com êxito:

```plaintext
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Testar o aplicativo

Quando o aplicativo é executado, um serviço de Kubernetes expõe o front-end do aplicativo à Internet.
A conclusão desse processo pode levar alguns minutos. Ocasionalmente, o serviço pode levar mais de alguns minutos para ser provisionado. Aguarde até dez minutos nesses casos.

Para monitorar o andamento, use o comando [kubectl get service][kubectl-get] com o argumento `--watch`.

```azurepowershell-interactive
kubectl get service sample --watch
```

Inicialmente, o **EXTERNAL-IP** para o serviço de **exemplo** é mostrado como **pendente**.

```plaintext
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quando o endereço **EXTERNAL-IP** for alterado de **pendente** para um endereço IP público real, use `CTRL-C` para interromper o processo de inspeção do `kubectl`. A seguinte saída de exemplo mostra um endereço IP público válido atribuído ao serviço:

```plaintext
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Para ver o aplicativo de exemplo em ação, abra um navegador da Web no endereço IP externo do serviço.

![Imagem de navegação para o aplicativo ASP.NET de exemplo](media/windows-container-powershell/asp-net-sample-app.png)

> [!Note]
> Se você receber um tempo limite de conexão ao tentar carregar a página, verifique se o aplicativo de exemplo está pronto com o seguinte comando `kubectl get pods --watch`. Às vezes, o contêiner do Windows não será iniciado no momento em que o endereço IP externo estiver disponível.

## <a name="delete-cluster"></a>Excluir cluster

Quando o cluster não for mais necessário, use o cmdlet [Remove-AzResourceGroup][remove-azresourcegroup] para remover o grupo de recursos, o serviço de contêiner e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

> [!NOTE]
> Quando você excluir o cluster, a entidade de serviço do Azure Active Directory usada pelo cluster do AKS não será removida. Para obter as etapas para remover a entidade de serviço, confira [Considerações sobre a entidade de serviço do AKS e sua exclusão][sp-delete]. Se você tiver usado uma identidade gerenciada, ela será gerenciada pela plataforma e não exigirá remoção.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você implantou um cluster Kubernetes e implantou um aplicativo de exemplo `ASP.NET` em um contêiner do Windows Server para ele. [Acesse o painel da Web do Kubernetes][kubernetes-dashboard] do cluster criado.

Para saber mais sobre o AKS e percorrer um código completo de exemplo de implantação, prossiga para o tutorial de cluster Kubernetes.

> [!div class="nextstepaction"]
> [Tutorial do AKS][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[install-azure-powershell]: /powershell/azure/install-az-ps
[new-azresourcegroup]: /powershell/module/az.resources/new-azresourcegroup
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[use-advanced-networking]: configure-azure-cni.md
[new-azaks]: /powershell/module/az.aks/new-azaks
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[import-azakscredential]: /powershell/module/az.aks/import-azakscredential
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[kubernetes-dashboard]: kubernetes-dashboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
