---
title: Erros de registro do provedor de recursos do Azure | Microsoft Docs
description: Descreve como solucionar erros de registro do provedor de recursos do Azure.
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: d6a99917e732a3439a31cafa5608348694014054
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Solucione erros de registro do provedor de recursos

Este artigo descreve os erros com que você pode se deparar ao usar um provedor de recursos que não tenha usado anteriormente em sua assinatura.

## <a name="symptom"></a>Sintoma

Ao implantar recursos, você pode receber o seguinte código de erro e a mensagem:

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

Ou você poderá receber uma mensagem semelhante que indica:

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

## <a name="cause"></a>Causa

Você recebe esses erros por um dos três motivos:

1. O provedor de recursos não foi registrado para sua assinatura
1. Versão da API não suportada para o tipo de recurso
1. Local não suportado para o tipo de recurso

## <a name="solution"></a>Solução

A mensagem de erro deve fornecer sugestões para os locais com suporte e as versões da API. Você pode alterar o modelo para um dos valores sugeridos. A maioria dos provedores são registrados automaticamente pelo portal do Azure ou pela interface de linha de comando que você está usando, mas nem todos. Se você não usou um provedor de recursos específico antes, precisará registrá-lo. Você pode descobrir mais sobre provedores de recursos por meio do PowerShell ou CLI do Azure.

### <a name="solution-1"></a>Solução 1

No PowerShell, use **Get-AzureRmResourceProvider** para ver o status do registro.

```powershell
Get-AzureRmResourceProvider -ListAvailable
```

Para registrar um provedor, use **Register-AzureRmResourceProvider** e forneça o nome do provedor de recursos que você deseja registrar.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn
```

Para obter os locais com suporte para um determinado tipo de recurso, use:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Para obter as versões da API com suporte para um determinado tipo de recurso, use:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

### <a name="solution-2"></a>Solução 2

**CLI do Azure**

Para ver se o provedor está registrado, use o comando `az provider list` .

```azurecli-interactive
az provider list
```

Para registrar um provedor de recursos, use o comando `az provider register` e especifique o *namespace* para registrar.

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

Para ver os locais com suporte e as versões da API para um tipo de recurso, use:

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

### <a name="solution-3"></a>Solução 3

É possível ver o status de registro e registrar um namespace do provedor de recursos por meio do portal.

1. Em sua assinatura, selecione **Provedores de recursos**.

   ![selecionar provedores de recursos](./media/resource-manager-register-provider-errors/select-resource-provider.png)

1. Examine a lista de provedores de recursos e, se necessário, selecione o link **Registrar** para registrar o provedor de recursos do tipo que você está tentando implantar.

   ![listar provedores de recursos](./media/resource-manager-register-provider-errors/list-resource-providers.png)
