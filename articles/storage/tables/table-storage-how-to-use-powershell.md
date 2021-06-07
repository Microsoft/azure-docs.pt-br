---
title: Executar operações de armazenamento de Tabelas do Azure com o PowerShell | Microsoft Docs
description: Saiba como executar tarefas comuns, como criar, consultar, excluir dados da conta de armazenamento de tabelas do Azure usando o PowerShell.
author: roygara
ms.service: storage
ms.topic: article
ms.date: 04/05/2019
ms.author: rogarana
ms.subservice: tables
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9a7502d48095fe18b983c1971d5145f51e766c95
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93306898"
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>Executar operações de armazenamento de Tabelas do Azure com o Azure PowerShell 
[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

O armazenamento de Tabelas do Azure é um armazenamento de dados NoSQL que pode ser usado para armazenar e consultar grandes conjuntos de dados estruturados e não relacionais. Os principais componentes do serviço são tabelas, entidades e propriedades. Uma tabela é uma coleção de entidades. Uma entidade é um conjunto de propriedades. Cada entidade pode ter até 252 propriedades, que são todas pares de nome-valor. Este artigo pressupõe que você já esteja familiarizado com os conceitos do Serviço de Armazenamento de Tabelas do Azure. Para obter informações detalhadas, confira [Understanding the Table Service Data Model](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) (Noções básicas do modelo de dados do serviço Tabela) e [Introdução ao Armazenamento de Tabelas do Azure usando o .NET](../../cosmos-db/tutorial-develop-table-dotnet.md).

Este artigo de instruções abrange operações comuns do armazenamento de Tabelas do Azure. Você aprenderá como: 

> [!div class="checklist"]
> * Criar uma tabela
> * Recuperar uma tabela
> * Adicionar entidades de tabela
> * Consultar uma tabela
> * Excluir entidades de tabela
> * Excluir uma tabela

Este artigo de instruções mostra como criar uma nova conta de armazenamento do Azure em um novo grupo de recursos, de modo que você possa removê-la facilmente quando terminar. Se preferir usar uma conta de armazenamento existente, faça isso.

Os exemplos exigem os módulos AZ PowerShell `Az.Storage (1.1.0 or greater)` e `Az.Resources (1.2.0 or greater)` . Em uma janela do PowerShell, execute `Get-Module -ListAvailable Az*` para localizar a versão. Se nada for exibido ou você precisar fazer atualização, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Usar esse recurso do Azure do PowerShell exige que você tenha o módulo `Az` instalado. A versão atual do `AzTable` não é compatível com o módulo AzureRM mais antigo.
> Siga as [instruções de instalação mais recentes para instalar o módulo AZ](/powershell/azure/install-az-ps) , se necessário.

Após a instalação ou atualização do Azure PowerShell, você deve instalar o módulo **AzTable**, que tem os comandos para gerenciar as entidades. Para instalar esse módulo, execute o PowerShell como administrador e use o comando **Install-Module**.

> [!IMPORTANT]
> Para fins de compatibilidade de nome de módulo, ainda estamos publicando esse mesmo módulo com o nome antigo `AzureRmStorageTables` em Galeria do PowerShell. Este documento fará referência apenas ao novo nome.

```powershell
Install-Module AzTable
```

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre na sua assinatura do Azure com o comando `Add-AzAccount` e siga as instruções na tela.

```powershell
Add-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Recuperar a lista de locais

Se você não sabe qual localização deseja usar, você pode listar as localizações disponíveis. Depois que a lista for exibida, encontre o que deseja usar. Esses exemplos usam **eastus**. Armazene esse valor na variável **location** para uso futuro.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos com o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Armazene o nome do grupo de recursos em uma variável para uso futuro. Neste exemplo, um grupo de recursos chamado *pshtablesrg* é criado na região *eastus*.

```powershell
$resourceGroup = "pshtablesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Criar Conta de Armazenamento

Crie uma conta de armazenamento padrão para uso geral com o LRS (armazenamento com redundância local) usando [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Certifique-se de especificar um nome de conta de armazenamento exclusivo. Em seguida, obtenha o contexto que representa a conta de armazenamento. Ao atuar em uma conta de armazenamento, você pode fazer referência ao contexto em vez de fornecer suas credenciais repetidamente.

```powershell
$storageAccountName = "pshtablestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-new-table"></a>Criar uma nova tabela

Para criar uma tabela, use o cmdlet [New-AzStorageTable](/powershell/module/az.storage/New-AzStorageTable) . Neste exemplo, a tabela é chamada `pshtesttable`.

```powershell
$tableName = "pshtesttable"
New-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>Recuperar uma lista de tabelas na conta de armazenamento

Recupere uma lista de tabelas na conta de armazenamento usando [Get-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
Get-AzStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>Recuperar uma referência a uma tabela específica

Para executar operações em uma tabela, você precisa de uma referência à tabela específica. Obtenha uma referência usando [Get-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
$storageTable = Get-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="reference-cloudtable-property-of-a-specific-table"></a>Propriedade Cloudtable de referência de uma tabela específica

> [!IMPORTANT]
> O uso de Cloudtable é obrigatório ao trabalhar com o módulo do PowerShell do **AzTable** . Chame o comando **Get-AzStorageTable** para obter a referência a esse objeto. Esse comando também criará a tabela se ela ainda não existir.

Para executar operações em uma tabela usando **AzTable**, você precisa de uma referência à propriedade cloudtable de uma tabela específica.

```powershell
$cloudTable = (Get-AzStorageTable –Name $tableName –Context $ctx).CloudTable
```

[!INCLUDE [storage-table-entities-powershell-include](../../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Excluir uma tabela

Para excluir uma tabela, use [Remove-AzStorageTable](/powershell/module/az.storage/Remove-AzStorageTable). Esse cmdlet remove a tabela, incluindo todos os seus dados.

```powershell
Remove-AzStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se você criou um novo grupo de recursos e uma conta de armazenamento no início destas instruções, remova todos os ativos criados neste exercício removendo o grupo de recursos. Esse comando exclui todos os recursos contidos no grupo, bem como o próprio grupo de recursos.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo de instruções, você aprendeu sobre operações comuns do armazenamento de Tabelas do Azure com o PowerShell, incluindo como: 

> [!div class="checklist"]
> * Criar uma tabela
> * Recuperar uma tabela
> * Adicionar entidades de tabela
> * Consultar uma tabela
> * Excluir entidades de tabela
> * Excluir uma tabela

Para obter mais informações, consulte os seguintes artigos

* [Cmdlets do PowerShell do Armazenamento](/powershell/module/az.storage#storage)

* [Trabalhando com tabelas do Azure do PowerShell-AzureRmStorageTable/AzTable PS Module v 2.0](https://paulomarquesc.github.io/working-with-azure-storage-tables-from-powershell)

* [O Gerenciador de Armazenamento do Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md) é um aplicativo autônomo e gratuito da Microsoft que possibilita o trabalho visual com os dados do Armazenamento do Azure no Windows, MacOS e Linux.