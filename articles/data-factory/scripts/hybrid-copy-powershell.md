---
title: Copiar dados do local para o Azure usando o PowerShell
description: Este script do PowerShell copia dados de um banco de SQL Server para outro armazenamento de BLOBs do Azure.
ms.service: data-factory
ms.topic: article
ms.author: jingwang
author: linda33wj
ms.custom: seo-lt-2019
ms.date: 10/31/2017
ms.openlocfilehash: 34c5497a8b059260dfe60e8015e62c0f1511f021
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100373378"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-from-sql-server-to-azure"></a>Usar o PowerShell para criar um pipeline de data factory para copiar dados do SQL Server para o Azure

Este script do PowerShell de exemplo cria um pipeline no Azure Data Factory que copia dados de um banco de SQL Server para um armazenamento de BLOBs do Azure.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- **SQL Server**. Você usa um banco de dados SQL Server como um armazenamento de data de **origem** neste exemplo.
- **Conta de Armazenamento do Azure**. O Armazenamento de Blobs do Azure é usado como um armazenamento de dados de **destino/coletor** neste exemplo. Se você não tiver uma conta de armazenamento do Azure, consulte o artigo [criar uma conta de armazenamento](../../storage/common/storage-account-create.md) para obter as etapas para criar uma.
- **Tempo de execução de integração auto-hospedado**. Baixe o arquivo MSI do [Centro de Download](https://www.microsoft.com/download/details.aspx?id=39717) e execute-o para instalar um runtime de integração auto-hospedada no seu computador.  

### <a name="create-sample-database-in-sql-server"></a>Criar o banco de dados de exemplo no SQL Server
1. No banco de dados SQL Server, crie uma tabela chamada **EMP** usando o seguinte script SQL:

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. Insira alguns dados de exemplo na tabela:

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```

## <a name="sample-script"></a>Exemplo de script

> [!IMPORTANT]
> Esse script cria arquivos JSON que definem as entidades do Data Factory (serviço vinculado, conjunto de dados e pipeline) no disco rígido na pasta c:\.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-onprem-sql-server-to-azure-blob/copy-from-onprem-sql-server-to-azure-blob.ps1 "Copy from SQL Server -> Azure Blob Storage")]


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
| [New-AzDataFactoryV2LinkedServiceEncryptCredential](/powershell/module/az.datafactory/new-Azdatafactoryv2linkedserviceencryptedcredential) | Criptografa as credenciais em um serviço vinculado e gera uma nova definição de serviço vinculado com a credencial criptografada.
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Cria um serviço vinculado no data factory. Um serviço vinculado vincula um armazenamento de dados ou uma computação a um data factory. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Cria um conjunto de dados no data factory. Um conjunto de dados representa a entrada/saída para uma atividade em um pipeline. |
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Cria um pipeline no data factory. Um pipeline contém uma ou mais atividades que executam uma determinada operação. Nesse pipeline, a atividade de cópia copia dados de um local para outro no Armazenamento de Blobs do Azure. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | Cria uma execução do pipeline. Em outras palavras, executa o pipeline. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Obtém os detalhes sobre a execução da atividade (execução da atividade) no pipeline.
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](/powershell/).

Exemplos adicionais de scripts do Azure Data Factory PowerShell podem ser encontrados nos [Exemplos do Azure Data Factory PowerShell](../samples-powershell.md).