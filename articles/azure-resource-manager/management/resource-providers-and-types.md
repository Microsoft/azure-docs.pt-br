---
title: Provedores de recursos e tipos de recursos
description: Descreve os provedores de recursos que dão suporte ao Azure Resource Manager. Ele descreve seus esquemas, as versões de API disponíveis e as regiões que podem hospedar os recursos.
ms.topic: conceptual
ms.date: 12/04/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6d114fdfae12dd9ee96a23e4dafc3847c6429d0c
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745087"
---
# <a name="azure-resource-providers-and-types"></a>Provedores e tipos de recursos do Azure

Ao implantar recursos, com frequência você precisa recuperar informações sobre os provedores e tipos de recursos. Por exemplo, se você quer armazenar chaves e segredos, trabalhe com o provedor de recursos Microsoft.KeyVault . Este provedor de recursos oferece um tipo de recurso denominado cofres para criar o cofre da chave.

O nome de um tipo de recurso está no formato: **{provedor-de-recursos}/{tipo-de-recurso}**. O tipo de recurso de um cofre de chaves é **Microsoft.KeyVault/vaults**.

Neste artigo, você aprenderá como:

* Exibir todos os provedores de recursos no Azure
* Verificar o status do registro de um provedor de recursos
* Registrar um provedor de recursos
* Exibir os tipos de recurso para um provedor de recursos
* Exibir localizações válidas para um tipo de recurso
* Exibir versões de API válidas para um tipo de recurso

Você pode executar essas etapas por meio do portal do Azure, Azure PowerShell ou CLI do Azure.

Para obter uma lista que mapeia os provedores de recursos para os serviços do Azure, confira [Provedores de recursos para os serviços do Azure](azure-services-resource-providers.md).

## <a name="register-resource-provider"></a>Registrar provedor de recursos

Antes de usar um provedor de recursos, sua assinatura do Azure deve ser registrada para o provedor de recursos. O Registro configura sua assinatura para trabalhar com o provedor de recursos. Alguns provedores de recursos são registrados por padrão. Outros provedores de recursos são registrados automaticamente quando você executar determinadas ações. Por exemplo, quando você cria um recurso por meio do portal, o provedor de recursos normalmente é registrado para você. Para outros cenários, talvez seja necessário registrar manualmente um provedor de recursos. Para obter uma lista de provedores de recursos registrados por padrão, consulte [provedores de recursos para serviços do Azure](azure-services-resource-providers.md).

Este artigo mostra como verificar o status de registro de um provedor de recursos e registrá-lo conforme necessário. Você deve ter permissão para realizar a `/register/action` operação para o provedor de recursos. A permissão está incluída nas funções colaborador e proprietário.

> [!IMPORTANT]
> Somente registre um provedor de recursos quando estiver pronto para usá-lo. A etapa de registro permite que você mantenha os privilégios mínimos em sua assinatura. Um usuário mal-intencionado não pode usar provedores de recursos que não estão registrados.

O código do aplicativo não deve bloquear a criação de recursos para um provedor de recursos que esteja no estado de **registro** . Quando você registra o provedor de recursos, a operação é feita individualmente para cada região com suporte. Para criar recursos em uma região, o registro precisa ser concluído apenas nessa região. Por não bloquear o provedor de recursos no estado de registro, seu aplicativo pode continuar muito antes de esperar que todas as regiões sejam concluídas.

Não é possível cancelar o registro de um provedor de recursos quando você ainda tem tipos de recursos desse provedor de recursos em sua assinatura.

## <a name="azure-portal"></a>Portal do Azure

### <a name="register-resource-provider"></a>Registrar provedor de recursos

Para ver todos os provedores de recursos e o status do registro para a sua assinatura:

1. Entre no [portal do Azure](https://portal.azure.com).
1. No menu portal do Azure, procure **assinaturas**. Selecione-as entre as opções disponíveis.

   :::image type="content" source="./media/resource-providers-and-types/search-subscriptions.png" alt-text="Pesquisar assinaturas":::

1. Selecione a assinatura que você deseja exibir.

   :::image type="content" source="./media/resource-providers-and-types/select-subscription.png" alt-text="selecionar assinaturas":::

1. No menu à esquerda, em **Configurações**, selecione **Provedores de recursos**.

   :::image type="content" source="./media/resource-providers-and-types/select-resource-providers.png" alt-text="selecionar provedores de recursos":::

6. Localize o provedor de recursos que você deseja registrar e selecione **registrar**. Para manter os privilégios mínimos em sua assinatura, registre apenas os provedores de recursos que você está pronto para usar.

   :::image type="content" source="./media/resource-providers-and-types/register-resource-provider.png" alt-text="registrar provedores de recursos":::

### <a name="view-resource-provider"></a>Exibir provedor de recursos

Para obter informações para um provedor de recursos específico:

1. Entre no [portal do Azure](https://portal.azure.com).
2. No menu do portal do Azure, selecione **Todos os serviços**.
3. Na caixa **Todos os serviços**, digite **gerenciador de recursos** e, em seguida, selecione **Gerenciador de Recursos**.

    ![selecionar Todos os serviços](./media/resource-providers-and-types/select-resource-explorer.png)

4. Expanda **provedores** selecionando a seta para a direita.

    ![Selecionar provedores](./media/resource-providers-and-types/select-providers.png)

5. Expanda o provedor de recursos e o tipo de recurso que você deseja exibir.

    ![Selecionar tipo de recurso](./media/resource-providers-and-types/select-resource-type.png)

6. O Gerenciador de Recursos tem suporte em todas as regiões, mas os recursos que você implanta talvez não tenham suporte em todas as regiões. Além disso, pode haver limitações na sua assinatura que impeçam o uso de algumas regiões que dão suporte ao recurso. O Resource Explorer mostra localizações válidas para o tipo de recurso.

    ![Mostrar localizações](./media/resource-providers-and-types/show-locations.png)

7. A versão disponível da API corresponde a uma versão das operações da API REST lançadas pelo provedor de recursos. Conforme um provedor de recursos habilita novos recursos, ele lança uma nova versão da API REST. O Resource Explorer mostra versões de API válidas para o tipo de recurso.

    ![Mostrar versões de API](./media/resource-providers-and-types/show-api-versions.png)

## <a name="azure-powershell"></a>Azure PowerShell

Para ver todos os provedores de recursos no Azure e o status do registro para a sua assinatura, use:

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

Que retorna resultados semelhantes a:

```output
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Para ver todos os provedores de recursos registrados para sua assinatura, use:

```azurepowershell-interactive
 Get-AzResourceProvider -ListAvailable | Where-Object RegistrationState -eq "Registered" | Select-Object ProviderNamespace, RegistrationState | Sort-Object ProviderNamespace
```

Para manter os privilégios mínimos em sua assinatura, registre apenas os provedores de recursos que você está pronto para usar. Para registrar um provedor de recursos, use:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Que retorna resultados semelhantes a:

```output
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

Para obter informações para um provedor de recursos específico, use:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Que retorna resultados semelhantes a:

```output
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

Para ver os tipos de recurso para um provedor de recursos, use:

```azurepowershell-interactive
(Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

Que retorna:

```output
batchAccounts
operations
locations
locations/quotas
```

A versão disponível da API corresponde a uma versão das operações da API REST lançadas pelo provedor de recursos. Conforme um provedor de recursos habilita novos recursos, ele lança uma nova versão da API REST.

Para obter versões de API disponíveis para um tipo de recurso, use:

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

Que retorna:

```output
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

O Gerenciador de Recursos tem suporte em todas as regiões, mas os recursos que você implanta talvez não tenham suporte em todas as regiões. Além disso, pode haver limitações na sua assinatura que impeçam o uso de algumas regiões que dão suporte ao recurso.

Para obter as localizações com suporte para um tipo de recurso, use.

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

Que retorna:

```output
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>CLI do Azure

Para ver todos os provedores de recursos no Azure e o status do registro para a sua assinatura, use:

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Que retorna resultados semelhantes a:

```output
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Para ver todos os provedores de recursos registrados para sua assinatura, use:

```azurecli-interactive
az provider list --query "sort_by([?registrationState=='Registered'].{Provider:namespace, Status:registrationState}, &Provider)" --out table
```

Para manter os privilégios mínimos em sua assinatura, registre apenas os provedores de recursos que você está pronto para usar. Para registrar um provedor de recursos, use:

```azurecli-interactive
az provider register --namespace Microsoft.Batch
```

Que retorna uma mensagem de que o registro está em andamento.

Para obter informações para um provedor de recursos específico, use:

```azurecli-interactive
az provider show --namespace Microsoft.Batch
```

Que retorna resultados semelhantes a:

```output
{
    "id": "/subscriptions/####-####/providers/Microsoft.Batch",
    "namespace": "Microsoft.Batch",
    "registrationsState": "Registering",
    "resourceTypes:" [
        ...
    ]
}
```

Para ver os tipos de recurso para um provedor de recursos, use:

```azurecli-interactive
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

Que retorna:

```output
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

A versão disponível da API corresponde a uma versão das operações da API REST lançadas pelo provedor de recursos. Conforme um provedor de recursos habilita novos recursos, ele lança uma nova versão da API REST.

Para obter versões de API disponíveis para um tipo de recurso, use:

```azurecli-interactive
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

Que retorna:

```output
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

O Gerenciador de Recursos tem suporte em todas as regiões, mas os recursos que você implanta talvez não tenham suporte em todas as regiões. Além disso, pode haver limitações na sua assinatura que impeçam o uso de algumas regiões que dão suporte ao recurso.

Para obter as localizações com suporte para um tipo de recurso, use.

```azurecli-interactive
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

Que retorna:

```output
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como criar modelos do Resource Manager, confira [criação de modelos de Azure Resource Manager](../templates/template-syntax.md). 
* Para exibir os esquemas de modelo de provedor de recursos, confira [Referência de modelo](/azure/templates/).
* Para obter uma lista que mapeia os provedores de recursos para os serviços do Azure, confira [Provedores de recursos para os serviços do Azure](azure-services-resource-providers.md).
* Para exibir as operações para um provedor de recursos, consulte [API REST do Azure](/rest/api/).
