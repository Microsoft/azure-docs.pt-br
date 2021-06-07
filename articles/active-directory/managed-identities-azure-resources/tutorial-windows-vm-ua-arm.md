---
title: Tutorial`:` Usar uma identidade gerenciada para acessar o Azure Resource Manager – Windows – Azure AD
description: Um tutorial que orienta você durante o processo de uso de uma identidade gerenciada atribuída pelo usuário em uma VM do Windows para acessar o Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43f926f400bf8ddd522bbb5f62509519fa09903d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101096607"
---
# <a name="tutorial-use-a-user-assigned-managed-identity-on-a-windows-vm-to-access-azure-resource-manager"></a>Tutorial: Usar uma identidade gerenciada atribuída pelo usuário em uma VM do Windows para acessar o Azure Resource Manager

Este tutorial explica como criar a identidade atribuída pelo usuário, atribuí-la a uma VM (Máquina Virtual) do Windows e então usar essa identidade para acessar a API do Azure Resource Manager. Identidade do Serviço Gerenciado são gerenciadas automaticamente pelo Azure. Elas permitem que a autenticação para serviços compatíveis com a autenticação do Azure AD, sem a necessidade de inserir as credenciais em seu código. 

Você aprenderá como:

> [!div class="checklist"]
> * Criar uma identidade gerenciada atribuída ao usuário
> * Atribuir sua identidade atribuída pelo usuário à sua VM do Windows
> * Conceder à identidade atribuída pelo usuário acesso a um Grupo de Recursos no Azure Resource Manager 
> * Obtenha um token de acesso usando a identidade atribuída pelo usuário e use-o para chamar o Azure Resource Manager 
> * Leia as propriedades de um grupo de recursos

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

- [Entrar no portal do Azure](https://portal.azure.com)

- [Criar uma máquina virtual do Windows](../../virtual-machines/windows/quick-create-portal.md)

- Para executar as etapas necessárias de criação de recursos e gerenciamento de funções neste tutorial, sua conta precisa das permissões de "Proprietário" no escopo apropriado (sua assinatura ou grupo de recursos). Se precisar de ajuda com a atribuição de função, confira [Atribuir funções do Azure para gerenciar o acesso aos recursos de assinatura do Azure](../../role-based-access-control/role-assignments-portal.md).

- Para executar os scripts de exemplo, você tem duas opções:
    - Use o [Azure Cloud Shell](../../cloud-shell/overview.md), que você pode abrir usando o botão **Experimentar** no canto superior direito dos blocos de código.
    - Executar os scripts localmente com o Azure PowerShell, conforme descrito na próxima seção.

### <a name="configure-azure-powershell-locally"></a>Configurar o Azure PowerShell localmente

Para usar o Azure PowerShell localmente para este artigo (em vez de usar o Cloud Shell), realize as seguintes etapas:

1. Instale [a versão mais recente do Azure PowerShell](/powershell/azure/install-az-ps) se ainda não o fez.

1. Entrar no Azure:

    ```azurepowershell
    Connect-AzAccount
    ```

1. Instale a [versão mais recente do PowerShellGet](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).

    ```azurepowershell
    Install-Module -Name PowerShellGet -AllowPrerelease
    ```

    Talvez seja necessário `Exit` da sessão atual do PowerShell depois de executar esse comando para a próxima etapa.

1. Instale a versão de pré-lançamento do módulo `Az.ManagedServiceIdentity` para executar as operações de identidade gerenciada atribuídas ao usuário neste artigo:

    ```azurepowershell
    Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease
    ```

## <a name="enable"></a>Habilitar

Para um cenário baseado em uma identidade atribuída pelo usuário, você precisa executar as seguintes etapas:

- Criar uma identidade
- Atribuir a identidade recém-criada

### <a name="create-identity"></a>Criar a identidade

Esta seção mostra como criar uma identidade atribuída pelo usuário. Uma identidade atribuída pelo usuário é criada como um recurso autônomo do Azure. Usando o [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/get-azuserassignedidentity), o Azure cria uma identidade em seu locatário do Azure AD que pode ser atribuído a uma ou mais instâncias de serviço do Azure.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1
```

A resposta contém detalhes para a identidade atribuída pelo usuário criada, semelhante ao exemplo a seguir. Anote os valores de `Id` e `ClientId` para a sua identidade atribuída pelo usuário, pois eles serão usados nas próximas etapas:

```azurepowershell
{
Id: /subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1
ResourceGroupName : myResourceGroupVM
Name: ID1
Location: westus
TenantId: 733a8f0e-ec41-4e69-8ad8-971fc4b533f8
PrincipalId: e591178e-b785-43c8-95d2-1397559b2fb9
ClientId: af825a31-b0e0-471f-baea-96de555632f9
ClientSecretUrl: https://control-westus.identity.azure.net/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1/credentials?tid=733a8f0e-ec41-4e69-8ad8-971fc4b533f8&oid=e591178e-b785-43c8-95d2-1397559b2fb9&aid=af825a31-b0e0-471f-baea-96de555632f9
Type: Microsoft.ManagedIdentity/userAssignedIdentities
}
```

### <a name="assign-identity"></a>Atribuir identidade

Esta seção mostra como atribuir a identidade atribuída pelo usuário a uma VM do Windows. Uma identidade atribuída pelo usuário pode ser usada por clientes em vários recursos do Azure. Use os comandos a seguir para atribuir a identidade atribuída pelo usuário a uma única VM. Use a propriedade `Id` retornada na etapa anterior para o valor do parâmetro `-IdentityID`.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-access"></a>Conceder acesso 

Esta seção mostra como conceder à sua identidade atribuída pelo usuário acesso a um Grupo de Recursos no Azure Resource Manager. Identidades gerenciadas para recursos do Azure fornecem identidades que seu código pode usar para solicitar tokens de acesso para autenticação em APIs de recursos que dão suporte à autenticação do Microsoft Azure AD. Neste tutorial, seu código acessará a API do Azure Resource Manager. 

Antes que seu código possa acessar a API, você precisa conceder o acesso de identidade a um recurso no Azure Resource Manager. Nesse caso, o grupo de recursos no qual a VM está contida. Atualize os valores de `<SUBSCRIPTION ID>` conforme apropriado para seu ambiente.

```azurepowershell-interactive
$spID = (Get-AzUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1).principalid
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/"
```

A resposta contém detalhes para a atribuição de função criada, semelhante ao exemplo a seguir:

```azurepowershell
RoleAssignmentId: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM/providers/Microsoft.Authorization/roleAssignments/f9cc753d-265e-4434-ae19-0c3e2ead62ac
Scope: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM
DisplayName: ID1
SignInName:
RoleDefinitionName: Reader
RoleDefinitionId: acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId: e591178e-b785-43c8-95d2-1397559b2fb9
ObjectType: ServicePrincipal
CanDelegate: False
```

## <a name="access-data"></a>Acessar dados

### <a name="get-an-access-token"></a>Obter um token de acesso 

Para o restante do tutorial, você trabalhará a partir da VM que criamos anteriormente.

1. Entre no portal do Azure em [https://portal.azure.com](https://portal.azure.com)

2. No portal, navegue até **Máquinas Virtuais** e vá para a máquina virtual do Windows e em **Visão geral**, clique em **Conectar**.

3. Insira o seu **Nome de usuário** e **Senha** que você adicionou quando criou a VM do Windows.

4. Agora que você criou uma **Conexão de Área de Trabalho Remota** com a máquina virtual, abra o **PowerShell** na sessão remota.

5. Usando o `Invoke-WebRequest` do PowerShell, faça uma solicitação para as identidades gerenciadas locais para o ponto de extremidade de recursos do Azure para obter um token de acesso para o Azure Resource Manager.  O valor `client_id` é o valor que foi retornado quando você criou a identidade gerenciada atribuída pelo usuário.

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=af825a31-b0e0-471f-baea-96de555632f9&resource=https://management.azure.com/' -Method GET -Headers @{Metadata="true"}
    $content = $response.Content | ConvertFrom-Json
    $ArmToken = $content.access_token
    ```

### <a name="read-properties"></a>Ler propriedades

Use o token de acesso recuperado na etapa anterior para acessar o Azure Resource Manager e ler as propriedades do Grupo de Recursos ao qual você concedeu acesso para a identidade atribuída pelo usuário. Substituir `<SUBSCRIPTION ID>` com a id da assinatura do seu ambiente.

```azurepowershell
(Invoke-WebRequest -Uri https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/myResourceGroupVM?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{Authorization ="Bearer $ArmToken"}).content
```
A resposta contém as informações de grupo de recursos específicas, semelhantes ao exemplo a seguir:

```json
{"id":"/subscriptions/<SUBSCRIPTIONID>/resourceGroups/myResourceGroupVM","name":"myResourceGroupVM","location":"eastus","properties":{"provisioningState":"Succeeded"}}
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a criar uma identidade atribuída pelo usuário e a anexá-la a uma Máquina Virtual do Azure para acessar a API do Azure Resource Manager.  Para saber mais sobre o Azure Resource Manager, confira:

> [!div class="nextstepaction"]
>[Azure Resource Manager](../../azure-resource-manager/management/overview.md)
