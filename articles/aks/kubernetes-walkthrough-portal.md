---
title: Criar um cluster AKS no portal
titleSuffix: Azure Kubernetes Service
description: Saiba como criar rapidamente um cluster do Kubernetes, implantar um aplicativo e monitorar o desempenho no AKS (Serviço de Kubernetes do Azure) usando o portal do Azure.
services: container-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.custom: mvc, seo-javascript-october2019
ms.openlocfilehash: 9304d3ee3cac364bfec3f2686de0cf84fb1d7d92
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98249118"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-the-azure-portal"></a>Início Rápido: Implantar um cluster do AKS (Serviço de Kubernetes do Azure) usando o portal do Azure

O AKS (Serviço de Kubernetes do Azure) é um serviço de Kubernetes gerenciado que permite implantar e gerenciar clusters rapidamente. Neste guia de início rápido, implante um cluster AKS usando o Portal do Azure. Um aplicativo de vários contêineres que inclui um front-end da Web e uma instância do Redis é executado no cluster. Em seguida, você verá como monitorar a integridade do cluster e dos pods que executam seu aplicativo.

![Imagem de navegação para o aplicativo Azure Vote de exemplo](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

Este guia de início rápido pressupõe uma compreensão básica dos conceitos do Kubernetes. Para obter mais informações, confira [Principais conceitos do Kubernetes para o AKS (Serviço de Kubernetes do Azure)][kubernetes-concepts].

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-aks-cluster"></a>Criar um cluster AKS

Para criar um cluster do AKS, conclua as seguintes etapas:

1. No menu do portal do Azure ou na **Página Inicial**, selecione **Criar um recurso**.

2. Selecione **contêineres** >  **Serviço Kubernetes**.

3. Na página **Noções básicas**, configure as seguintes opções:
    - **Detalhes do projeto**: Selecione uma **assinatura** do Azure e marque ou crie um **grupo de recursos** do Azure, por exemplo, *meuGrupodeRecursos*.
    - **Detalhes do cluster**: Insira um **nome do cluster do Kubernetes**, como *myAKSCluster*. Selecione uma **Região** e uma **versão do Kubernetes** para o cluster do AKS.
    - **Pool de nós primários**: Selecione um **Tamanho de nó** de VM para os nós de AKS. O tamanho da VM *não pode* ser alterado após a implantação de um cluster AKS.
            – Selecione o número de nós para implantação no cluster. Para este início rápido, defina **Contagem de nós** como *1*. A contagem de nós *pode* ser ajustada após a implantação do cluster.
    
    ![Criar cluster do AKS – fornecer informações básicas](media/kubernetes-walkthrough-portal/create-cluster-basics.png)

    Selecione **Avançar: Pools de nó** quando terminar.

4. Na página **Pools de nó**, mantenha as opções padrão. Na parte inferior da tela, clique em **Avançar: Autenticação**.
    > [!CAUTION]
    > A criação de novas Entidades de Serviço do AAD pode levar vários minutos para ser propagada e disponibilizada, levando a erros de Entidade de Serviço não encontrada e a falhas de validação no portal do Azure. Se passar por isso, veja as mitigações [aqui](troubleshooting.md#received-an-error-saying-my-service-principal-wasnt-found-or-is-invalid-when-i-try-to-create-a-new-cluster).

5. Na página **Autenticação**, configure as seguintes opções:
    - Crie uma nova entidade de serviço deixando o campo **Entidade de Serviço** com **(novo) entidade de serviço padrão**. Ou você pode escolher *Configurar entidade de serviço* para usar uma existente. Se você usar uma existente, será necessário fornecer a ID e segredo de cliente do SPN.
    - Habilite a opção para RBAC (controle de acesso baseado em função) do Kubernetes. Isso fornecerá um controle mais refinado sobre o acesso aos recursos do Kubernetes implantados no cluster do AKS.

    Como alternativa, é possível usar uma identidade gerenciada em vez de uma entidade de serviço. Confira [usar identidades gerenciadas](use-managed-identity.md) para obter mais informações.

Por padrão, a rede *Básica* é usada e o Azure Monitor para contêineres está habilitado. Clique em **Revisar+ criar** e depois em **Criar** quando terminar a validação.

Leva alguns minutos para o cluster do AKS ser criado. Após a conclusão da sua implantação, clique em **Acessar recurso**, ou navegue até o grupo de recursos do cluster AKS, como *myResourceGroup* e selecione o recurso do AKS, como *myAKSCluster*. O painel do cluster do AKS é mostrado, como neste exemplo:

![Painel do AKS de exemplo no portal do Azure](media/kubernetes-walkthrough-portal/aks-portal-dashboard.png)

## <a name="connect-to-the-cluster"></a>Conectar-se ao cluster

Para gerenciar um cluster do Kubernetes, use [kubectl][kubectl], o cliente de linha de comando do Kubernetes. O cliente `kubectl` está pré-instalado no Azure Cloud Shell.

Abra o Cloud Shell usando o botão `>_` na parte superior do portal do Azure.

![Abrir o Azure Cloud Shell no portal](media/kubernetes-walkthrough-portal/aks-cloud-shell.png)

> [!NOTE]
> Para executar essas operações em uma instalação de shell local, você precisará primeiro verificar se a CLI do Azure foi instalada e se conectar ao Azure por meio do comando `az login`.

Para configurar o `kubectl` para se conectar ao cluster do Kubernetes, use o comando [az aks get-credentials][az-aks-get-credentials]. Este comando baixa as credenciais e configura a CLI do Kubernetes para usá-las. O exemplo a seguir obtém as credenciais para o nome do cluster *myAKSCluster* no grupo de recursos chamado *myResourceGroup*:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a conexão com o cluster, use o comando [kubectl get][kubectl-get] para retornar uma lista dos nós de cluster.

```console
kubectl get nodes
```

A saída de exemplo a seguir mostra o único nó criado nas etapas anteriores. Verifique se o status do nó é *Pronto*:

```output
NAME                       STATUS    ROLES     AGE       VERSION
aks-agentpool-14693408-0   Ready     agent     15m       v1.11.5
```

## <a name="run-the-application"></a>Executar o aplicativo

Um arquivo de manifesto do Kubernetes define um estado desejado para o cluster, como as imagens de contêiner a serem executadas. Neste início rápido, um manifesto é usado para criar todos os objetos necessários para executar o aplicativo Azure Vote. Esse manifesto inclui duas [implantações do Kubernetes][kubernetes-deployment] – uma para os aplicativos de exemplo do Azure Vote Python e outra para uma instância do Redis. Dois [Serviços de Kubernetes][kubernetes-service] também são criados – um serviço interno para a instância do Redis e um serviço externo para acessar o aplicativo Azure Vote na Internet.

No Cloud Shell, use um editor para criar um arquivo chamado `azure-vote.yaml`, como `code azure-vote.yaml`, `nano azure-vote.yaml` ou `vi azure-vote.yaml`. Em seguida, copie a definição YAML a seguir:

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

Implante o aplicativo usando o comando [kubectl apply][kubectl-apply] e especifique o nome do manifesto YAML:

```console
kubectl apply -f azure-vote.yaml
```

A seguinte saída de exemplo mostra as Implantações e os Serviços criados com êxito:

```output
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Testar o aplicativo

Quando o aplicativo é executado, um serviço de Kubernetes expõe o front-end do aplicativo à Internet. A conclusão desse processo pode levar alguns minutos.

Para monitorar o andamento, use o comando [kubectl get service][kubectl-get] com o argumento `--watch`.

```console
kubectl get service azure-vote-front --watch
```

Inicialmente, o *EXTERNAL-IP* para o serviço *azure-vote-front* é mostrado como *pendente*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quando o endereço *EXTERNAL-IP* for alterado de *pendente* para um endereço IP público real, use `CTRL-C` para interromper o processo de inspeção do `kubectl`. A seguinte saída de exemplo mostra um endereço IP público válido atribuído ao serviço:

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Para ver o aplicativo Azure Vote em ação, abra um navegador da Web no endereço IP externo do serviço.

![Imagem de navegação para o aplicativo Azure Vote de exemplo](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="monitor-health-and-logs"></a>Monitorar integridade e logs

Quando você criou o cluster, o Azure Monitor para contêineres foi habilitado. Esse recurso de monitoramento fornece métricas de integridade para o cluster do AKS e pods em execução no cluster.

Talvez demore alguns minutos para que esses dados sejam preenchidos no Portal do Azure. Para ver o status atual, o tempo de atividade e o uso de recursos dos pods do Azure Vote, navegue para o recurso de AKS no portal do Azure, por exemplo, *myAKSCluster*. Em seguida, você pode acessar o status da integridade da seguinte maneira:

1. Em **Monitoramento**, no lado esquerdo, escolha **Insights**
1. Na parte superior, escolha **+ Adicionar Filtro**
1. Selecione *Namespace* como a propriedade e escolha *\<All but kube-system\>*
1. Escolha exibir os **Contêineres**.

Os contêineres *azure-vote-back* e *azure-vote-front* são exibidos, conforme mostrado no exemplo a seguir:

![Exibir a integridade de contêineres em execução no AKS](media/kubernetes-walkthrough-portal/monitor-containers.png)

Para ver os logs do pod `azure-vote-front`, selecione **Exibir logs de contêiner** no menu suspenso da lista de contêineres. Esses logs incluem os fluxos *stdout* e *stderr* do contêiner.

![Exibir os logs de contêineres no AKS](media/kubernetes-walkthrough-portal/monitor-container-logs.png)

## <a name="delete-cluster"></a>Excluir cluster

Quando o cluster não for mais necessário, exclua o recurso de cluster, o que excluirá todos os recursos associados. Essa operação pode ser concluída no portal do Azure selecionando o botão **Excluir** no painel do cluster do AKS. Como alternativa, o comando [az aks delete][az-aks-delete] pode ser usado no Cloud Shell:

```azurecli
az aks delete --resource-group myResourceGroup --name myAKSCluster --no-wait
```

> [!NOTE]
> Quando você excluir o cluster, a entidade de serviço do Azure Active Directory usada pelo cluster do AKS não será removida. Para obter as etapas para remover a entidade de serviço, confira [Considerações sobre a entidade de serviço do AKS e sua exclusão][sp-delete]. Se você tiver usado uma identidade gerenciada, ela será gerenciada pela plataforma e não exigirá remoção.

## <a name="get-the-code"></a>Obter o código

Neste início rápido, foram usadas imagens de contêiner pré-criadas para criar uma implantação do Kubernetes. O código de aplicativo relacionado, o Dockerfile e o arquivo de manifesto Kubernetes estão disponíveis no GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou um cluster Kubernetes e um aplicativo de com vários contêineres nele.

Para saber mais sobre o AKS e percorrer um código completo de exemplo de implantação, prossiga para o tutorial de cluster Kubernetes.

> [!div class="nextstepaction"]
> [Tutorial do AKS][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-documentation]: https://kubernetes.io/docs/home/

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-get-credentials
[az-aks-delete]: /cli/azure/aks#az-aks-delete
[aks-monitor]: ../azure-monitor/insights/container-insights-overview.md
[aks-network]: ./concepts-network.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[http-routing]: ./http-application-routing.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-dev-spaces]: ../dev-spaces/index.yml
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
