---
title: Redefinir as credenciais para um cluster
titleSuffix: Azure Kubernetes Service
description: Saiba como atualizar ou redefinir as credenciais do aplicativo de entidade de serviço ou do AAD para um cluster do serviço de kubernetes do Azure (AKS)
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: 91d40a5a738737098b0941bf3e41e4edd515e6df
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640484"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>Atualizar ou girar as credenciais do serviço kubernetes do Azure (AKS)

Os clusters AKS criados com uma entidade de serviço têm um tempo de expiração de um ano. Conforme você se aproxima da data do término, pode redefinir as credenciais para estender a entidade de serviço por um período adicional. Talvez você também deseje atualizar ou girar as credenciais como parte de uma política de segurança definida. Este artigo fornece detalhes de como atualizar essas credenciais para um cluster do AKS.

Você também pode ter [integrado o cluster AKs com Azure Active Directory][aad-integration]e usá-lo como um provedor de autenticação para o cluster. Nesse caso, você terá mais duas identidades criadas para o cluster, o aplicativo do servidor do AAD e o aplicativo cliente do AAD, também poderá redefinir essas credenciais.

Como alternativa, é possível usar uma identidade gerenciada para permissões em vez de uma entidade de serviço. Identidades gerenciadas são mais fáceis de gerenciar do que as entidades de serviço e não exigem atualizações ou rotações. Para obter mais informações, confira [Usar identidades gerenciadas](use-managed-identity.md).

## <a name="before-you-begin"></a>Antes de começar

Você precisa do CLI do Azure versão 2.0.65 ou posterior instalado e configurado. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][install-azure-cli].

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>Atualizar ou criar uma nova entidade de serviço para o cluster AKS

Quando desejar atualizar as credenciais de um cluster AKS, você poderá optar por:

* Atualize as credenciais para a entidade de serviço existente.
* Crie uma nova entidade de serviço e atualize o cluster para usar essas novas credenciais. 

> [!WARNING]
> Se você optar por criar uma *nova* entidade de serviço, atualizar um cluster AKs grande para usar essas credenciais poderá levar muito tempo para ser concluído.

### <a name="check-the-expiration-date-of-your-service-principal"></a>Verificar a data de validade da entidade de serviço

Para verificar a data de validade de sua entidade de serviço, use o comando [AZ ad SP Credential List][az-ad-sp-credential-list] . O exemplo a seguir obtém a ID da entidade de serviço para o cluster chamado *myAKSCluster* no grupo de recursos *MyResource* Group usando o comando [AZ AKs show][az-aks-show] . A ID da entidade de serviço é definida como uma variável chamada *SP_ID* para uso com o comando [AZ ad SP Credential List][az-ad-sp-credential-list] .

```azurecli
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
az ad sp credential list --id $SP_ID --query "[].endDate" -o tsv
```

### <a name="reset-the-existing-service-principal-credential"></a>Redefinir a credencial da entidade de serviço existente

Para atualizar as credenciais para a entidade de serviço existente, obtenha a ID da entidade de serviço do cluster usando o comando [az aks show][az-aks-show]. O exemplo a seguir obtém a ID do cluster chamado *myAKSCluster* no grupo de recursos *myResourceGroup*. A ID da entidade de serviço é definida como uma variável chamada *SP_ID* para uso em um comando adicional. Esses comandos usam a sintaxe bash.

> [!WARNING]
> Quando você redefinir suas credenciais de cluster em um cluster AKS que usa conjuntos de dimensionamento de máquinas virtuais do Azure, uma [atualização de imagem de nó][node-image-upgrade] é executada para atualizar seus nós com as novas informações de credencial.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

Com um conjunto de variáveis que contém a ID da entidade de serviço, redefina agora as credenciais usando [az ad sp credential reset][az-ad-sp-credential-reset]. O exemplo a seguir permite que a plataforma Azure gere um novo segredo seguro para a entidade de serviço. Esse novo segredo seguro também é armazenado como uma variável.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Agora, continue em para [atualizar o cluster AKs com novas credenciais de entidade de serviço](#update-aks-cluster-with-new-service-principal-credentials). Essa etapa é necessária para que as alterações da entidade de serviço sejam refletidas no cluster AKS.

### <a name="create-a-new-service-principal"></a>Criar uma nova entidade de serviço

Se você optar por atualizar as credenciais da entidade de serviço existente na seção anterior, ignore esta etapa. Continue a [atualizar o cluster AKs com novas credenciais de entidade de serviço](#update-aks-cluster-with-new-service-principal-credentials).

Para criar uma entidade de serviço e, em seguida, atualizar o cluster do AKS para usar essas novas credenciais, use o comando [az ad sp create-for-rbac][az-ad-sp-create]. No exemplo abaixo, o parâmetro `--skip-assignment` impede que outras atribuições padrão sejam atribuídas:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

A saída deverá ser semelhante ao seguinte exemplo: Anote seu próprio `appId` e `password`. Esses valores serão usados na próxima etapa.

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

Agora defina as variáveis para a ID da entidade de serviço e o segredo do cliente usando a saída do próprio comando [az ad sp create-for-rbac][az-ad-sp-create], conforme mostrado no exemplo a seguir. A *SP_ID* é sua *appId* e o *SP_SECRET* é sua *password*:

```console
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

Agora, continue em para [atualizar o cluster AKs com novas credenciais de entidade de serviço](#update-aks-cluster-with-new-service-principal-credentials). Essa etapa é necessária para que as alterações da entidade de serviço sejam refletidas no cluster AKS.

## <a name="update-aks-cluster-with-new-service-principal-credentials"></a>Atualizar o cluster AKS com novas credenciais de entidade de serviço

> [!IMPORTANT]
> Para clusters grandes, a atualização do cluster AKS com uma nova entidade de serviço pode levar muito tempo para ser concluída.

Independentemente se você optou por atualizar as credenciais da entidade de serviço existente ou criar uma entidade de serviço, agora você atualizará o cluster do AKS com suas novas credenciais usando o comando [az aks update-credentials][az-aks-update-credentials]. As variáveis para *--service-principal* e *--client-secret* são usadas:

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

Para clusters de pequeno e médio porte, levará alguns minutos para que as credenciais da entidade de serviço sejam atualizadas no AKS.

## <a name="update-aks-cluster-with-new-aad-application-credentials"></a>Atualizar o cluster AKS com novas credenciais de aplicativo do AAD

Você pode criar novos aplicativos cliente e servidor do AAD seguindo as [etapas de integração do AAD][create-aad-app]. Ou redefina seus aplicativos do AAD existentes seguindo o [mesmo método que para a redefinição da entidade de serviço](#reset-the-existing-service-principal-credential). Depois disso, você só precisa atualizar as credenciais do aplicativo AAD do cluster usando o mesmo comando [AZ AKs Update-Credentials][az-aks-update-credentials] , mas usando as variáveis *--Reset-AAD* .

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-aad \
    --aad-server-app-id <SERVER APPLICATION ID> \
    --aad-server-app-secret <SERVER APPLICATION SECRET> \
    --aad-client-app-id <CLIENT APPLICATION ID>
```


## <a name="next-steps"></a>Próximas etapas

Neste artigo, a entidade de serviço para o cluster AKS e os aplicativos de integração do AAD foram atualizados. Para obter mais informações sobre como gerenciar a identidade para cargas de trabalho em um cluster, confira [Melhores práticas de autenticação e autorização no AKS][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: ./azure-ad-integration-cli.md
[create-aad-app]: ./azure-ad-integration-cli.md#create-azure-ad-server-component
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-list]: /cli/azure/ad/sp/credential#az-ad-sp-credential-list
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
[node-image-upgrade]: ./node-image-upgrade.md
