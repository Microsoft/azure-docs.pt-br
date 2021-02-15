---
title: Gerenciar um cluster do Serviço de Kubernetes do Azure com o painel da Web
description: Saiba como usar o painel da interface do usuário Web interna do Kubernetes para gerenciar um cluster do AKS (Serviço de Kubernetes do Azure)
services: container-service
author: mlearned
ms.topic: article
ms.date: 06/03/2020
ms.author: mlearned
ms.openlocfilehash: 85f0a42cdfcbea2223d202a9dc35f58746580e85
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350119"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Acessar o painel da Web do Kubernetes no AKS (Serviço de Kubernetes do Azure)

Kubernetes inclui um painel da web que pode ser usado para operações básicas de gerenciamento. Este painel permite exibir o status de integridade básico e as métricas dos seus aplicativos, criar e implantar serviços e editar aplicativos existentes. Este artigo mostra como acessar o painel do Kubernetes usando a CLI do Azure e, em seguida, orienta você por meio de algumas operações básicas do painel.

Para obter mais informações sobre o painel do Kubernetes, consulte [Painel de interface do usuário Web do Kubernetes][kubernetes-dashboard]. O AKS usa a versão 2,0 e superior do painel de código-fonte aberto.

> [!WARNING]
> **O complemento do painel do AKS está definido para substituição. Use a [exibição de recurso kubernetes no portal do Azure (visualização)][kubernetes-portal] em vez disso.** 
> * O painel do kubernetes é habilitado por padrão para clusters que executam uma versão do kubernetes inferior a 1,18.
> * O complemento do painel será desabilitado por padrão para todos os novos clusters criados no kubernetes 1,18 ou superior. 
 > * A partir do kubernetes 1,19 em versão prévia, o AKS não dará mais suporte à instalação do complemento Kube-Dashboard gerenciado. 
 > * Os clusters existentes com o complemento habilitado não serão afetados. Os usuários continuarão a ser capazes de instalar manualmente o painel de código-fonte aberto como um software instalado pelo usuário.

## <a name="before-you-begin"></a>Antes de começar

As etapas detalhadas neste documento pressupõem que você criou um cluster AKS e estabeleceu uma `kubectl` conexão com o cluster. Se você precisar criar um cluster AKS, consulte [início rápido: implantar um cluster do serviço kubernetes do Azure usando o CLI do Azure][aks-quickstart].

Você também precisa do CLI do Azure versão 2.6.0 ou posterior instalada e configurada. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][install-azure-cli].

## <a name="disable-the-kubernetes-dashboard"></a>Desabilitar o painel do kubernetes

O complemento Kube-Dashboard está **habilitado por padrão em clusters com mais de K8s 1,18**. O complemento pode ser desabilitado executando o comando a seguir.

``` azurecli
az aks disable-addons -g myRG -n myAKScluster -a kube-dashboard
```

## <a name="start-the-kubernetes-dashboard"></a>Iniciar o painel do Kubernetes

> [!WARNING]
> O complemento do painel do AKS foi preterido para as versões 1.19 +. Use a [exibição de recurso kubernetes no portal do Azure (versão prévia)][kubernetes-portal] em vez disso. 
> * O comando a seguir agora abrirá o modo de exibição de recursos do portal do Azure em vez do painel do kubernetes para as versões 1,19 e posteriores.

Para iniciar o painel do kubernetes em um cluster, use o comando [AZ AKs Browse][az-aks-browse] . Esse comando requer a instalação do complemento Kube no cluster, que é incluído por padrão em clusters que executam qualquer versão anterior à kubernetes 1,18.

O exemplo abaixo abre o painel para o cluster nomeado *myAKSCluster* em um grupo de recursos chamado *myResourceGroup*:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Este comando cria um proxy entre o sistema de desenvolvimento e a API do Kubernetes, e abre um navegador da Web para o painel do Kubernetes. Se o navegador da Web não abrir o painel do Kubernetes, copie e cole o endereço da URL anotado na CLI do Azure, que geralmente é `http://127.0.0.1:8001`.

> [!NOTE]
> Se você não vir o painel em, `http://127.0.0.1:8001` poderá rotear manualmente para os endereços a seguir. Os clusters em 1,16 ou superior usam https e exigem um ponto de extremidade separado.
> * K8s 1,16 ou superior: `http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`
> * K8s 1,15 e inferior: `http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard:/proxy`

<!--
![The login page of the Kubernetes web dashboard](./media/kubernetes-dashboard/dashboard-login.png)

You have the following options to sign in to your cluster's dashboard:

* A [kubeconfig file][kubeconfig-file]. You can generate a kubeconfig file using [az aks get-credentials][az-aks-get-credentials].
* A token, such as a [service account token][aks-service-accounts] or user token. On [AAD-enabled clusters][aad-cluster], this token would be an AAD token. You can use `kubectl config view` to list the tokens in your kubeconfig file. For more details on creating an AAD token for use with an AKS cluster see [Integrate Azure Active Directory with Azure Kubernetes Service using the Azure CLI][aad-cluster].
* The default dashboard service account, which is used if you click *Skip*.

> [!WARNING]
> Never expose the Kubernetes dashboard publicly, regardless of the authentication method used.
> 
> When setting up authentication for the Kubernetes dashboard, it is recommended that you use a token over the default dashboard service account. A token allows each user to use their own permissions. Using the default dashboard service account may allow a user to bypass their own permissions and use the service account instead.
> 
> If you do choose to use the default dashboard service account and your AKS cluster uses Kubernetes RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays Kubernetes RBAC access errors. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
>
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command as shown in the following example. **This sample binding does not apply any additional authentication components and may lead to insecure use.**
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> You can now access the Kubernetes dashboard in your Kubernetes RBAC-enabled cluster. To start the Kubernetes dashboard, use the [az aks browse][az-aks-browse] command as detailed in the previous step.
>
> If your cluster does not use Kubernetes RBAC, it is not recommended to create a *ClusterRoleBinding*.
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].

After you choose a method to sign in, the Kubernetes dashboard is displayed. If you chose to use *token* or *skip*, the Kubernetes dashboard will use the permissions of the currently logged in user to access the cluster.

> [!IMPORTANT]
> If your AKS cluster uses Kubernetes RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays Kubernetes RBAC access errors. The Kubernetes dashboard does not currently support user-provided credentials to determine the level of access, rather it uses the roles granted to the service account. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
> 
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command. The following example shows how to create a sample binding, however, this sample binding does not apply any additional authentication components and may lead to insecure use. The Kubernetes dashboard is open to anyone with access to the URL. Do not expose the Kubernetes dashboard publicly.
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].
-->

## <a name="sign-in-to-the-dashboard-kubernetes-116"></a>Entrar no painel (kubernetes 1.16 +)

> [!IMPORTANT]
> A partir de [v 1.10.1 do painel do kubernetes](https://github.com/kubernetes/dashboard/releases/tag/v1.10.1) ou do kubernetes v 1.16 + a conta de serviço "kubernetes-Dashboard" não pode mais ser usada para recuperar recursos devido a uma [correção de segurança nessa versão](https://github.com/kubernetes/dashboard/pull/3400). Como resultado, as solicitações sem informações de autenticação retornam um [erro não autorizado 401](https://github.com/Azure/AKS/issues/1573#issuecomment-703040998). Um token de portador recuperado de uma conta de serviço ainda pode ser usado como neste [exemplo de painel do kubernetes](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/#accessing-the-dashboard-ui), mas isso afeta o fluxo de logon do complemento do painel em comparação com as versões mais antigas.
>
>Se você ainda executar uma versão anterior a 1,16, ainda poderá conceder permissões para a conta de serviço "kubernetes-Dashboard", mas isso **não é recomendado**:
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```

A tela inicial apresentada requer um kubeconfig ou token. Ambas as opções exigem permissões de recurso para exibir esses recursos no painel.

![tela de logon](./media/kubernetes-dashboard/login.png)

**Usar um kubeconfig**

Para os clusters habilitados para o Azure AD e não para o Azure AD, um kubeconfig pode ser passado. Verifique se os tokens de acesso são válidos, se os tokens estão expirados, você pode atualizar tokens via kubectl.

1. Definir o kubeconfig do administrador com `az aks get-credentials -a --resource-group <RG_NAME> --name <CLUSTER_NAME>`
1. Selecione `Kubeconfig` e clique `Choose kubeconfig file` para abrir o seletor de arquivo
1. Selecione o arquivo kubeconfig (o padrão é $HOME/.Kube/config)
1. Clique em `Sign In`

**Usar um token**

1. Para um **cluster habilitado para não Azure ad**, execute `kubectl config view` e copie o token associado à conta de usuário do cluster.
1. Cole na opção de token na entrada.    
1. Clique em `Sign In`

Para clusters habilitados do Azure AD, recupere o token do AAD com o comando a seguir. Valide que você substituiu o grupo de recursos e o nome do cluster no comando.

```
## Update <RESOURCE_GROUP and <AKS_NAME> with your input.

kubectl config view -o jsonpath='{.users[?(@.name == "clusterUser_<RESOURCE GROUP>_<AKS_NAME>")].user.auth-provider.config.access-token}'
```

Uma vez bem-sucedido, uma página semelhante à mostrada abaixo será exibida.

![A página de visão geral do painel da Web do Kubernetes](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>Criar um aplicativo

As etapas a seguir exigem que o usuário tenha permissões para os respectivos recursos. 

Para ver como o painel do Kubernetes pode reduzir a complexidade das tarefas de gerenciamento, vamos criar um aplicativo. Você pode criar um aplicativo usando o painel do Kubernetes fornecendo texto de entrada e um arquivo YAML ou por meio de um assistente gráfico.

Para criar um aplicativo, conclua as seguintes etapas:

1. Selecione o botão **Criar** na janela superior direita.
1. Para usar o assistente gráfico, escolha **Criar um aplicativo**.
1. Forneça um nome para a implantação, como *nginx*
1. Insira o nome da imagem de contêiner a ser usada, como *nginx:1.15.5*
1. Para expor a porta 80 para o tráfego da Web, crie um serviço de Kubernetes. Em **Serviço**, selecione **Externo** e insira **80** para a porta e para a porta de destino.
1. Quando estiver pronto, selecione **Implantar** para criar o aplicativo.

![Implantar um aplicativo no painel da Web do Kubernetes](./media/kubernetes-dashboard/create-app.png)

Leva um minuto ou dois para que um endereço IP externo seja atribuído ao serviço de Kubernetes. No lado esquerdo, em **Descoberta e Balanceamento de Carga** selecione **Serviços**. O serviço do aplicativo é listado, incluindo os *Pontos de extremidade externos*, conforme é mostrado no exemplo a seguir:

![Exibir uma lista de serviços e pontos de extremidade](./media/kubernetes-dashboard/view-services.png)

Selecione o endereço do ponto de extremidade para abrir uma janela do navegador da Web para a página padrão do NGINX:

![Exibir a página do NGINX padrão do aplicativo implantado](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Exibir informações de pod

O painel do Kubernetes pode fornecer o monitoramento básico de métricas e informações de solução de problemas, como logs.

Para obter mais informações sobre os pods de aplicativo, selecione **Pods** no menu à esquerda. A lista de pods disponíveis é mostrada. Escolha o pod *nginx* para exibir informações, como o consumo de recursos:

![Exibir informações de pod](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>Editar o aplicativo

Além de criar e exibir os aplicativos, o painel do Kubernetes pode ser usado para editar e atualizar implantações de aplicativo. Para fornecer redundância adicional ao aplicativo, vamos aumentar o número de réplicas do NGINX.

Para editar uma implantação:

1. Selecione **Implantações** no menu à esquerda e, em seguida, escolha a implantação *nginx*.
1. Selecione **Editar** na barra de navegação superior à direita.
1. Localize o valor `spec.replica`, próximo à linha 20. Para aumentar o número de réplicas do aplicativo, altere esse valor de *1* para *3*.
1. Selecione **Atualizar** quando estiver pronto.

![Edite a implantação para atualizar o número de réplicas](./media/kubernetes-dashboard/edit-deployment.png)

Levará alguns instantes para que os pods sejam criados dentro de um conjunto de réplicas. No menu à esquerda, escolha **Conjuntos de Réplicas** e, em seguida, escolha o conjunto de réplicas *nginx*. A lista de pods agora reflete a contagem de réplica atualizada, conforme é mostrado na saída de exemplo a seguir:

![Exibir informações sobre o conjunto de réplicas](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o painel do Kubernetes, confira [Painel da interface do usuário da Web do Kubernetes][kubernetes-dashboard].

<!-- LINKS - external -->
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubeconfig-file]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

<!-- LINKS - internal -->
[aad-cluster]: ./azure-ad-integration-cli.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[aks-service-accounts]: ./concepts-identity.md#kubernetes-service-accounts
[az-account-get-access-token]: /cli/azure/account?view=azure-cli-latest#az-account-get-access-token
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[kubernetes-portal]: ./kubernetes-portal.md
