---
title: Use o Azure Active Directory no Serviço de Kubernetes do Azure
description: Saiba como usar o Azure Active Directory no Serviço de Kubernetes do Azure (AKS)
services: container-service
ms.topic: article
ms.date: 02/1/2021
ms.author: miwithro
ms.openlocfilehash: 7f6cf503a459175e3109a515b666bbeaa3a25b4d
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99549992"
---
# <a name="aks-managed-azure-active-directory-integration"></a>Integração de Azure Active Directory gerenciados pelo AKS

A integração do Azure AD gerenciada pelo AKS foi projetada para simplificar a experiência de integração do Azure AD, em que os usuários eram solicitados anteriormente a criar um aplicativo cliente, um aplicativo de servidor e exigiram que o locatário do Azure AD concedesse permissões de leitura de diretório. Na nova versão, o provedor de recursos AKS gerencia os aplicativos de cliente e de servidor para você.

## <a name="azure-ad-authentication-overview"></a>Visão geral da autenticação do Azure AD

Os administradores de cluster podem configurar o controle de acesso baseado em função do kubernetes (kubernetes RBAC) com base na identidade do usuário ou no grupo de diretórios. A autenticação do Azure AD é fornecida aos clusters do AKS com OpenID Connect. O OpenID Connect é uma camada de identidade compilada sobre o protocolo OAuth 2.0. Para obter mais informações sobre o OpenID Connect, consulte a [documentação do Open ID Connect][open-id-connect].

Saiba mais sobre o fluxo de integração do Azure AD na [documentação de conceitos de integração do Azure Active Directory](concepts-identity.md#azure-active-directory-integration).

## <a name="limitations"></a>Limitações 

* A integração do Azure AD gerenciada pelo AKS não pode ser desabilitada
* Não há suporte para clusters habilitados para RBAC não kubernetes para integração do Azure AD gerenciada pelo AKS
* Não há suporte para a alteração do locatário do Azure AD associado à integração do Azure AD AKS

## <a name="prerequisites"></a>Pré-requisitos

* O CLI do Azure versão 2.11.0 ou posterior
* Kubectl com uma versão mínima de [1.18.1](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1181) ou [kubelogin](https://github.com/Azure/kubelogin)
* Se você estiver usando o [Helm](https://github.com/helm/helm), a versão mínima do Helm 3,3.

> [!Important]
> Você deve usar Kubectl com uma versão mínima de 1.18.1 ou kubelogin. Se você não usar a versão correta, observará problemas de autenticação.

Para instalar o kubectl e o kubelogin, use os seguintes comandos:

```azurecli-interactive
sudo az aks install-cli
kubectl version --client
kubelogin --version
```

Use [essas instruções](https://kubernetes.io/docs/tasks/tools/install-kubectl/) para outros sistemas operacionais.

## <a name="before-you-begin"></a>Antes de começar

Para o cluster, você precisa de um grupo do Azure AD. Esse grupo é necessário como grupo de administradores para que o cluster conceda permissões de administrador de cluster. Você pode usar um grupo existente do Azure AD ou criar um novo. Registre a ID de objeto do seu grupo do Azure AD.

```azurecli-interactive
# List existing groups in the directory
az ad group list --filter "displayname eq '<group-name>'" -o table
```

Para criar um novo grupo do Azure AD para seus administradores de cluster, use o seguinte comando:

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name myAKSAdminGroup --mail-nickname myAKSAdminGroup
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Criar um cluster do AKS habilitado para o Azure AD

Crie um cluster AKS usando os comandos da CLI a seguir.

Criar um grupo de recursos do Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Criar um cluster AKS e habilitar o acesso de administração para seu grupo do Azure AD

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Uma criação bem-sucedida de um cluster do Azure AD gerenciado por AKS tem a seção a seguir no corpo da resposta
```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Depois que o cluster for criado, você poderá começar a acessá-lo.

## <a name="access-an-azure-ad-enabled-cluster"></a>Acessar um cluster habilitado do Azure AD

Você precisará da função interna de [usuário do cluster do serviço kubernetes do Azure](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) para executar as etapas a seguir.

Obtenha as credenciais do usuário para acessar o cluster:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
Siga as instruções em para entrar.

Use o comando kubectl Get Nodes para exibir os nós no cluster:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
Configure o [controle de acesso baseado em função do Azure (RBAC do Azure)](./azure-ad-rbac.md) para configurar grupos de segurança adicionais para seus clusters.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Solucionando problemas de acesso com o Azure AD

> [!Important]
> As etapas descritas abaixo estão ignorando a autenticação de grupo do Azure AD normal. Use-os somente em uma emergência.

Se você estiver permanentemente bloqueado por não ter acesso a um grupo válido do Azure AD com acesso ao seu cluster, ainda poderá obter as credenciais de administrador para acessar o cluster diretamente.

Para executar essas etapas, você precisará ter acesso à função interna de [administrador de cluster do serviço kubernetes do Azure](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) .

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster --admin
```

## <a name="enable-aks-managed-azure-ad-integration-on-your-existing-cluster"></a>Habilitar a integração do Azure AD gerenciada pelo AKS em seu cluster existente

Você pode habilitar a integração do Azure AD AKS gerenciada em seu cluster habilitado do kubernetes RBAC existente. Certifique-se de definir seu grupo de administradores para manter o acesso ao cluster.

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad --aad-admin-group-object-ids <id-1> [--aad-tenant-id <id>]
```

Uma ativação bem-sucedida de um cluster do Azure AD gerenciado por AKS tem a seção a seguir no corpo da resposta

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Baixe as credenciais do usuário novamente para acessar o cluster seguindo as etapas [aqui][access-cluster].

## <a name="upgrading-to-aks-managed-azure-ad-integration"></a>Atualizando para a integração do Azure AD gerenciada pelo AKS

Se o cluster usar a integração herdada do Azure AD, você poderá atualizar para a integração do Azure AD gerenciada pelo AKS.

```azurecli-interactive
az aks update -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Uma migração bem-sucedida de um cluster do Azure AD gerenciado por AKS tem a seção a seguir no corpo da resposta

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Se você quiser acessar o cluster, siga as etapas [aqui][access-cluster].

## <a name="non-interactive-sign-in-with-kubelogin"></a>Entrada não interativa com kubelogin

Há alguns cenários não interativos, como pipelines de integração contínua, que atualmente não estão disponíveis com kubectl. Você pode usar [`kubelogin`](https://github.com/Azure/kubelogin) o para acessar o cluster com a entrada de entidade de serviço não interativa.

## <a name="use-conditional-access-with-azure-ad-and-aks"></a>Usar o acesso condicional com o Azure AD e o AKS

Ao integrar o Azure AD ao cluster do AKS, você também pode usar o [acesso condicional][aad-conditional-access] para controlar o acesso ao cluster.

> [!NOTE]
> O acesso condicional do Azure AD é um recurso Azure AD Premium.

Para criar um exemplo de política de acesso condicional para usar com o AKS, conclua as seguintes etapas:

1. Na parte superior da portal do Azure, procure e selecione Azure Active Directory.
1. No menu para Azure Active Directory no lado esquerdo, selecione *aplicativos empresariais*.
1. No menu para aplicativos empresariais no lado esquerdo, selecione *acesso condicional*.
1. No menu para acesso condicional no lado esquerdo, selecione *políticas* e, em seguida, *nova política*.
    :::image type="content" source="./media/managed-aad/conditional-access-new-policy.png" alt-text="Adicionando uma política de acesso condicional":::
1. Insira um nome para a política, como *AKs-Policy*.
1. Selecione *usuários e grupos* e, em *incluir* , selecione *Selecionar usuários e grupos*. Escolha os usuários e grupos nos quais você deseja aplicar a política. Para este exemplo, escolha o mesmo grupo do Azure AD que tem acesso de administração ao cluster.
    :::image type="content" source="./media/managed-aad/conditional-access-users-groups.png" alt-text="Selecionando usuários ou grupos para aplicar a política de acesso condicional":::
1. Selecione *aplicativos de nuvem ou ações* e, em *incluir* , selecione *selecionar aplicativos*. Pesquise o *serviço kubernetes do Azure* e selecione *servidor AAD do serviço kubernetes do Azure*.
    :::image type="content" source="./media/managed-aad/conditional-access-apps.png" alt-text="Selecionando o servidor AD do serviço kubernetes do Azure para aplicar a política de acesso condicional":::
1. Em *Controles de acesso*, selecione *Conceder*. Selecione *conceder acesso* e, em seguida, *exigir que o dispositivo seja marcado como em conformidade*.
    :::image type="content" source="./media/managed-aad/conditional-access-grant-compliant.png" alt-text="Selecionando para permitir somente dispositivos em conformidade para a política de acesso condicional":::
1. Em *habilitar política*, selecione *ativado* e *criar*.
    :::image type="content" source="./media/managed-aad/conditional-access-enable-policy.png" alt-text="Habilitando a política de acesso condicional":::

Obtenha as credenciais do usuário para acessar o cluster, por exemplo:

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

Siga as instruções em para entrar.

Use o `kubectl get nodes` comando para exibir os nós no cluster:

```azurecli-interactive
kubectl get nodes
```

Siga as instruções para entrar novamente. Observe que há uma mensagem de erro informando que você está conectado com êxito, mas seu administrador requer que o dispositivo que solicita o acesso seja gerenciado pelo AD do Azure para acessar o recurso.

Na portal do Azure, navegue até Azure Active Directory, selecione *aplicativos empresariais* e, em *atividade* , selecione *entradas*. Observe uma entrada na parte superior com um *status* de *falha* e um *acesso condicional* de *êxito*. Selecione a entrada e, em seguida, selecione *acesso condicional* em *detalhes*. Observe que a política de acesso condicional está listada.

:::image type="content" source="./media/managed-aad/conditional-access-sign-in-activity.png" alt-text="Falha na entrada de logon devido à política de acesso condicional":::

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [a integração do RBAC do Azure para autorização de kubernetes][azure-rbac-integration]
* Saiba mais sobre [a integração do Azure AD com o KUBERNETES RBAC][azure-ad-rbac].
* Use o [kubelogin](https://github.com/Azure/kubelogin) para acessar recursos para a autenticação do Azure que não estão disponíveis no kubectl.
* Saiba mais sobre os [conceitos de identidade AKs e kubernetes][aks-concepts-identity].
* Use [modelos de Azure Resource Manager (ARM) ][aks-arm-template] para criar clusters habilitados do Azure ad gerenciados por AKs.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters

<!-- LINKS - Internal -->
[aad-conditional-access]: ../active-directory/conditional-access/overview.md
[azure-rbac-integration]: manage-azure-rbac.md
[aks-concepts-identity]: concepts-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration
