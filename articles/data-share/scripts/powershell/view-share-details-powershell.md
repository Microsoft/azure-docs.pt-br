---
title: 'Script do PowerShell: listar compartilhamentos existentes no compartilhamento de dados do Azure'
description: Este script do PowerShell lista e exibe detalhes de compartilhamentos.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.openlocfilehash: 54b4b6cedc2dc09e4a9d5c4b7b937e80750e17c6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92220815"
---
# <a name="use-powershell-to-view-the-details-of-a-sent-data-share"></a>Usar o PowerShell para exibir os detalhes de um compartilhamento de dados enviados

Esse script do PowerShell lista os compartilhamentos de dados de uma conta existente e obtém os detalhes de um compartilhamento específico.


## <a name="sample-script"></a>Exemplo de script

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

#Lists all data shares within an account
Get-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName

#Gets details of a specific data share
Get-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos: 

| Comando | Observações |
|---|---|
| [Get-AzDataShare](/powershell/module/az.datashare/get-azdatashare) | Obtém e lista de compartilhamentos em uma conta. |
|||

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](/powershell/).

Exemplos adicionais de script do PowerShell do compartilhamento de dados do Azure podem ser encontrados nos [exemplos do PowerShell do compartilhamento de dados do Azure](../../samples-powershell.md).
