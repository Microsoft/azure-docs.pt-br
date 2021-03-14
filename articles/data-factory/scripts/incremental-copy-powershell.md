---
title: Carregar dados incrementalmente usando o PowerShell
description: Este script do PowerShell mostra como usar Azure Data Factory para copiar dados incrementalmente de um banco de dado SQL do Azure para um armazenamento de BLOBs do Azure.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: article
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 736403696ec340e547547458cb62e243e6e660b9
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389834"
---
# <a name="powershell-script---incrementally-load-data-by-using-azure-data-factory"></a>Script do PowerShell – Carregar dados incrementalmente usando o Azure Data Factory

Este script do PowerShell de exemplo carrega somente os registros novos ou atualizados de um armazenamento de dados de origem para um armazenamento de dados do coletor após a cópia completa inicial dos dados da origem para o coletor.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

Consulte [tutorial: cópia incremental](../tutorial-incremental-copy-powershell.md#prerequisites) para conhecer os pré-requisitos para executar esse exemplo. 

## <a name="sample-script"></a>Exemplo de script

> [!IMPORTANT]
> Esse script cria arquivos JSON que definem as entidades do Data Factory (serviço vinculado, conjunto de dados e pipeline) no disco rígido na pasta c:\.

:::code language="powershell" source="~/powershell_scripts/data-factory/incremental-copy-from-azure-sql-to-blob/incremental-copy-from-azure-sql-to-blob.ps1":::

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
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Criar um data factory. |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Cria um serviço vinculado no data factory. Um serviço vinculado vincula um armazenamento de dados ou uma computação a um data factory. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Cria um conjunto de dados no data factory. Um conjunto de dados representa a entrada/saída para uma atividade em um pipeline. | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Cria um pipeline no data factory. Um pipeline contém uma ou mais atividades que executam uma determinada operação. Nesse pipeline, a atividade de cópia copia dados de um local para outro no Armazenamento de Blobs do Azure. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | Cria uma execução do pipeline. Em outras palavras, executa o pipeline. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Obtém os detalhes sobre a execução da atividade (execução da atividade) no pipeline. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](/powershell/).

Exemplos adicionais de scripts do Azure Data Factory PowerShell podem ser encontrados nos [Scripts do Azure Data Factory PowerShell](../samples-powershell.md).