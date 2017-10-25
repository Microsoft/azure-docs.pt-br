---
title: "Guia de início rápido do Azure – Criar uma conta de armazenamento usando o PowerShell | Microsoft Docs"
description: Aprenda rapidamente criar uma nova conta de armazenamento com o PowerShell
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/29/2017
ms.author: robinsh
ms.openlocfilehash: c9175cce0cb93e73009fb8d751e54f631603d482
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-storage-account-using-powershell"></a>Criar uma conta de armazenamento usando o PowerShell

O módulo do Azure PowerShell é usado para criar e gerenciar recursos do Azure da linha de comando do PowerShell ou em scripts. Este guia detalha o uso do PowerShell para criar uma conta de armazenamento do Azure. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Este início rápido requer o módulo Azure PowerShell versão 3.6 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa instalar ou atualizar, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon na sua assinatura do Azure com o comando `Login-AzureRmAccount` e siga as instruções na tela.

```powershell
Login-AzureRmAccount
```

Se você não sabe qual localização deseja usar, você pode listar as localizações disponíveis. Depois que a lista for exibida, encontre a que deseja usar. Este exemplo usará **eastus**. Armazene-a em uma variável e use a variável para que você possa alterá-la em um único local.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos do Azure com [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

```powershell
# put resource group in a variable so you can use the same group name going forward
#   without hardcoding it repeatedly
$resourceGroup = "contoso-storage-accounts"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 
```

## <a name="create-a-general-purpose-standard-storage-account"></a>Criar uma conta de armazenamento padrão de uso geral

Há vários tipos de contas de armazenamento, dependendo de como ela será usada e para qual serviço (blobs, arquivos, tabelas ou filas). Esta tabela mostra as possibilidades.

|**Tipo de conta de armazenamento**|**Standard de uso geral**|**Premium de uso geral**|**Armazenamento de blobs, níveis de acesso quente e frio**|
|-----|-----|-----|-----|
|**Serviços compatíveis**| Serviços de Blob, Arquivo, Tabela e Fila | Serviço Blob | Serviço Blob|
|**Tipos de blobs compatíveis**|Blobs de blocos, blobs de páginas e blobs de acréscimo | Blobs de página | Blobs de blocos e blobs de acréscimo|

Use [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount) para criar uma conta de armazenamento padrão de uso geral que você pode usar para os quatro serviços. Atribua o nome de *contosomvcstandard* à conta de armazenamento e configure-a para ter o Armazenamento com Redundância de Local e criptografia de blob habilitadas.

```powershell
New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "contosomvcstandard" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode usar o comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos e todos os recursos relacionados. Nesse caso, ele remove a conta de armazenamento que você criou.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou uma conta de armazenamento padrão de uso geral. Para saber como carregar e baixar blobs com sua conta de armazenamento, prossiga para o Guia de início rápido do Armazenamento de Blobs.
> [!div class="nextstepaction"]
> [Transferir objetos de/para o Armazenamento de Blobs do Azure usando o PowerShell](../blobs/storage-quickstart-blobs-powershell.md)