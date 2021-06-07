---
title: Copiar dados em massa com o PowerShell
description: Esse script do PowerShell mostra como usar o Azure Data Factory para copiar dados em massa de um armazenamento de dados de origem para um armazenamento de dados de destino.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: article
ms.custom: seo-lt-2019
ms.date: 10/31/2017
ms.openlocfilehash: 16cb08d49efcd46e2746c5031793af8b747a2b31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100373565"
---
# <a name="powershell-script---copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Script do PowerShell – copiar várias tabelas em massa usando o Azure Data Factory

Esse script do PowerShell de exemplo copia dados de diversas tabelas no Banco de Dados SQL do Azure para o Azure Synapse Analytics.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

Consulte [tutorial: cópia em massa](../tutorial-bulk-copy.md#prerequisites) para conhecer os pré-requisitos para executar esse exemplo.

## <a name="sample-script"></a>Exemplo de script

> [!IMPORTANT]
> Esse script cria arquivos JSON que definem as entidades do Data Factory (serviço vinculado, conjunto de dados e pipeline) no disco rígido na pasta c:\.

[!code-powershell[main](../../../powershell_scripts/data-factory/bulk-copy-from-sql-databse-to-sql-data-warehouse/bulk-copy-from-sql-database-to-sql-data-warehouse.ps1 "Bulk copy from Azure SQL Database => Azure Synapse Analytics")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Depois de executar o exemplo de script, use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Para remover o data factory do grupo de recursos, execute o seguinte comando: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos: 

| Comando | Observações |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-azdatafactoryv2) | Criar um data factory. |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/set-azdatafactoryv2linkedservice) | Cria um serviço vinculado no data factory. Um serviço vinculado vincula um armazenamento de dados ou uma computação a um data factory. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/set-azdatafactoryv2dataset) | Cria um conjunto de dados no data factory. Um conjunto de dados representa a entrada/saída para uma atividade em um pipeline. | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/set-azdatafactoryv2pipeline) | Cria um pipeline no data factory. Um pipeline contém uma ou mais atividades que executam uma determinada operação. Nesse pipeline, a atividade de cópia copia dados de um local para outro no Armazenamento de Blobs do Azure. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/invoke-azdatafactoryv2pipeline) | Cria uma execução do pipeline. Em outras palavras, executa o pipeline. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-azdatafactoryv2activityrun) | Obtém os detalhes sobre a execução da atividade (execução da atividade) no pipeline. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](/powershell/).

Exemplos adicionais de scripts do Azure Data Factory PowerShell podem ser encontrados nos [Scripts do Azure Data Factory PowerShell](../samples-powershell.md).