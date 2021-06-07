---
title: 'Início Rápido: Implantar um cluster do AKS usando a CLI do Azure'
description: Saiba como criar rapidamente um cluster do Kubernetes, implantar um aplicativo e monitorar o desempenho no AKS (Serviço de Kubernetes do Azure) usando a CLI do Azure.
services: container-service
ms.topic: quickstart
ms.date: 02/26/2021
ms.custom:
- H1Hack27Feb2017
- mvc
- devcenter
- seo-javascript-september2019
- seo-javascript-october2019
- seo-python-october2019
- devx-track-azurecli
- contperf-fy21q1
ms.openlocfilehash: 8adfd1a6e26a3381653ca9a794b124e201b9d481
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106697"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-the-azure-cli"></a>Início Rápido: Implantar um cluster do Serviço de Kubernetes do Azure usando a CLI do Azure

O AKS (Serviço de Kubernetes do Azure) é um serviço de Kubernetes gerenciado que permite implantar e gerenciar clusters rapidamente. Neste guia de início rápido, você vai:
* Implantar um cluster do AKS usando a CLI do Azure. 
* Executar um aplicativo de vários contêineres com um front-end da Web e uma instância do Redis no cluster. 
* Monitorar a integridade do cluster e dos pods que executam o aplicativo.

  ![Aplicativo de votação implantado no Serviço de Kubernetes do Azure](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

Este guia de início rápido pressupõe uma compreensão básica dos conceitos do Kubernetes. Para obter mais informações, confira [Principais conceitos do Kubernetes para o AKS (Serviço de Kubernetes do Azure)][kubernetes-concepts].

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Para saber mais sobre como criar um pool de nós do Windows Server, confira [Criar um cluster do AKS compatível com contêineres do Windows Server][windows-container-cli].

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo exige a versão 2.0.64 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

> [!NOTE]
> Execute os comandos como administrador se você planeja executar os comandos neste início rápido localmente, e não no Azure Cloud Shell.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um [grupo de recursos do Azure](../azure-resource-manager/management/overview.md) é um grupo lógico no qual os recursos do Azure são implantados e gerenciados. Ao criar um grupo de recursos, você verá um prompt para especificar uma localização. Essa localização é: 
* A localização de armazenamento dos metadados do grupo de recursos.
* Onde seus recursos serão executados no Azure caso você não especifique outra região durante a criação de recursos. 

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*.

Crie um grupo de recursos usando o comando [az group create][az-group-create].


```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Saída do grupo de recursos criado com êxito:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="enable-cluster-monitoring"></a>Habilitar o monitoramento do cluster

1. Verifique se *Microsoft.OperationsManagement* e *Microsoft.OperationalInsights* estão registrados em sua assinatura. Para verificar o status do registro:

    ```azurecli
    az provider show -n Microsoft.OperationsManagement -o table
    az provider show -n Microsoft.OperationalInsights -o table
    ```
 
    Se ainda não estiverem registrados, registre *Microsoft.OperationsManagement* e *Microsoft.OperationalInsights* usando:
 
    ```azurecli
    az provider register --namespace Microsoft.OperationsManagement
    az provider register --namespace Microsoft.OperationalInsights
    ```

2. Habilite o [Azure Monitor para contêineres][azure-monitor-containers] usando o parâmetro *--enable-addons monitoring*. 

## <a name="create-aks-cluster"></a>Criar cluster AKS

Crie um cluster do AKS usando o comando [az aks create][az-aks-create]. O seguinte exemplo cria um cluster chamado *myAKSCluster* com um nó: 

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1 --enable-addons monitoring --generate-ssh-keys
```

Após alguns minutos, o comando será concluído e retornará informações no formato JSON sobre o cluster.

> [!NOTE]
> Quando você cria um cluster do AKS, um segundo grupo de recursos é criado automaticamente para armazenar os recursos do AKS. Para obter mais informações, confira [Por que dois grupos de recursos são criados com o AKS?](./faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Conectar-se ao cluster

Para gerenciar um cluster do Kubernetes, use o cliente de linha de comando do Kubernetes, [kubectl][kubectl]. `kubectl` já está instalado se você usa o Azure Cloud Shell. 

1. Instale `kubectl` localmente usando o comando [az aks install-cli][az-aks-install-cli]:

    ```azurecli
    az aks install-cli
    ```

2. Configure o `kubectl` para se conectar ao cluster do Kubernetes usando o comando [az aks get-credentials][az-aks-get-credentials]. O seguinte comando:  
      * Baixa credenciais e configura a CLI de Kubernetes para usá-las.
      * Usa `~/.kube/config`, a localização padrão do [arquivo de configuração do Kubernetes][kubeconfig-file]. Especifique outra localização para o arquivo de configuração do Kubernetes usando *--file*.


    ```azurecli-interactive
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. Verifique a conexão com o cluster usando o comando [kubectl get][kubectl-get]. Esse comando retorna uma lista dos nós de cluster.

    ```azurecli-interactive
    kubectl get nodes
    ```

    A saída mostra o único nó criado nas etapas anteriores. Verifique se que o status do nó é *Pronto*:

    ```output
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
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
    * Se você usar o Azure Cloud Shell, esse arquivo poderá ser criado usando `code`, `vi` ou `nano`, como se você estivesse trabalhando em um sistema físico ou virtual
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

    ```console
    kubectl apply -f azure-vote.yaml
    ```

    A saída mostra as implantações e os serviços criados com êxito:

    ```output
    deployment "azure-vote-back" created
    service "azure-vote-back" created
    deployment "azure-vote-front" created
    service "azure-vote-front" created
    ```

## <a name="test-the-application"></a>Testar o aplicativo

Quando o aplicativo é executado, um serviço de Kubernetes expõe o front-end do aplicativo à Internet. A conclusão desse processo pode levar alguns minutos.

Monitore o andamento usando o comando [kubectl get service][kubectl-get] com o argumento `--watch`.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

A saída **EXTERNAL-IP** do serviço `azure-vote-front` será mostrada inicialmente como *pendente*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quando o endereço **EXTERNAL-IP** for alterado de *pendente* para um endereço IP público real, use `CTRL-C` para interromper o processo de inspeção do `kubectl`. A seguinte saída de exemplo mostra um endereço IP público válido atribuído ao serviço:

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Para ver o aplicativo Azure Vote em ação, abra um navegador da Web no endereço IP externo do serviço.

![Aplicativo de votação implantado no Serviço de Kubernetes do Azure](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

Exiba as métricas de integridade dos pods e dos nós de cluster capturadas pelo [Azure Monitor][azure-monitor-containers] para contêineres no portal do Azure. 

## <a name="delete-the-cluster"></a>Excluir o cluster

Para evitar encargos do Azure, limpe os recursos desnecessários. Use o comando [az group delete][az-group-delete] para remover o grupo de recursos, o serviço de contêiner e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
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
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeconfig-file]: https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: ../azure-monitor/containers/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks#az_aks_browse
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install_azure_cli
[azure-monitor-containers]: ../azure-monitor/containers/container-insights-overview.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[windows-container-cli]: windows-container-cli.md
