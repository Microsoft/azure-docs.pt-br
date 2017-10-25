---
title: Provedores de recursos e tipos de recursos do Azure | Microsoft Docs
description: "Descreve os provedores de recursos que oferecem suporte ao Gerenciador de Recursos, aos respectivos esquemas e versões de API disponíveis, bem como às regiões que podem hospedar os recursos."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 3c7a6fe4-371a-40da-9ebe-b574f583305b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: tomfitz
ms.openlocfilehash: 6a9128f45d4199404019cee594842d59c7f1aaf3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="resource-providers-and-types"></a>Provedores e tipos de recursos

Ao implantar recursos, com frequência você precisa recuperar informações sobre os provedores e tipos de recursos. Neste artigo, você aprende a:

* Exibir todos os provedores de recursos no Azure
* Verificar o status do registro de um provedor de recursos
* Registrar um provedor de recursos
* Exibir os tipos de recurso para um provedor de recursos
* Exibir localizações válidas para um tipo de recurso
* Exibir versões de API válidas para um tipo de recurso

Você pode executar essas etapas por meio do portal, do PowerShell ou da CLI do Azure.

## <a name="powershell"></a>PowerShell

Para ver todos os provedores de recursos no Azure e o status do registro para a sua assinatura, use:

```powershell
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

Que retorna resultados semelhantes a:

```powershell
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

O registro de um provedor de recursos configura sua assinatura para trabalhar com o provedor de recursos. O escopo de registro é sempre a assinatura. Por padrão, muitos provedores de recursos são automaticamente registrados. No entanto, talvez seja necessário registrar manualmente alguns provedores de recursos. Para registrar um provedor de recursos, você deve ter permissão para executar a operação do `/register/action` para o provedor de recursos. Esta operação está incluída nas funções de Colaborador e de Proprietário.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
```

Que retorna resultados semelhantes a:

```powershell
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

Você não poderá cancelar o registro de um provedor de recursos enquanto ainda tiver tipos de recursos desse provedor de recursos em sua assinatura.

Para obter informações para um provedor de recursos específico, use:

```powershell
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
```

Que retorna resultados semelhantes a:

```powershell
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

Para ver os tipos de recurso para um provedor de recursos, use:

```powershell
(Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

Que retorna:

```powershell
batchAccounts
operations
locations
locations/quotas
```

A versão disponível da API corresponde a uma versão das operações da API REST lançadas pelo provedor de recursos. Conforme um provedor de recursos habilita novos recursos, ele lança uma nova versão da API REST. 

Para obter versões de API disponíveis para um tipo de recurso, use:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

Que retorna:

```powershell
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

O Gerenciador de Recursos tem suporte em todas as regiões, mas os recursos que você implanta talvez não tenham suporte em todas as regiões. Além disso, pode haver limitações em sua assinatura que impedem o uso de algumas regiões que oferecem suporte aos recursos. 

Para obter as localizações com suporte para um tipo de recurso, use.

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

Que retorna:

```powershell
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>CLI do Azure
Para ver todos os provedores de recursos no Azure e o status do registro para a sua assinatura, use:

```azurecli
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Que retorna resultados semelhantes a:

```azurecli
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

O registro de um provedor de recursos configura sua assinatura para trabalhar com o provedor de recursos. O escopo de registro é sempre a assinatura. Por padrão, muitos provedores de recursos são automaticamente registrados. No entanto, talvez seja necessário registrar manualmente alguns provedores de recursos. Para registrar um provedor de recursos, você deve ter permissão para executar a operação do `/register/action` para o provedor de recursos. Esta operação está incluída nas funções de Colaborador e de Proprietário.

```azurecli
az provider register --namespace Microsoft.Batch
```

Que retorna uma mensagem de que o registro está em andamento.

Você não poderá cancelar o registro de um provedor de recursos enquanto ainda tiver tipos de recursos desse provedor de recursos em sua assinatura.

Para obter informações para um provedor de recursos específico, use:

```azurecli
az provider show --namespace Microsoft.Batch
```

Que retorna resultados semelhantes a:

```azurecli
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

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

Que retorna:

```azurecli
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

A versão disponível da API corresponde a uma versão das operações da API REST lançadas pelo provedor de recursos. Conforme um provedor de recursos habilita novos recursos, ele lança uma nova versão da API REST. 

Para obter versões de API disponíveis para um tipo de recurso, use:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

Que retorna:

```azurecli
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

O Gerenciador de Recursos tem suporte em todas as regiões, mas os recursos que você implanta talvez não tenham suporte em todas as regiões. Além disso, pode haver limitações em sua assinatura que impedem o uso de algumas regiões que oferecem suporte aos recursos. 

Para obter as localizações com suporte para um tipo de recurso, use.

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

Que retorna:

```azurecli
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="portal"></a>Portal

Para ver todos os provedores de recursos no Azure e o status do registro para a sua assinatura, selecione **Assinaturas**.

![selecionar assinaturas](./media/resource-manager-supported-services/select-subscriptions.png)

Escolha a assinatura para exibir.

![especificar a assinatura](./media/resource-manager-supported-services/subscription.png)

Selecione **Provedores de recursos** e exiba a lista de provedores de recursos disponíveis.

![mostrar provedores de recursos](./media/resource-manager-supported-services/show-resource-providers.png)

O registro de um provedor de recursos configura sua assinatura para trabalhar com o provedor de recursos. O escopo de registro é sempre a assinatura. Por padrão, muitos provedores de recursos são automaticamente registrados. No entanto, talvez seja necessário registrar manualmente alguns provedores de recursos. Para registrar um provedor de recursos, você deve ter permissão para executar a operação do `/register/action` para o provedor de recursos. Esta operação está incluída nas funções de Colaborador e de Proprietário. Para registrar um provedor de recursos, selecione **Registrar**.

![registrar provedor de recursos](./media/resource-manager-supported-services/register-provider.png)

Você não poderá cancelar o registro de um provedor de recursos enquanto ainda tiver tipos de recursos desse provedor de recursos em sua assinatura.

Para obter informações para um provedor de recursos específico, selecione **Mais serviços**.

![selecionar mais serviços](./media/resource-manager-supported-services/more-services.png)

Pesquise por **Resource Explorer** e selecione-o nas opções disponíveis.

![selecionar resource explorer](./media/resource-manager-supported-services/select-resource-explorer.png)

Selecione **Provedores**.

![Selecionar provedores](./media/resource-manager-supported-services/select-providers.png)

Selecione o provedor de recursos e o tipo de recurso que você deseja exibir.

![Selecionar tipo de recurso](./media/resource-manager-supported-services/select-resource-type.png)

O Gerenciador de Recursos tem suporte em todas as regiões, mas os recursos que você implanta talvez não tenham suporte em todas as regiões. Além disso, pode haver limitações em sua assinatura que impedem o uso de algumas regiões que oferecem suporte aos recursos. O Resource Explorer mostra localizações válidas para o tipo de recurso.

![Mostrar localizações](./media/resource-manager-supported-services/show-locations.png)

A versão disponível da API corresponde a uma versão das operações da API REST lançadas pelo provedor de recursos. Conforme um provedor de recursos habilita novos recursos, ele lança uma nova versão da API REST. O Resource Explorer mostra versões de API válidas para o tipo de recurso.

![Mostrar versões de API](./media/resource-manager-supported-services/show-api-versions.png)

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre a criação de modelos do Gerenciador de Recursos, confira [Criando modelos do Gerenciador de Recursos do Azure](resource-group-authoring-templates.md).
* Para saber mais sobre como implantar recursos, confira [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](resource-group-template-deploy.md).
* Para exibir as operações para um provedor de recursos, consulte [API REST do Azure](/rest/api/).

