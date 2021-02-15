---
title: Erros de registro do provedor de recursos
description: Descreve como resolver erros de registro do provedor de recursos do Azure ao implantar recursos com o Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 02/15/2019
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 143cf03a33739f43a29af94fc2f8a336fb3aef8f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89076660"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Solucione erros de registro do provedor de recursos

Este artigo descreve os erros com que você pode encontrar ao usar um provedor de recursos que não tenha usado anteriormente em sua assinatura.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

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

A mensagem de erro deve fornecer sugestões para os locais com suporte e as versões da API. Você pode alterar o modelo para um dos valores sugeridos. A maioria dos provedores são registrados automaticamente pelo portal do Azure ou pela interface de linha de comando que você está usando, mas nem todos. Se você não usou um provedor de recursos específico antes, precisará registrar esse provedor.

Ou, ao desabilitar o desligamento automático das máquinas virtuais, você pode receber uma mensagem de erro semelhante a:

```
Code: AuthorizationFailed
Message: The client '<identifier>' with object id '<identifier>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope ...
```

## <a name="cause"></a>Causa

Você recebe esses erros por um destes três motivos:

* O provedor de recursos necessário não foi registrado para a sua assinatura
* Versão da API não suportada para o tipo de recurso
* Local não suportado para o tipo de recurso
* Para o desligamento automático de VMs, é necessário que o provedor de recursos Microsoft.DevTestLab esteja registrado.

## <a name="solution-1---powershell"></a>Solução 1: PowerShell

No PowerShell, use **Get-AzResourceProvider** para ver o status do registro.

```powershell
Get-AzResourceProvider -ListAvailable
```

Para registrar um provedor, use **Register-AzResourceProvider** e forneça o nome do provedor de recursos que você deseja registrar.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Cdn
```

Para obter os locais com suporte para um determinado tipo de recurso, use:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Para obter as versões da API com suporte para um determinado tipo de recurso, use:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

## <a name="solution-2---azure-cli"></a>Solução 2: CLI do Azure

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

## <a name="solution-3---azure-portal"></a>Solução 3: Portal do Azure

É possível ver o status de registro e registrar um namespace do provedor de recursos por meio do portal.

1. No portal do Azure, selecione **Todos os serviços**.

   ![Selecionar todos os serviços](./media/error-register-resource-provider/select-all-services.png)

1. Selecione **Assinaturas**.

   ![Selecione assinaturas](./media/error-register-resource-provider/select-subscriptions.png)

1. Na lista de assinaturas, selecione a assinatura que deseja usar para registrar o provedor de recursos.

   ![Selecionar assinatura para registrar o provedor de recursos](./media/error-register-resource-provider/select-subscription-to-register.png)

1. Em sua assinatura, selecione **Provedores de recursos**.

   ![Selecionar provedores de recursos](./media/error-register-resource-provider/select-resource-provider.png)

1. Examine a lista de provedores de recursos e, se necessário, selecione o link **Registrar** para registrar o provedor de recursos do tipo que você está tentando implantar.

   ![Listar provedores de recursos](./media/error-register-resource-provider/list-resource-providers.png)
