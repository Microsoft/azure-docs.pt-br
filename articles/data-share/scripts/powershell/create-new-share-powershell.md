---
title: 'Script do PowerShell: criar um novo compartilhamento de dados do Azure'
description: Esse script do PowerShell cria um novo compartilhamento de dados em uma conta de compartilhamento de dados existente.
author: joannapea
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 36c1bdfef2afe4c34796a804784317a5e7fe12ff
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92221342"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>Usar o PowerShell para criar um compartilhamento de dados no Azure

Esse script do PowerShell cria um novo compartilhamento de dados em uma conta de compartilhamento de dados existente.

## <a name="sample-script"></a>Exemplo de script

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

# Create a new Azure Data Share
New-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos: 

| Comando | Observações |
|---|---|
| [New-AzDataShare](/powershell/module/az.datashare/new-azdatashare) | Cria um compartilhamento de dados. |
|||

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](/powershell/).

Exemplos adicionais de script do PowerShell do compartilhamento de dados do Azure podem ser encontrados nos [exemplos do PowerShell do compartilhamento de dados do Azure](../../samples-powershell.md).
