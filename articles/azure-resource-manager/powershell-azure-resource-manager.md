---
title: "Gerenciar as soluções do Azure com o PowerShell | Microsoft Docs"
description: Use o Azure PowerShell e o Resource Manager para gerenciar seus recursos.
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: b33b7303-3330-4af8-8329-c80ac7e9bc7f
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 10/06/2017
ms.author: tomfitz
ms.openlocfilehash: ae5ccb83a0088cb7c9668f18620b74f9f3f1e9b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-resources-with-azure-powershell-and-resource-manager"></a>Gerenciar recursos com o Azure PowerShell e o Resource Manager

Neste artigo, você aprenderá a gerenciar suas soluções com o Azure PowerShell e o Azure Resource Manager. Se você não estiver familiarizado com o Resource Manager, veja [Visão geral do Resource Manager](resource-group-overview.md). O foco deste artigo são as tarefas de gerenciamento. Você vai:

1. Criar um grupos de recursos
2. Adicionar um recurso ao grupo de recursos
3. Adicionar uma marca ao recurso
4. Recursos de consulta baseados em nomes ou em valores de marca
5. Aplicar e remover um bloqueio no recurso
6. Excluir um grupo de recursos

Este artigo mostra como implantar um modelo do Resource Manager à sua assinatura. Para obter essas informações, veja [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](resource-group-template-deploy.md).

## <a name="get-started-with-azure-powershell"></a>Introdução ao Azure PowerShell

Se você não tiver o Azure PowerShell instalado, veja [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

Se você tiver instalado o Azure PowerShell no passado, mas não o tiver atualizado recentemente, considere a possibilidade de instalar a versão mais recente. Você pode atualizar a versão por meio do mesmo método usado para instalá-lo. Por exemplo, se você tiver usado o Web Platform Installer, inicie-o novamente e procure uma atualização.

Para verificar a versão do módulo de Recursos do Azure, use o seguinte cmdlet:

```powershell
Get-Module -ListAvailable -Name AzureRm.Resources | Select Version
```

Este artigo foi atualizado para a versão 3.3.0. Se você tiver uma versão anterior, sua experiência poderá não corresponder às etapas mostradas aqui. Para obter a documentação sobre os cmdlets nesta versão, veja [AzureRM.Resources módulo](/powershell/module/azurerm.resources).

## <a name="log-in-to-your-azure-account"></a>Fazer logon na sua conta do Azure
Antes de trabalhar em sua solução, você deve fazer logon em sua conta.

Para fazer logon em sua conta do Azure, use o cmdlet **Login-AzureRmAccount**.

```powershell
Login-AzureRmAccount
```

O cmdlet solicita as credenciais de logon para sua conta do Azure. Depois de entrar, ele baixa as configurações da conta para que elas estejam disponíveis para o Azure PowerShell.

O cmdlet retorna informações sobre sua conta e a assinatura a ser usada para as tarefas.

```powershell
Environment           : AzureCloud
Account               : example@contoso.com
TenantId              : {guid}
SubscriptionId        : {guid}
SubscriptionName      : Example Subscription One
CurrentStorageAccount :

```

Se você tiver mais de uma assinatura, você pode alternar para uma assinatura diferente. Primeiro, vamos ver todas as assinaturas para sua conta.

```powershell
Get-AzureRmSubscription
```

Retorna habilitados e desabilitados de assinaturas.

```powershell
SubscriptionName : Example Subscription One
SubscriptionId   : {guid}
TenantId         : {guid}
State            : Enabled

SubscriptionName : Example Subscription Two
SubscriptionId   : {guid}
TenantId         : {guid}
State            : Enabled

SubscriptionName : Example Subscription Three
SubscriptionId   : {guid}
TenantId         : {guid}
State            : Disabled
```

Para alternar para uma assinatura diferente, forneça o nome da assinatura com o **AzureRmContext conjunto** cmdlet.

```powershell
Set-AzureRmContext -SubscriptionName "Example Subscription Two"
```

## <a name="create-a-resource-group"></a>Criar um grupos de recursos

Antes de implantar os recursos em sua assinatura, você deve criar um grupo de recursos que conterá os recursos.

Para criar um grupo de recursos, use o cmdlet **New-AzureRmResourceGroup** . O comando usa o parâmetro **Name** para especificar um nome para o grupo de recursos e o parâmetro **Location** para especificar o local.

```powershell
New-AzureRmResourceGroup -Name TestRG1 -Location "South Central US"
```

A saída está neste formato:

```powershell
ResourceGroupName : TestRG1
Location          : southcentralus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1
```

Se você precisar recuperar o grupo de recursos posteriormente, use o seguinte cmdlet:

```powershell
Get-AzureRmResourceGroup -ResourceGroupName TestRG1
```

Para obter todos os grupos de recursos em sua assinatura, não especifique um nome:

```powershell
Get-AzureRmResourceGroup
```

## <a name="add-resources-to-a-resource-group"></a>Adicionar recursos a um grupo de recursos

Para adicionar um recurso ao grupo de recursos, você pode usar o **novo AzureRmResource** cmdlet ou um cmdlet que é específico para o tipo de recurso que você está criando (como **novo AzureRmStorageAccount**). Talvez seja mais fácil de usar um cmdlet que é específico para um tipo de recurso porque ele inclui parâmetros para as propriedades que são necessárias para o novo recurso. Usar **AzureRmResource novo**, você deve conhecer todas as propriedades para definir sem ser solicitado para eles.

No entanto, a adição de um recurso por meio de cmdlets pode causar uma confusão futura, pois o novo recurso não existe em um modelo do Resource Manager. A Microsoft recomenda definir a infraestrutura para sua solução do Azure em um modelo do Resource Manager. Modelos permitem repetidamente e confiável implantar sua solução. Para este artigo, você cria uma conta de armazenamento com um cmdlet do PowerShell, mas posteriormente você gera um modelo usando seu grupo de recursos.

O cmdlet a seguir cria uma conta de armazenamento. Em vez de usar o nome mostrado no exemplo, forneça um nome exclusivo para a conta de armazenamento. O nome deve ter entre 3 e 24 caracteres de comprimento e usar somente números e letras minúsculas. Se você usar o nome mostrado no exemplo, você recebe um erro porque esse nome já está em uso.

```powershell
New-AzureRmStorageAccount -ResourceGroupName TestRG1 -AccountName mystoragename -Type "Standard_LRS" -Location "South Central US"
```

Se você precisar recuperar esse recurso posteriormente, use o seguinte cmdlet:

```powershell
Get-AzureRmResource -ResourceName mystoragename -ResourceGroupName TestRG1
```

## <a name="add-a-tag"></a>Adicione uma marca

Marcas permitem que você organize seus recursos de acordo com propriedades diferentes. Por exemplo, você pode ter vários recursos em diferentes grupos de recursos que pertencem ao mesmo departamento. Você pode aplicar uma marca de departamento e o valor desses recursos para marcá-los como pertencentes à mesma categoria. Ou, você pode marcar se um recurso é usado em um ambiente de produção ou de teste. Neste artigo, você aplica marcas a apenas um recurso, mas em seu ambiente provavelmente faz mais sentido aplicar marcas a todos eles.

O cmdlet a seguir aplica-se duas marcas à sua conta de armazenamento:

```powershell
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceName mystoragename -ResourceGroupName TestRG1 -ResourceType Microsoft.Storage/storageAccounts
 ```

Marcas são atualizadas como um único objeto. Para adicionar uma marca a um recurso que já inclui marcas, primeiro recupere as marcas existentes. Adicionar nova marca para o objeto que contém as marcas existentes e reaplicar todas as marcas para o recurso.

```powershell
$tags = (Get-AzureRmResource -ResourceName mystoragename -ResourceGroupName TestRG1).Tags
$tags += @{Status="Approved"}
Set-AzureRmResource -Tag $tags -ResourceName mystoragename -ResourceGroupName TestRG1 -ResourceType Microsoft.Storage/storageAccounts
```

## <a name="search-for-resources"></a>Pesquisa de recursos

Use o **AzureRmResource localizar** cmdlet para recuperar os recursos para os critérios de pesquisa diferentes.

* Para obter um recurso por nome, forneça o **ResourceNameContains** parâmetro:

  ```powershell
  Find-AzureRmResource -ResourceNameContains mystoragename
  ```

* Para obter todos os recursos em um grupo de recursos, forneça o **ResourceGroupNameContains** parâmetro:

  ```powershell
  Find-AzureRmResource -ResourceGroupNameContains TestRG1
  ```

* Para obter todos os recursos com um valor e nome de marca, forneça o **TagName** e **TagValue** parâmetros:

  ```powershell
  Find-AzureRmResource -TagName Dept -TagValue IT
  ```

* Para todos os recursos com um tipo de recurso específico, forneça o **ResourceType** parâmetro:

  ```powershell
  Find-AzureRmResource -ResourceType Microsoft.Storage/storageAccounts
  ```

## <a name="get-resource-id"></a>Obter ID de recurso

Muitos comandos usam uma ID de recurso como parâmetro. Para obter a ID de um recurso e armazená-la em uma variável, use:

```powershell
$webappID = (Get-AzureRmResource -ResourceGroupName exampleGroup -ResourceName exampleSite).ResourceId
```

## <a name="lock-a-resource"></a>Um recurso de bloqueio

Quando você precisa certificar-se de um recurso crítico for acidentalmente excluído ou modificado, aplica um bloqueio no recurso. Você pode especificar um **CanNotDelete** ou **ReadOnly**.

Para criar ou excluir bloqueios de gerenciamento, você deve ter acesso às ações `Microsoft.Authorization/*` ou `Microsoft.Authorization/locks/*`. Das funções internas, somente Proprietário e Administrador do Acesso de Usuário recebem essas ações.

Para aplicar um bloqueio, use o seguinte cmdlet:

```powershell
New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockStorage -ResourceName mystoragename -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName TestRG1
```

O recurso bloqueado no exemplo anterior não pode ser excluído até que o bloqueio seja removido. Para remover um bloqueio, use:

```powershell
Remove-AzureRmResourceLock -LockName LockStorage -ResourceName mystoragename -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName TestRG1
```

Para saber mais sobre bloqueios de configuração, confira [Bloquear recursos com o Azure Resource Manager](resource-group-lock-resources.md).

## <a name="remove-resources-or-resource-group"></a>Remover recursos ou grupo de recursos
Você pode remover um recurso ou grupo de recursos. Ao remover um grupo de recursos, você também removerá todos os recursos dentro daquele grupo de recursos.

* Para excluir um recurso do grupo de recursos, use o cmdlet **Remove-AzureRmResource** . Esse cmdlet exclui o recurso, mas não exclui o grupo de recursos.

  ```powershell
  Remove-AzureRmResource -ResourceName mystoragename -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName TestRG1
  ```

* Para excluir um grupo de recursos e todos os seus recursos, use o cmdlet **Remove-AzureRmResourceGroup**.

  ```powershell
  Remove-AzureRmResourceGroup -Name TestRG1
  ```

Para ambos os cmdlets, você deve confirmar que você deseja remover o recurso ou grupo de recursos. Se a operação com êxito exclui o recurso ou grupo de recursos, ele retorna **True**.

## <a name="run-resource-manager-scripts-with-azure-automation"></a>Executar scripts do Resource Manager com a automação do Azure

Este artigo mostra como executar operações básicas em seus recursos com o Azure PowerShell. Para cenários mais avançados de gerenciamento, normalmente você deseja criar um script e reutilizar esse script conforme necessário ou em uma agenda. [A automação do Azure](../automation/automation-intro.md) fornece uma maneira de automatizar frequentemente usados scripts que gerenciam as soluções do Azure.

Os tópicos a seguir mostram como usar a automação do Azure, o Resource Manager e o PowerShell para a realização de tarefas de gerenciamento:

- Para obter informações sobre como criar um runbook, consulte [meu primeiro runbook PowerShell](../automation/automation-first-runbook-textual-powershell.md).
- Para obter informações sobre como trabalhar com galerias de scripts, consulte [galerias de Runbook e módulo de automação do Azure](../automation/automation-runbook-gallery.md).
- Para runbooks que inicie e pare as máquinas virtuais, consulte [cenário de automação do Azure: formato JSON usando marcas para criar uma agenda para a VM do Azure inicialização e desligamento](../automation/automation-scenario-start-stop-vm-wjson-tags.md).
- Para runbooks que iniciar e parar máquinas virtuais fora do horário comercial, consulte [VMs iniciar/parar durante a solução de fora do horário comercial em automação](../automation/automation-solution-vm-management.md).

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre a criação de modelos do Gerenciador de Recursos, consulte [Criando Modelos do Azure Resource Manager](resource-group-authoring-templates.md).
* Para saber mais sobre como implantar modelos, consulte [Implantar um aplicativo com o Modelo do Azure Resource Manager](resource-group-template-deploy.md).
* Você pode mover os recursos existentes para um novo grupo de recursos. Para obter exemplos, confira [Mover recursos para um novo grupo de recursos ou uma nova assinatura](resource-group-move-resources.md).
* Para obter orientação sobre como as empresas podem usar o Resource Manager para gerenciar assinaturas de forma eficaz, consulte [Azure enterprise scaffold – controle de assinatura prescritivas](resource-manager-subscription-governance.md).

