---
title: Transformar dados usando a atividade de procedimento armazenado no Azure Data Factory | Microsoft Docs
description: Explica como usar a atividade de procedimento armazenado do SQL Server para invocar um procedimento armazenado em um Banco de Dados SQL do Azure/Data Warehouse do Azure de um pipeline do Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/27/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 806654b7586895b62b014a49b8b3a00fb18f008f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60764400"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Transformar dados usando a atividade de procedimento armazenado do SQL Server no Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-stored-proc-activity.md)
> * [Versão atual](transform-data-using-stored-procedure.md)

Use atividades de transformação de dados em um [pipeline](concepts-pipelines-activities.md) do Data Factory para transformar e processar dados brutos em previsões e informações. A Atividade de Procedimento Armazenado é uma das atividades de transformação que o Data Factory dá suporte. Este artigo baseia-se no artigo sobre [dados de transformação](transform-data.md), que apresenta uma visão geral da transformação de dados e das atividades de transformação com suporte no Data Factory.

> [!NOTE]
> Se você é novo no Azure Data Factory, leia a [Introdução ao Azure Data Factory](introduction.md) e siga o tutorial: [Transformar dados](tutorial-transform-data-spark-powershell.md) antes de ler este artigo. 

Use a Atividade de Procedimento Armazenado para invocar um procedimento armazenado em um dos seguintes armazenamentos de dados em sua empresa ou em uma VM (máquina virtual) do Azure: 

- Banco de dados SQL do Azure
- Azure SQL Data Warehouse
- Banco de Dados do SQL Server.  Se estiver usando o SQL Server, instale o Integration Runtime (auto-hospedado) no mesmo computador que hospeda o banco de dados ou em um computador separado com acesso ao banco de dados. O Integration Runtime (auto-hospedado) é um componente que conecta fontes de dados locais ou em uma VM do Azure aos serviços de nuvem de maneira segura e gerenciada. Consulte o artigo [Self-hosted integration runtime](create-self-hosted-integration-runtime.md) (Integration Runtime auto-hospedado) para obter detalhes.

> [!IMPORTANT]
> Ao copiar dados para o Banco de Dados SQL do Azure ou o SQL Server, configure o **SqlSink** na atividade de cópia para invocar um procedimento armazenado usando a propriedade **sqlWriterStoredProcedureName**. Para saber mais sobre a propriedade, consulte os seguintes artigos sobre conector: [Banco de Dados SQL do Azure](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md). Não há suporte para invocar um procedimento armazenado ao copiar dados em um SQL Data Warehouse do Azure usando uma atividade de cópia. Mas, você pode usar a atividade de procedimento armazenado para invocar um procedimento armazenado em um SQL Data Warehouse. 
>
> Ao copiar dados do Banco de Dados SQL do Azure, do SQL Server ou do SQL Data Warehouse do Azure, configure **SqlSource** na atividade de cópia para invocar um procedimento armazenado para ler dados do banco de dados de origem usando a propriedade **sqlReaderStoredProcedureName**. Para saber mais, consulte os seguintes artigos sobre conector: [Banco de Dados SQL do Azure](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md) e [SQL Data Warehouse do Azure](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>Detalhes da sintaxe
Aqui está o formato JSON para definir uma Atividade de Procedimento Armazenado:

```json
{
    "name": "Stored Procedure Activity",
    "description":"Description",
    "type": "SqlServerStoredProcedure",
    "linkedServiceName": {
        "referenceName": "AzureSqlLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "storedProcedureName": "usp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

A seguinte tabela descreve essas propriedades JSON:

| Propriedade                  | DESCRIÇÃO                              | Obrigatório |
| ------------------------- | ---------------------------------------- | -------- |
| nome                      | Nome da atividade                     | Sim      |
| descrição               | Texto que descreve qual a utilidade da atividade | Não       |
| tipo                      | Para a atividade de procedimento armazenado, o tipo de atividade é **SqlServerStoredProcedure** | Sim      |
| linkedServiceName         | Referência ao **Banco de Dados SQL do Azure** ou ao **SQL Data Warehouse do Azure** ou ao **SQL Server** registrado como um serviço vinculado no Data Factory. Para saber mais sobre esse serviço vinculado, consulte o artigo [Compute linked services](compute-linked-services.md) (Serviços de computação vinculados). | Sim      |
| storedProcedureName       | Especifique o nome do procedimento armazenado para invocar. | Sim      |
| storedProcedureParameters | Especifique os valores para parâmetros de procedimento armazenado. Use `"param1": { "value": "param1Value","type":"param1Type" }` para passar os valores de parâmetro e seu tipo com suporte da fonte de dados. Se você precisar passar null para um parâmetro, use `"param1": { "value": null }` (tudo em letras minúsculas). | Não       |

## <a name="error-info"></a>Informações de Erro

Quando um procedimento armazenado falha e retorna detalhes do erro, não é possível capturar as informações de erro diretamente na saída da atividade. No entanto, o Data Factory bombeia todos os seus eventos de execução de atividade para o Azure Monitor. Entre os eventos que o Data Factory bombeia para o Azure Monitor, ele envia detalhes do erro para lá. Você pode, por exemplo, configurar alertas de e-mail desses eventos. Para obter mais informações, consulte [Alertar e monitorar os data factories usando o Azure Monitor](monitor-using-azure-monitor.md).

## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes artigos que explicam como transformar dados de outras maneiras: 

* [U-SQL Activity](transform-data-using-data-lake-analytics.md) (Atividade do U-SQL)
* [Atividade de Hive](transform-data-using-hadoop-hive.md)
* [Atividade Pig](transform-data-using-hadoop-pig.md)
* [Atividade MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Atividade de Transmissão do Hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade do Spark](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Bach Execution Activity](transform-data-using-machine-learning.md) (Atividade de execução em lotes do Machine Learning)
* [Stored procedure activity](transform-data-using-stored-procedure.md) (Atividade de procedimento armazenado)
