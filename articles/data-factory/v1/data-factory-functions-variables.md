---
title: "Funções do Data Factory e Variáveis do Sistema | Microsoft Docs"
description: "Fornece uma lista de funções do Azure Data Factory e variáveis do sistema"
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
services: data-factory
ms.assetid: b6b3c2ae-b0e8-4e28-90d8-daf20421660d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: 6d7e9be25badbd83ee613518ba768d98334a5f4d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-data-factory---functions-and-system-variables"></a>Azure Data Factory - Funções e Variáveis do Sistema
Este artigo fornece informações sobre funções e variáveis com suporte no Azure Data Factory.

## <a name="data-factory-system-variables"></a>Variáveis do sistema do Data Factory
| Nome de variável | Descrição | Escopo do objeto | Escopo JSON e casos de uso |
| --- | --- | --- | --- |
| WindowStart |Início do intervalo de tempo para a janela da execução de atividade atual |atividade |<ol><li>Especifica consultas de seleção de dados. Veja os artigos sobre o conector referenciados no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md) .</li> |
| WindowEnd |Fim do intervalo de tempo para a janela da execução de atividade atual |atividade |igual a WindowStart. |
| SliceStart |Início do intervalo de tempo para a fatia de dados sendo gerada |atividade<br/>conjunto de dados |<ol><li>Especifique caminhos de pasta dinâmicos e nomes de arquivos enquanto estiver trabalhando com o [Blob do Azure](data-factory-azure-blob-connector.md) e [Conjuntos de dados do sistema de arquivos](data-factory-onprem-file-system-connector.md).</li><li>Especificar dependências de entrada com funções de data factory na coleção de entradas da atividade.</li></ol> |
| SliceEnd |Fim do intervalo de tempo da fatia de dados atual. |atividade<br/>dataset |o mesmo que SliceStart. |

> [!NOTE]
> Atualmente, o data factory exige que o agendamento especificado na atividade corresponda exatamente ao agendamento especificado na disponibilidade do conjunto de dados de saída. Portanto, WindowStart, WindowEnd, SliceStart e SliceEnd sempre são mapeados para o mesmo período de tempo e uma única fatia de saída.
> 

### <a name="example-for-using-a-system-variable"></a>Exemplo para usar uma variável de sistema
No exemplo a seguir, o ano, o mês, o dia e a hora de **SliceStart** são extraídos em variáveis separadas que são usadas pelas propriedades **folderPath** e **fileName**.

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

## <a name="data-factory-functions"></a>Funções do Data Factory
Você pode usar funções no Data Factory junto com as variáveis do sistema para as seguintes finalidades:

1. Especificando consultas de seleção de dados (veja os artigos sobre o conector referenciados no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md) .
   
   A sintaxe para invocar uma função do Data Factory é: **$$<function>** para consultas de seleção de dados e outras propriedades na atividade e nos conjuntos de dados.  
2. Especificar dependências de entrada com funções de data factory na coleção de entradas da atividade.
   
    $$ não é necessário para especificar expressões de dependência de entrada.     

No exemplo a seguir, a propriedade **sqlReaderQuery** em um arquivo JSON é atribuída a um valor retornado pela função `Text.Format`. Este exemplo também usa uma variável de sistema chamada **WindowStart**, que representa a hora de início da janela de execução de atividade.

```json
{
    "Type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
}
```

Confira o tópico [Cadeias de caracteres de formato de data e hora personalizado](https://msdn.microsoft.com/library/8kb3ddd4.aspx) que descreve as diferentes opções de formatação que você pode usar (por exemplo: aa versus aaaa). 

### <a name="functions"></a>Funções
As tabelas a seguir listam todas as funções no Azure Data Factory:

| Categoria | Função | Parâmetros | Descrição |
| --- | --- | --- | --- |
| Hora |AddHours(X,Y) |X: DateTime  <br/><br/>Y: int |Adiciona Y horas até o momento X determinado. <br/><br/>Exemplo: `9/5/2013 12:00:00 PM + 2 hours = 9/5/2013 2:00:00 PM` |
| Hora |AddMinutes(X,Y) |X: DateTime  <br/><br/>Y: int |Adiciona Y minutos a X.<br/><br/>Exemplo: `9/15/2013 12: 00:00 PM + 15 minutes = 9/15/2013 12: 15:00 PM` |
| Hora |StartOfHour(X) |X: DateTime  |Obtém a hora de início para a hora representada pelo componente de hora do X. <br/><br/>Exemplo: `StartOfHour of 9/15/2013 05: 10:23 PM is 9/15/2013 05: 00:00 PM` |
| Data |AddDays(X,Y) |X: DateTime <br/><br/>Y: int |Adiciona Y dias a X. <br/><br/>Exemplo: 15/9/2013 12:00:00 + 2 dias = 17/9/2013 12:00:00.<br/><br/>Você pode subtrair dias também, especificando Y como um número negativo.<br/><br/>Exemplo: `9/15/2013 12:00:00 PM - 2 days = 9/13/2013 12:00:00 PM`. |
| Data |AddMonths(X,Y) |X: DateTime <br/><br/>Y: int |Adiciona Y meses a X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 month = 10/15/2013 12:00:00 PM`.<br/><br/>Você pode subtrair meses também, especificando Y como um número negativo.<br/><br/>Exemplo: `9/15/2013 12:00:00 PM - 1 month = 8/15/2013 12:00:00 PM`.|
| Data |AddQuarters(X,Y) |X: DateTime  <br/><br/>Y: int |Adiciona Y * 3 meses a X.<br/><br/>Exemplo: `9/15/2013 12:00:00 PM + 1 quarter = 12/15/2013 12:00:00 PM` |
| Data |AddWeeks(X,Y) |X: DateTime <br/><br/>Y: int |Adiciona Y * 7 dias a X<br/><br/>Exemplo: 15/9/2013 12:00:00 + 1 semana = 22/9/2013 12:00:00<br/><br/>Você pode subtrair semanas também, especificando Y como um número negativo.<br/><br/>Exemplo: `9/15/2013 12:00:00 PM - 1 week = 9/7/2013 12:00:00 PM`. |
| Data |AddYears(X,Y) |X: DateTime <br/><br/>Y: int |Adiciona Y dias a X<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 year = 9/15/2014 12:00:00 PM`<br/><br/>Você pode subtrair anos também, especificando Y como um número negativo.<br/><br/>Exemplo: `9/15/2013 12:00:00 PM - 1 year = 9/15/2012 12:00:00 PM`. |
| Data |Day(X) |X: DateTime  |Obtém o componente de dia de X.<br/><br/>Exemplo: `Day of 9/15/2013 12:00:00 PM is 9`. |
| Data |DayOfWeek(X) |X: DateTime  |Obtém o componente de dia da semana de X.<br/><br/>Exemplo: `DayOfWeek of 9/15/2013 12:00:00 PM is Sunday`. |
| Data |DayOfYear(X) |X: DateTime  |Obtém o dia do ano que representa o componente de ano de X.<br/><br/>Exemplos:<br/>`12/1/2015: day 335 of 2015`<br/>`12/31/2015: day 365 of 2015`<br/>`12/31/2016: day 366 of 2016 (Leap Year)` |
| Data |DaysInMonth(X) |X: DateTime  |Obtém os dias do mês representados pelo componente de mês do parâmetro X.<br/><br/>Exemplo: `DaysInMonth of 9/15/2013 are 30 since there are 30 days in the September month`. |
| Data |EndOfDay(X) |X: DateTime  |Obtém a data e hora que representam o fim do dia (componente do dia) do X.<br/><br/>Exemplo: `EndOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 11:59:59 PM`. |
| Data |EndOfMonth(X) |X: DateTime  |Obtém o fim do mês representado pelo componente de mês do parâmetro X. <br/><br/>Exemplo: `EndOfMonth of 9/15/2013 05:10:23 PM is 9/30/2013 11:59:59 PM` (data/hora que representa o fim do mês de setembro) |
| Data |StartOfDay(X) |X: DateTime  |Obtém o início do dia representado pelo componente dia do parâmetro X.<br/><br/>Exemplo: `StartOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 12:00:00 AM`. |
| DateTime |From(X) |X: Cadeia de caracteres |Analise a cadeia de caracteres X para um valor de data e hora. |
| DateTime |Ticks(X) |X: DateTime  |Obtém os tiques de propriedade do parâmetro X. Um tique é igual a 100 nanossegundos. O valor dessa propriedade representa o número de tiques que se passaram desde 0h, meia-noite de 1º de janeiro de 0001. |
| Texto |Format(X) |X: variável de cadeia de caracteres |Formata o texto (use a combinação `\\'` para escapar o caractere `'`).|

> [!IMPORTANT]
> Ao usar uma função dentro de outra função, você não precisa usar o prefixo **$$** para a função interna. Por exemplo: $$Text.Format('PartitionKey eq \\'my_pkey_filter_value\\' e RowKey ge \\'{0: yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(SliceStart, -6)). Neste exemplo, observe que o prefixo **$$** não é usado para a função **Time.AddHours**. 

#### <a name="example"></a>Exemplo
No exemplo a seguir, os parâmetros de entrada e saída da atividade do Hive são determinados com o uso da função `Text.Format` e da variável do sistema SliceStart. 

```json  
{
    "name": "HiveActivitySamplePipeline",
        "properties": {
    "activities": [
            {
            "name": "HiveActivitySample",
            "type": "HDInsightHive",
            "inputs": [
                    {
                    "name": "HiveSampleIn"
                    }
            ],
            "outputs": [
                    {
                    "name": "HiveSampleOut"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
                    "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                }
            }
            }
    ]
    }
}
```

### <a name="example-2"></a>Exemplo 2

No exemplo a seguir, o parâmetro DateTime da Atividade de Procedimento Armazenado é determinado com o uso de Text. Format e da variável SliceStart. 

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_sample",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
            "start": "2016-08-02T00:00:00Z",
            "end": "2016-08-02T05:00:00Z",
        "isPaused": false
    }
}
```

### <a name="example-3"></a>Exemplo 3
Para ler dados do dia anterior em vez do dia representado pelo SliceStart, use a função AddDays conforme mostrado no exemplo a seguir: 

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-01-01T08:00:00",
        "end": "2017-01-01T11:00:00",
        "description": "hive activity",
        "activities": [
            {
                "name": "SampleHiveActivity",
                "inputs": [
                    {
                        "name": "MyAzureBlobInput",
                        "startTime": "Date.AddDays(SliceStart, -1)",
                        "endTime": "Date.AddDays(SliceEnd, -1)"
                    }
                ],
                "outputs": [
                    {
                        "name": "MyAzureBlobOutput"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adftutorial\\hivequery.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
                        "Month": "$$Text.Format('{0:MM}',WindowStart)",
                        "Day": "$$Text.Format('{0:dd}',WindowStart)"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 2,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

Confira o tópico [Cadeias de caracteres de formato de data e hora personalizado](https://msdn.microsoft.com/library/8kb3ddd4.aspx) que descreve as diferentes opções de formatação que você pode usar (por exemplo: aa versus aaaa). 

