---
title: Use o Azure Active Directory no Serviço de Kubernetes do Azure
description: Saiba como usar o Azure Active Directory no Serviço de Kubernetes do Azure (AKS)
services: container-service
ms.topic: article
ms.date: 02/1/2021
ms.author: miwithro
ms.openlocfilehash: b7918ecc31fe152bd25153ac8c899ce3ff8fdacb
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640592"
---
# <a name="aks-managed-azure-active-directory-integration"></a>Integração de Azure Active Directory gerenciados pelo AKS

A integração do Azure AD gerenciada pelo AKS foi projetada para simplificar a experiência de integração do Azure AD, em que os usuários eram solicitados anteriormente a criar um aplicativo cliente, um aplicativo de servidor e exigiram que o locatário do Azure AD concedesse permissões de leitura de diretório. Na nova versão, o provedor de recursos AKS gerencia os aplicativos de cliente e de servidor para você.

## <a name="azure-ad-authentication-overview"></a>Visão geral da autenticação do Azure AD

Os administradores de cluster podem configurar o controle de acesso baseado em função do kubernetes (kubernetes RBAC) com base na identidade do usuário ou no grupo de diretórios. A autenticação do Azure AD é fornecida aos clusters do AKS com OpenID Connect. O OpenID Connect é uma camada de identidade compilada sobre o protocolo OAuth 2.0. Para obter mais informações sobre o OpenID Connect, consulte a [documentação do Open ID Connect][open-id-connect].

Saiba mais sobre o fluxo de integração do Azure AD na [documentação de conceitos de integração do Azure Active Directory](concepts-identity.md#azure-active-directory-integration).

## <a name="limitations"></a>Limitações 

* A integração do Azure AD gerenciada pelo AKS não pode ser desabilitada
* Não há suporte para a alteração de um cluster integrado do Azure AD AKS para o AAD herdado
* Não há suporte para clusters habilitados para RBAC não kubernetes para integração do Azure AD gerenciada pelo AKS
* Não há suporte para a alteração do locatário do Azure AD associado à integração do Azure AD AKS

## <a name="prerequisites"></a>Pré-requisitos

* O CLI do Azure versão 2.11.0 ou posterior
* Kubectl com uma versão mínima de [1.18.1](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1181) ou [kubelogin](https://github.com/Azure/kubelogin)
* Se você estiver usando o [Helm](https://github.com/helm/helm), a versão mínima do Helm 3,3.

> [!Important]
> Você deve usar Kubectl com uma versão mínima de 1.18.1 ou kubelogin. A diferença entre as versões secundárias de kubernetes e kubectl não deve ser superior a 1 versão. Se você não usar a versão correta, observará problemas de autenticação.

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

## <a name="configure-just-in-time-cluster-access-with-azure-ad-and-aks"></a>Configurar o acesso de cluster just-in-time com o Azure AD e o AKS

Outra opção para o controle de acesso ao cluster é usar Privileged Identity Management (PIM) para solicitações just-in-time.

>[!NOTE]
> O PIM é um recurso Azure AD Premium que requer uma SKU Premium P2. Para obter mais informações sobre SKUs do Azure AD, consulte o [Guia de preços][aad-pricing].

Para integrar solicitações de acesso just-in-time com um cluster AKS usando a integração do Azure AD gerenciada pelo AKS, conclua as seguintes etapas:

1. Na parte superior da portal do Azure, procure e selecione Azure Active Directory.
1. Anote a ID do locatário, mencionada para o restante dessas instruções como `<tenant-id>` :::image type="content" source="./media/managed-aad/jit-get-tenant-id.png" alt-text="em um navegador da Web, a tela de portal do Azure para Azure Active Directory é mostrada com a ID do locatário realçada.":::
1. No menu para Azure Active Directory no lado esquerdo, em *gerenciar* , selecione *grupos* e *novo grupo*.
    :::image type="content" source="./media/managed-aad/jit-create-new-group.png" alt-text="Mostra a tela portal do Azure grupos de Active Directory com a opção ' novo grupo ' realçada.":::
1. Verifique se um tipo de *segurança* de grupo está selecionado e insira um nome de grupo, como *myJITGroup*. Em *funções do Azure ad podem ser atribuídas a este grupo (versão prévia)*, selecione *Sim*. Por fim, selecione *Criar*.
    :::image type="content" source="./media/managed-aad/jit-new-group-created.png" alt-text="Mostra a nova tela de criação de grupo do portal do Azure.":::
1. Você será levado de volta à página *grupos* . Selecione seu grupo recém-criado e anote a ID do objeto, referida para o restante dessas instruções como `<object-id>` .
    :::image type="content" source="./media/managed-aad/jit-get-object-id.png" alt-text="Mostra a tela de portal do Azure do grupo recém-criado, destacando a ID do objeto":::
1. Implante um cluster AKS com a integração do Azure AD gerenciada pelo AKS usando os `<tenant-id>` `<object-id>` valores e anteriores:
    ```azurecli-interactive
    az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <object-id> --aad-tenant-id <tenant-id>
    ```
1. De volta ao portal do Azure, no menu para *atividade* no lado esquerdo, selecione *acesso privilegiado (versão prévia)* e selecione *habilitar acesso privilegiado*.
    :::image type="content" source="./media/managed-aad/jit-enabling-priv-access.png" alt-text="A página de acesso privilegiado do portal do Azure (visualização) é mostrada, com ' habilitar acesso privilegiado ' realçado":::
1. Selecione *Adicionar atribuições* para começar a conceder acesso.
    :::image type="content" source="./media/managed-aad/jit-add-active-assignment.png" alt-text="A tela de acesso privilegiado do portal do Azure (visualização) após a habilitação é mostrada. A opção ' Adicionar atribuições ' é realçada.":::
1. Selecione uma função de *membro* e selecione os usuários e grupos aos quais você deseja conceder acesso ao cluster. Essas atribuições podem ser modificadas a qualquer momento por um administrador de grupo. Quando estiver pronto para continuar, selecione *Avançar*.
    :::image type="content" source="./media/managed-aad/jit-adding-assignment.png" alt-text="A tela de associação adicionar atribuições de portal do Azure é mostrada, com um usuário de exemplo selecionado para ser adicionado como um membro. A opção ' Next ' é realçada.":::
1. Escolha um tipo de atribuição *ativo*, a duração desejada e forneça uma justificativa. Quando você estiver pronto para continuar, selecione *atribuir*. Para obter mais informações sobre tipos de atribuição, consulte [atribuir qualificação para um grupo de acesso privilegiado (versão prévia) em Privileged Identity Management][aad-assignments].
    :::image type="content" source="./media/managed-aad/jit-set-active-assignment-details.png" alt-text="A tela de configuração adicionar atribuições do portal do Azure é mostrada. Um tipo de atribuição ' ativo ' está selecionado e uma justificação de exemplo foi fornecida. A opção ' Assign ' é realçada.":::

Depois que as atribuições forem feitas, verifique se o acesso just-in-time está funcionando acessando o cluster. Por exemplo:

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

Siga as etapas para entrar.

Use o `kubectl get nodes` comando para exibir os nós no cluster:

```azurecli-interactive
kubectl get nodes
```

Observe o requisito de autenticação e siga as etapas para autenticar. Se for bem-sucedido, você deverá ver uma saída semelhante à seguinte:

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-61156405-vmss000000   Ready    agent   6m36s   v1.18.14
aks-nodepool1-61156405-vmss000001   Ready    agent   6m42s   v1.18.14
aks-nodepool1-61156405-vmss000002   Ready    agent   6m33s   v1.18.14
```

### <a name="troubleshooting"></a>Solução de problemas

Se `kubectl get nodes` o retornar um erro semelhante ao seguinte:

```output
Error from server (Forbidden): nodes is forbidden: User "aaaa11111-11aa-aa11-a1a1-111111aaaaa" cannot list resource "nodes" in API group "" at the cluster scope
```

Verifique se o administrador do grupo de segurança concedeu à sua conta uma atribuição *ativa* .

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
[aad-pricing]: https://azure.microsoft.com/pricing/details/active-directory/

<!-- LINKS - Internal -->
[aad-conditional-access]: ../active-directory/conditional-access/overview.md
[azure-rbac-integration]: manage-azure-rbac.md
[aks-concepts-identity]: concepts-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration
[aad-assignments]: ../active-directory/privileged-identity-management/groups-assign-member-owner.md#assign-an-owner-or-member-of-a-group
