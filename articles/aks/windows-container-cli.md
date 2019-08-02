---
title: Visualização-criar um contêiner do Windows Server em um cluster do AKS (serviço kubernetes do Azure)
description: Saiba como criar rapidamente um cluster kubernetes, implantar um aplicativo em um contêiner do Windows Server no AKS (serviço kubernetes do Azure) usando o CLI do Azure.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/17/2019
ms.author: mlearned
ms.openlocfilehash: 305901007180cfb197cf5c0dfb338800449560a1
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382037"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Visualização-criar um contêiner do Windows Server em um cluster do AKS (serviço kubernetes do Azure) usando o CLI do Azure

O AKS (Serviço de Kubernetes do Azure) é um serviço de Kubernetes gerenciado que permite implantar e gerenciar clusters rapidamente. Neste artigo, você implanta um cluster AKS usando o CLI do Azure. Você também implanta um aplicativo de exemplo ASP.NET em um contêiner do Windows Server para o cluster.

Esse recurso está atualmente na visualização.

![Imagem de navegação para o aplicativo de exemplo ASP.NET](media/windows-container/asp-net-sample-app.png)

Este artigo pressupõe uma compreensão básica dos conceitos de kubernetes. Para obter mais informações, confira [Principais conceitos do Kubernetes para o AKS (Serviço de Kubernetes do Azure)][kubernetes-concepts].

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que você esteja executando o CLI do Azure versão 2.0.61 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

## <a name="before-you-begin"></a>Antes de começar

Você deve adicionar um pool de nós adicional depois de criar o cluster que pode executar contêineres do Windows Server. Adicionar um pool de nós adicional é abordado em uma etapa posterior, mas primeiro você precisa habilitar alguns recursos de visualização.

> [!IMPORTANT]
> Os recursos de visualização do AKS são de autoatendimento e aceitação. Eles são fornecidos para reunir comentários e bugs de nossa comunidade. Na versão prévia, esses recursos não são destinados ao uso em produção. Os recursos na visualização pública se enquadram no suporte "melhor esforço". A assistência das equipes de suporte técnico do AKS está disponível durante o horário comercial do fuso horário do Pacífico (PST). Para obter informações adicionais, consulte os seguintes artigos de suporte:
>
> * [Políticas de suporte do AKS][aks-support-policies]
> * [Perguntas frequentes sobre o suporte do Azure.][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Instalar a extensão da CLI aks-preview

Para usar contêineres do Windows Server, você precisa da extensão da CLI do *AKs* versão 0.4.1 ou superior. Instale a extensão de CLI do Azure *de AKs-Preview* usando o comando [AZ Extension Add][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] ::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-windows-preview-feature"></a>Registrar o recurso de visualização do Windows

Para criar um cluster AKS que pode usar vários pools de nó e executar contêineres do Windows Server, primeiro habilite os sinalizadores de recurso *WindowsPreview* em sua assinatura. O recurso *WindowsPreview* também usa clusters de pool de vários nós e conjunto de dimensionamento de máquinas virtuais para gerenciar a implantação e a configuração dos nós kubernetes. Registre o sinalizador de recurso *WindowsPreview* usando o comando [AZ Feature Register][az-feature-register] , conforme mostrado no exemplo a seguir:

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Qualquer cluster AKS que você criar depois de registrar com êxito o sinalizador de recurso *WindowsPreview* usará essa experiência de visualização de cluster. Para continuar a criar clusters regulares e com suporte total, não habilite os recursos de visualização em assinaturas de produção. Use uma assinatura de teste ou desenvolvimento separada do Azure para testar os recursos de visualização.

Demora alguns minutos para que o registro seja concluído. Verifique o status do registro usando o comando [AZ Feature List][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

Quando o estado do registro `Registered`for, pressione CTRL-C para parar de monitorar o estado.  Em seguida, atualize o registro do provedor de recursos *Microsoft. ContainerService* usando o comando [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Limitações

As seguintes limitações se aplicam quando você cria e gerencia clusters AKS que dão suporte a vários pools de nós:

* Vários pools de nó estão disponíveis para clusters criados depois que você registrou o *WindowsPreview*com êxito. Vários pools de nós também estarão disponíveis se você registrar os recursos *MultiAgentpoolPreview* e *VMSSPreview* para sua assinatura. Você não pode adicionar ou gerenciar pools de nós com um cluster AKS existente criado antes que esses recursos tenham sido registrados com êxito.
* Não é possível excluir o primeiro pool de nós.

Embora esse recurso esteja em versão prévia, as seguintes limitações adicionais se aplicam:

* O cluster AKS pode ter um máximo de oito pools de nós.
* O cluster AKS pode ter um máximo de 400 nós entre esses oito pools de nós.
* O nome do pool de nós do Windows Server tem um limite de 6 caracteres.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um grupo lógico no qual os recursos do Azure são implantados e gerenciados. Ao criar um grupo de recursos, você é solicitado a especificar um local. Essa é a localização na qual os metadados do grupo de recursos são armazenados e na qual os recursos são executados no Azure, caso você não especifique outra região durante a criação de recursos. Crie um grupo de recursos usando o comando [az group create][az-group-create].

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*.

> [!NOTE]
> Este artigo usa a sintaxe bash para os comandos neste tutorial.
> Se você estiver usando Azure Cloud Shell, verifique se a lista suspensa no canto superior esquerdo da janela Cloud Shell está definida como **bash**.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

A seguinte saída de exemplo mostra o grupo de recursos criado com êxito:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

## <a name="create-an-aks-cluster"></a>Criar um cluster AKS

Para executar um cluster AKS que dá suporte a pools de nós para contêineres do Windows Server, o cluster precisa usar uma política de rede que usa o (advanced) network plugin. For more detailed information to help plan out the required subnet ranges and network considerations, see [configure Azure CNI networking][use-advanced-networking] [CNI do Azure][azure-cni-about] . Use o comando [AZ AKs Create][AZ-AKs-Create] para criar um cluster AKs chamado *myAKSCluster*. Esse comando criará os recursos de rede necessários, se eles não existirem.
  * O cluster está configurado com um nó
  * Os parâmetros *Windows-admin-password* e *Windows-admin-username* definem as credenciais de administrador para qualquer contêiner do Windows Server criado no cluster.

Forneça seu próprio *PASSWORD_WIN* seguro (Lembre-se de que os comandos neste artigo são inseridos em um shell bash):

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --enable-addons monitoring \
    --kubernetes-version 1.14.1 \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --enable-vmss \
    --network-plugin azure
```

> [!Note]
> Se você receber um erro de validação de senha, tente criar seu grupo de recursos em outra região.
> Em seguida, tente criar o cluster com o novo grupo de recursos.

Após alguns minutos, o comando será concluído e retornará informações no formato JSON sobre o cluster.

## <a name="add-a-windows-server-node-pool"></a>Adicionar um pool de nós do Windows Server

Por padrão, um cluster AKS é criado com um pool de nós que pode executar contêineres do Linux. Use `az aks nodepool add` o comando para adicionar um pool de nós adicional que pode executar contêineres do Windows Server.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.14.1
```

O comando acima cria um novo pool de nós chamado *npwin* e o adiciona ao *myAKSCluster*. Ao criar um pool de nós para executar contêineres do Windows Server, o valor padrão para *node-VM-size* é *Standard_D2s_v3*. Se você optar por definir o parâmetro *nó-VM-size* , verifique a lista de [tamanhos de VM restritos][restricted-vm-sizes]. O tamanho mínimo recomendado é *Standard_D2s_v3*. O comando acima também usa a sub-rede padrão na vnet padrão criada durante a execução `az aks create`.

## <a name="connect-to-the-cluster"></a>Conectar-se ao cluster

Para gerenciar um cluster do Kubernetes, use [kubectl][kubectl], o cliente de linha de comando do Kubernetes. Se você usar o Azure Cloud Shell, o `kubectl` já estará instalado. Para instalar o `kubectl` localmente, use o comando [az aks install-cli][az-aks-install-cli]:

```azurecli
az aks install-cli
```

Para configurar o `kubectl` para se conectar ao cluster do Kubernetes, use o comando [az aks get-credentials][az-aks-get-credentials]. Este comando baixa as credenciais e configura a CLI do Kubernetes para usá-las.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a conexão ao seu cluster, use o comando [kubectl get][kubectl-get] para retornar uma lista de nós do cluster.

```azurecli-interactive
kubectl get nodes
```

A saída de exemplo a seguir mostra o único nó criado nas etapas anteriores. Verifique se o status do nó é *Pronto*:

```
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
aksnpwin987654                      Ready    agent   108s   v1.14.1
```

## <a name="run-the-application"></a>Executar o aplicativo

Um arquivo de manifesto do Kubernetes define um estado desejado para o cluster, como as imagens de contêiner a serem executadas. Neste artigo, um manifesto é usado para criar todos os objetos necessários para executar o aplicativo de exemplo ASP.NET em um contêiner do Windows Server. Esse manifesto inclui uma [implantação][kubernetes-deployment] for the ASP.NET sample application and an external [Kubernetes service][kubernetes-service] do kubernetes para acessar o aplicativo da Internet.

O aplicativo de exemplo ASP.NET é fornecido como parte do [.NET Framework amostras][dotnet-samples] e é executado em um contêiner do Windows Server. O AKS requer que os contêineres do Windows Server sejam baseados em imagens do *Windows Server 2019* ou superior. O arquivo de manifesto kubernetes também deve definir um seletor de [nó][node-selector] para instruir o cluster AKs a executar o Pod do aplicativo de exemplo ASP.net em um nó que possa executar contêineres do Windows Server.

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

```azurecli-interactive
kubectl apply -f sample.yaml
```

A saída de exemplo a seguir mostra a implantação e o serviço criados com êxito:

```
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Testar o aplicativo

Quando o aplicativo é executado, um serviço de Kubernetes expõe o front-end do aplicativo à Internet. A conclusão desse processo pode levar alguns minutos.

Para monitorar o andamento, use o comando [kubectl get service][kubectl-get] com o argumento `--watch`.

```azurecli-interactive
kubectl get service sample --watch
```

Inicialmente, o *IP externo* para o serviço de *exemplo* é mostrado como *pendente*.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quando o endereço *EXTERNAL-IP* for alterado de *pendente* para um endereço IP público real, use `CTRL-C` para interromper o processo de inspeção do `kubectl`. A seguinte saída de exemplo mostra um endereço IP público válido atribuído ao serviço:

```
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Para ver o aplicativo de exemplo em ação, abra um navegador da Web para o endereço IP externo do seu serviço.

![Imagem de navegação para o aplicativo de exemplo ASP.NET](media/windows-container/asp-net-sample-app.png)

## <a name="delete-cluster"></a>Excluir cluster

Quando o cluster não for mais necessário, use o comando [az group delete][az-group-delete] para remover o grupo de recursos, o serviço de contêiner e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Quando você excluir o cluster, a entidade de serviço do Azure Active Directory usada pelo cluster do AKS não será removida. Para obter as etapas para remover a entidade de serviço, confira [Considerações sobre a entidade de serviço do AKS e sua exclusão][sp-delete].

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você implantou um cluster kubernetes e implantou um aplicativo de exemplo ASP.NET em um contêiner do Windows Server para ele. [Acesse o painel da Web do kubernetes][kubernetes-dashboard] para o cluster que você acabou de criar.

Para saber mais sobre o AKS e percorrer um código completo de exemplo de implantação, prossiga para o tutorial de cluster Kubernetes.

> [!div class="nextstepaction"]
> [Tutorial do AKS][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: https://aka.ms/coingfonboarding
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-provider-register]: /cli/azure/provider#az-provider-register
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[use-advanced-networking]: configure-advanced-networking.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
