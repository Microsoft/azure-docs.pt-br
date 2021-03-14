---
title: Criar um contêiner do Windows Server em um cluster AKS usando CLI do Azure
description: Saiba como criar rapidamente um cluster kubernetes, implantar um aplicativo em um contêiner do Windows Server no AKS (serviço kubernetes do Azure) usando o CLI do Azure.
services: container-service
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: 13b4fbd21bb348d1ef79a3ca68128869115745cc
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200898"
---
# <a name="create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Criar um contêiner do Windows Server em um cluster do AKS (serviço kubernetes do Azure) usando o CLI do Azure

O AKS (Serviço de Kubernetes do Azure) é um serviço de Kubernetes gerenciado que permite implantar e gerenciar clusters rapidamente. Neste artigo, você implanta um cluster AKS usando o CLI do Azure. Você também implanta um aplicativo de exemplo ASP.NET em um contêiner do Windows Server para o cluster.

![Imagem de navegação para o aplicativo ASP.NET de exemplo](media/windows-container/asp-net-sample-app.png)

Este artigo pressupõe uma compreensão básica dos conceitos do Kubernetes. Para obter mais informações, confira [Principais conceitos do Kubernetes para o AKS (Serviço de Kubernetes do Azure)][kubernetes-concepts].

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

### <a name="limitations"></a>Limitações

As seguintes limitações se aplicam quando você cria e gerencia clusters do AKS com suporte a vários pools de nós:

* Não é possível excluir o primeiro pool de nós.

As seguintes limitações adicionais se aplicam aos pools de nós do Windows Server:

* O cluster AKS pode ter um máximo de dez pools de nós.
* O cluster AKS pode ter um máximo de 100 nós em cada pool de nós.
* O nome do pool de nós do Windows Server tem um limite de seis caracteres.

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

Para executar um cluster AKS que dá suporte a pools de nós para contêineres do Windows Server, o cluster precisa usar uma política de rede que usa plug-in de rede [CNI do Azure][azure-cni-about] (avançado). Para obter informações mais detalhadas para ajudar a planejar os intervalos de sub-rede e as considerações de rede necessárias, confira [configurar a rede do CNI do Azure][use-advanced-networking]. Use o comando [AZ AKs Create][az-aks-create] para criar um cluster AKs chamado *myAKSCluster*. Esse comando criará os recursos de rede necessários, se eles não existirem.

* O cluster é configurado com dois nós.
* Os `--windows-admin-password` `--windows-admin-username` parâmetros e definem as credenciais de administrador para qualquer contêiner do Windows Server criado no cluster e devem atender aos [requisitos de senha do Windows Server][windows-server-password]. Se você não especificar o parâmetro *Windows-admin-password* , será solicitado a fornecer um valor.
* O pool de nós usa `VirtualMachineScaleSets` .

> [!NOTE]
> Para garantir que o cluster opere de forma confiável, você deve executar pelo menos 2 (dois) nós no pool de nós padrão.

Crie um nome de usuário para usar como credenciais de administrador para seus contêineres do Windows Server em seu cluster. Os comandos a seguir solicitam um nome de usuário e os definem WINDOWS_USERNAME para uso em um comando posterior (Lembre-se de que os comandos neste artigo são inseridos em um shell BASH).

```azurecli-interactive
echo "Please enter the username to use as administrator credentials for Windows Server containers on your cluster: " && read WINDOWS_USERNAME
```

Crie seu cluster garantindo que você especifique o `--windows-admin-username` parâmetro. O comando de exemplo a seguir cria um cluster usando o valor de *WINDOWS_USERNAME* definido no comando anterior. Como alternativa, você pode fornecer um nome de usuário diferente diretamente no parâmetro em vez de usar *WINDOWS_USERNAME*. O comando a seguir também solicitará que você crie uma senha para as credenciais de administrador para seus contêineres do Windows Server em seu cluster. Como alternativa, você pode usar o parâmetro *Windows-admin-password* e especificar seu próprio valor lá.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --enable-addons monitoring \
    --generate-ssh-keys \
    --windows-admin-username $WINDOWS_USERNAME \
    --vm-set-type VirtualMachineScaleSets \
    --network-plugin azure
```

> [!NOTE]
> Se você receber um erro de validação de senha, verifique se a senha que você definiu atende aos [requisitos de senha do Windows Server][windows-server-password]. Se sua senha atender aos requisitos, tente criar seu grupo de recursos em outra região. Em seguida, tente criar o cluster com o novo grupo de recursos.

Após alguns minutos, o comando será concluído e retornará informações no formato JSON sobre o cluster. Ocasionalmente, o cluster pode levar mais de alguns minutos para ser provisionado. Aguarde até dez minutos nesses casos.

## <a name="add-a-windows-server-node-pool"></a>Adicionar um pool de nós do Windows Server

Por padrão, um cluster AKS é criado com um pool de nós que pode executar contêineres do Linux. Use `az aks nodepool add` o comando para adicionar um pool de nós adicional que pode executar contêineres do Windows Server juntamente com o pool de nós do Linux.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1
```

O comando acima cria um pool de nós chamado *npwin* e o adiciona ao *myAKSCluster*. Ao criar um pool de nós para executar contêineres do Windows Server, o valor padrão do *nó-VM-size* é *Standard_D2s_v3*. Se você optar por definir o parâmetro *nó-VM-size* , verifique a lista de [tamanhos de VM restritos][restricted-vm-sizes]. O tamanho mínimo recomendado é *Standard_D2s_v3*. O comando acima também usa a sub-rede padrão na vnet padrão criada durante a execução `az aks create` .

## <a name="connect-to-the-cluster"></a>Conectar-se ao cluster

Para gerenciar um cluster do Kubernetes, use [kubectl][kubectl], o cliente de linha de comando do Kubernetes. Se você usar o Azure Cloud Shell, o `kubectl` já estará instalado. Para instalar o `kubectl` localmente, use o comando [az aks install-cli][az-aks-install-cli]:

```azurecli
az aks install-cli
```

Para configurar o `kubectl` para se conectar ao cluster do Kubernetes, use o comando [az aks get-credentials][az-aks-get-credentials]. Este comando baixa as credenciais e configura a CLI do Kubernetes para usá-las.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a conexão com o cluster, use o comando [kubectl get][kubectl-get] para retornar uma lista dos nós de cluster.

```console
kubectl get nodes
```

A saída de exemplo a seguir mostra todos os nós no cluster. Verifique se o status de todos os nós é *Pronto*:

```output
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.16.9
aksnpwin987654                      Ready    agent   108s   v1.16.9
```

## <a name="run-the-application"></a>Executar o aplicativo

Um arquivo de manifesto do Kubernetes define um estado desejado para o cluster, como as imagens de contêiner a serem executadas. Neste artigo, um manifesto é usado para criar todos os objetos necessários para executar o aplicativo de exemplo ASP.NET em um contêiner do Windows Server. Esse manifesto inclui uma [implantação do Kubernetes][kubernetes-deployment] para o aplicativo de exemplo ASP.NET e um [serviço do Kubernetes][kubernetes-service] externo para acessar ao aplicativo da Internet.

O aplicativo de exemplo ASP.NET é fornecido como parte das [Amostras .NET Framework][dotnet-samples] e é executado em um contêiner do Windows Server. O AKS requer que os contêineres do Windows Server sejam baseados em imagens do *Windows Server 2019* ou posterior. O arquivo de manifesto Kubernetes também deve definir um [seletor de nó][node-selector] para instruir o cluster AKS a executar o pod de seu aplicativo de exemplo ASP.NET em um nó que possa executar contêineres do Windows Server.

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

```console
kubectl apply -f sample.yaml
```

A seguinte saída de exemplo mostra a Implantação e o Serviço criado com êxito:

```output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Testar o aplicativo

Quando o aplicativo é executado, um serviço de Kubernetes expõe o front-end do aplicativo à Internet. A conclusão desse processo pode levar alguns minutos. Ocasionalmente, o serviço pode levar mais de alguns minutos para ser provisionado. Aguarde até dez minutos nesses casos.

Para monitorar o andamento, use o comando [kubectl get service][kubectl-get] com o argumento `--watch`.

```console
kubectl get service sample --watch
```

Inicialmente, o *EXTERNAL-IP* para o serviço de *exemplo* é mostrado como *pendente*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quando o endereço *EXTERNAL-IP* for alterado de *pendente* para um endereço IP público real, use `CTRL-C` para interromper o processo de inspeção do `kubectl`. A seguinte saída de exemplo mostra um endereço IP público válido atribuído ao serviço:

```output
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Para ver o aplicativo de exemplo em ação, abra um navegador da Web no endereço IP externo do serviço.

![Imagem de navegação para o aplicativo ASP.NET de exemplo](media/windows-container/asp-net-sample-app.png)

> [!Note]
> Se você receber um tempo limite de conexão ao tentar carregar a página, verifique se o aplicativo de exemplo está pronto com o seguinte comando [kubectl Get pods--Watch]. Às vezes, o contêiner do Windows não será iniciado no momento em que o endereço IP externo estiver disponível.

## <a name="delete-cluster"></a>Excluir cluster

Quando o cluster não for mais necessário, use o comando [az group delete][az-group-delete] para remover o grupo de recursos, o serviço de contêiner e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Quando você excluir o cluster, a entidade de serviço do Azure Active Directory usada pelo cluster do AKS não será removida. Para obter as etapas para remover a entidade de serviço, confira [Considerações sobre a entidade de serviço do AKS e sua exclusão][sp-delete]. Se você tiver usado uma identidade gerenciada, ela será gerenciada pela plataforma e não exigirá remoção.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você implantou um cluster kubernetes e implantou um aplicativo de exemplo ASP.NET em um contêiner do Windows Server para ele. [Acesse o painel da Web do Kubernetes][kubernetes-dashboard] para o cluster recém-criado.

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
[aks-monitor]: ../azure-monitor/containers/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks#az-aks-install-cli
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
[use-advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[windows-server-password]: /windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference