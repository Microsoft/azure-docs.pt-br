---
title: Gerenciar o RBAC do Azure no kubernetes do Azure
titleSuffix: Azure Kubernetes Service
description: Saiba como usar o Azure RBAC para autorização de kubernetes com o AKS (serviço kubernetes do Azure).
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 9ce8bc71139b52d690893734435e6bfee090062d
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102184365"
---
# <a name="use-azure-rbac-for-kubernetes-authorization-preview"></a>Usar o Azure RBAC para Autorização do Kubernetes (versão prévia)

Hoje, você já pode aproveitar a [autenticação integrada entre o Azure Active Directory (Azure AD) e o AKs](managed-aad.md). Quando habilitado, essa integração permite que os clientes usem usuários, grupos ou entidades de serviço do Azure AD como entidades no RBAC kubernetes, veja mais [aqui](azure-ad-rbac.md).
Esse recurso libera você de ter que gerenciar identidades e credenciais de usuário separadamente para kubernetes. No entanto, você ainda precisa configurar e gerenciar RBAC do Azure e kubernetes RBAC separadamente. Para obter mais detalhes sobre autenticação e autorização com RBAC em AKS, consulte [aqui](concepts-identity.md).

Este documento aborda uma nova abordagem que permite o gerenciamento unificado e o controle de acesso em recursos do Azure, AKS e recursos do kubernetes.

## <a name="before-you-begin"></a>Antes de começar

A capacidade de gerenciar o RBAC para recursos de kubernetes do Azure oferece a você a opção de gerenciar o RBAC para os recursos de cluster usando o Azure ou mecanismos kubernetes nativos. Quando habilitada, as entidades de segurança do Azure AD serão validadas exclusivamente pelo RBAC do Azure, enquanto os usuários e contas de serviço kubernetes regulares são validados exclusivamente pelo RBAC kubernetes. Para obter mais detalhes sobre autenticação e autorização com RBAC em AKS, consulte [aqui](concepts-identity.md#azure-rbac-for-kubernetes-authorization-preview).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="prerequisites"></a>Pré-requisitos 
- Verifique se você tem o CLI do Azure versão 2.9.0 ou posterior
- Verifique se o `EnableAzureRBACPreview` sinalizador de recurso está habilitado.
- Verifique se você tem a `aks-preview` [extensão da CLI][az-extension-add] v 0.4.55 ou superior instalada
- Verifique se você instalou o [kubectl v 1.18.3 +][az-aks-install-cli].

#### <a name="register-enableazurerbacpreview-preview-feature"></a>Registrar `EnableAzureRBACPreview` recurso de visualização

Para criar um cluster AKS que usa o RBAC do Azure para autorização do kubernetes, você deve habilitar o `EnableAzureRBACPreview` sinalizador de recurso em sua assinatura.

Registre o `EnableAzureRBACPreview` sinalizador de recurso usando o comando [AZ Feature Register][az-feature-register] , conforme mostrado no exemplo a seguir:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureRBACPreview"
```

 Você pode verificar o status de registro usando o comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureRBACPreview')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualize o registro do provedor de recursos *Microsoft. ContainerService* usando o comando [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

#### <a name="install-aks-preview-cli-extension"></a>Instalar a extensão da CLI aks-preview

Para criar um cluster AKS que usa o RBAC do Azure, você precisa da extensão da CLI do *AKs-Preview* versão 0.4.55 ou superior. Instale a extensão de CLI do Azure *de AKs-Preview* usando o comando [AZ Extension Add][az-extension-add] ou instale todas as atualizações disponíveis usando o comando [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Limitações

- Requer a [integração do Azure ad gerenciada](managed-aad.md).
- Não é possível integrar o RBAC do Azure para autorização kubernetes em clusters existentes durante a visualização, mas você poderá usar a disponibilidade geral (GA).
- Use [kubectl v 1.18.3 +][az-aks-install-cli].
- Se você tiver CRDs e estiver fazendo definições de função personalizadas, a única maneira de abordar o CRDs hoje é fornecer `Microsoft.ContainerService/managedClusters/*/read` . O AKS está trabalhando para fornecer permissões mais granulares para CRDs. Para os objetos restantes, você pode usar os grupos de API específicos, por exemplo: `Microsoft.ContainerService/apps/deployments/read` .
- As novas atribuições de função podem levar até 5 min para serem propagadas e atualizadas pelo servidor de autorização.
- Requer que o locatário do Azure AD configurado para autenticação seja o mesmo que o locatário para a assinatura que contém o cluster AKS. 

## <a name="create-a-new-cluster-using-azure-rbac-and-managed-azure-ad-integration"></a>Criar um novo cluster usando o RBAC do Azure e a integração do Azure AD gerenciada

Crie um cluster AKS usando os comandos da CLI a seguir.

Criar um grupo de recursos do Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Crie o cluster AKS com a integração do Azure AD gerenciada e o RBAC do Azure para autorização do kubernetes.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad --enable-azure-rbac
```

Uma criação bem-sucedida de um cluster com a integração do Azure AD e o RBAC do Azure para autorização kubernetes tem a seguinte seção no corpo da resposta:

```json
"AADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "enableAzureRbac": true,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "****-****-****-****-****"
  }
```

## <a name="create-role-assignments-for-users-to-access-cluster"></a>Criar atribuições de função para os usuários acessarem o cluster

O AKS fornece as quatro funções internas a seguir:


| Função                                | Descrição  |
|-------------------------------------|--------------|
| Leitor de RBAC do serviço kubernetes do Azure  | Permite acesso somente leitura para ver a maioria dos objetos em um namespace. Ele não permite a exibição de funções ou associações de função. Essa função não permite a exibição `Secrets` , uma vez que a leitura do conteúdo de segredos permite o acesso a credenciais de uma conta no namespace, o que permitiria o acesso à API como qualquer uma das contas no namespace (uma forma de elevação de privilégio)  |
| Gravador RBAC do serviço kubernetes do Azure | Permite acesso de leitura/gravação à maioria dos objetos em um namespace. Essa função não permite exibir ou modificar funções ou associações de função. No entanto, essa função permite acessar `Secrets` e executar pods como qualquer uma das contas no namespace, para que possa ser usada para obter os níveis de acesso de API de qualquer conta no namespace. |
| Administrador de RBAC do serviço kubernetes do Azure  | Permite o acesso de administrador, destinado a ser concedido em um namespace. Permite acesso de leitura/gravação para a maioria dos recursos em um namespace (ou escopo de cluster), incluindo a capacidade de criar funções e associações de função no namespace. Essa função não permite acesso de gravação à cota de recursos ou ao próprio namespace. |
| Administrador de cluster do RBAC do serviço kubernetes do Azure  | Permite o acesso de superusuário para executar qualquer ação em qualquer recurso. Ele fornece controle total sobre cada recurso no cluster e em todos os namespaces. |


Atribuições de funções com escopo definido para **todo o cluster AKs** podem ser feitas na folha controle de acesso (iam) do recurso de cluster em portal do Azure ou usando comandos CLI do Azure, como mostrado abaixo:

```bash
# Get your AKS Resource ID
AKS_ID=$(az aks show -g MyResourceGroup -n MyManagedCluster --query id -o tsv)
```

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Admin" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

onde `<AAD-ENTITY-ID>` pode ser um nome de usuário (por exemplo, user@contoso.com ) ou até o ClientID de uma entidade de serviço.

Você também pode criar atribuições de função com escopo para um **namespace** específico dentro do cluster:

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID/namespaces/<namespace-name>
```

Atualmente, as atribuições de função com escopo para namespaces precisam ser configuradas por meio de CLI do Azure.


### <a name="create-custom-roles-definitions"></a>Criar definições de funções personalizadas

Opcionalmente, você pode optar por criar sua própria definição de função e, em seguida, atribuir como acima.

Abaixo está um exemplo de uma definição de função que permite que um usuário somente Leia implantações e nada mais. Você pode verificar a lista completa de possíveis ações [aqui](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice).


Copie o JSON abaixo em um arquivo chamado `deploy-view.json` .

```json
{
    "Name": "AKS Deployment Viewer",
    "Description": "Lets you view all deployments in cluster/namespace.",
    "Actions": [],
    "NotActions": [],
    "DataActions": [
        "Microsoft.ContainerService/managedClusters/apps/deployments/read"
    ],
    "NotDataActions": [],
    "assignableScopes": [
        "/subscriptions/<YOUR SUBSCRIPTION ID>"
    ]
}
```

Substitua `<YOUR SUBSCRIPTION ID>` pela ID da sua assinatura, que você pode obter executando:

```azurecli-interactive
az account show --query id -o tsv
```


Agora, podemos criar a definição de função executando o comando abaixo da pasta em que você salvou `deploy-view.json` :

```azurecli-interactive
az role definition create --role-definition @deploy-view.json 
```

Agora que você tem a definição de função, é possível atribuí-la a um usuário ou outra identidade executando:

```azurecli-interactive
az role assignment create --role "AKS Deployment Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubectl"></a>Usar o RBAC do Azure para autorização de kubernetes com `kubectl`

> [!NOTE]
> Verifique se você tem os kubectl mais recentes executando o comando abaixo:
>
> ```azurecli-interactive
> az aks install-cli
> ```
> Talvez seja necessário executá-lo com `sudo` privilégios. 

Agora que você atribuiu a função e as permissões desejadas. Você pode começar a chamar a API kubernetes, por exemplo, de `kubectl` .

Para essa finalidade, vamos primeiro obter o kubeconfig do cluster usando o comando abaixo:

```azurecli-interactive
az aks get-credentials -g MyResourceGroup -n MyManagedCluster
```

> [!IMPORTANT]
> Você precisará da função interna de [usuário do cluster do serviço kubernetes do Azure](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) para executar a etapa acima.

Agora, você pode usar kubectl para, por exemplo, listar os nós no cluster. Na primeira vez que você executá-lo, precisará entrar e os comandos subsequentes usarão o respectivo token de acesso.

```azurecli-interactive
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubelogin"></a>Usar o RBAC do Azure para autorização de kubernetes com `kubelogin`

Para desbloquear cenários adicionais como logons não interativos, `kubectl` versões mais antigas ou aproveitar o SSO em vários clusters sem a necessidade de entrar no novo cluster, contanto que seu token ainda seja válido, AKs criou um plug-in exec chamado [`kubelogin`](https://github.com/Azure/kubelogin) .

Você pode usá-lo executando:

```bash
export KUBECONFIG=/path/to/kubeconfig
kubelogin convert-kubeconfig
``` 

Na primeira vez, você precisará entrar de forma interativa com kubectl regulares, mas depois você não precisará mais, mesmo para os novos clusters do Azure AD (desde que o token ainda seja válido).

```bash
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="clean-up"></a>Limpar

### <a name="clean-role-assignment"></a>Limpar atribuição de função

```azurecli-interactive
az role assignment list --scope $AKS_ID --query [].id -o tsv
```
Copie a ID ou as IDs de todas as atribuições que você fez e depois.

```azurecli-interactive
az role assignment delete --ids <LIST OF ASSIGNMENT IDS>
```

### <a name="clean-up-role-definition"></a>Limpar definição de função

```azurecli-interactive
az role definition delete -n "AKS Deployment Viewer"
```

### <a name="delete-cluster-and-resource-group"></a>Excluir grupo de recursos e cluster

```azurecli-interactive
az group delete -n MyResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

- Leia mais sobre autenticação AKS, autorização, RBAC kubernetes e RBAC do Azure [aqui](concepts-identity.md).
- Leia mais sobre o RBAC do Azure [aqui](../role-based-access-control/overview.md).
- Leia mais sobre todas as ações que você pode usar para definir de maneira granular funções personalizadas do Azure para autorização kubernetes [aqui](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice).


<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-aks-install-cli]: /cli/azure/aks#az-aks-install-cli
[az-provider-register]: /cli/azure/provider#az-provider-register
