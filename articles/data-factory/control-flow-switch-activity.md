---
title: Alternar atividade no Azure Data Factory
description: A atividade switch permite controlar o fluxo de processamento com base em uma condição.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2019
ms.openlocfilehash: 08d93bd3d49dc345b35a942d4d5db4eef937d3de
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786134"
---
# <a name="switch-activity-in-azure-data-factory"></a>Alternar atividade no Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A atividade switch fornece a mesma funcionalidade que uma instrução switch fornece em linguagens de programação. Ele avalia um conjunto de atividades correspondentes a um caso que corresponde à avaliação da condição.

## <a name="syntax"></a>Sintaxe

```json

{
   "name": "<Name of the activity>",
    "type": "Switch",
    "typeProperties": {
        "expression": {
            "value": "<expression that evaluates to some string value>",
            "type": "Expression"
        },
        "cases": [
            {
                "value": "<string value that matches expression evaluation>",
                "activities": [
                    {
                        "<Activity 1 definition>"
                    },
                    {
                        "<Activity 2 definition>"
                    },
                    {
                        "<Activity N definition>"
                    }
                ]
            }           
        ],
        "defaultActivities": [
            {
                "<Activity 1 definition>"
            },
            {
                "<Activity 2 definition>"
            },
            {
                "<Activity N definition>"
            }
        ]
    }
}
```

## <a name="type-properties"></a>Propriedades de tipo

Propriedade | Descrição | Valores permitidos | Obrigatório
-------- | ----------- | -------------- | --------
name | Nome da atividade do comutador. | String | Sim
type | Deve ser definido como *switch** | String | Sim
expressão | Expressão que deve ser avaliada como valor da cadeia de caracteres | Expressão com cadeia de caracteres de tipo de resultado | Sim
cases | Conjunto de casos que contêm um valor e um conjunto de atividades a serem executadas quando o valor corresponde à avaliação da expressão. Deve fornecer pelo menos um caso. Há um limite máximo de 25 casos. | Matriz de objetos Case | Sim
defaultactivities | Conjunto de atividades que são executadas quando a avaliação da expressão não é satisfeita. | Matriz de atividades | Sim

## <a name="example"></a>Exemplo

O pipeline neste exemplo copia dados de uma pasta de entrada para uma pasta de saída. A pasta de saída é determinada pelo valor do parâmetro do pipeline: routeSelection.

> [!NOTE]
> Esta seção fornece definições de JSON e comandos de exemplo do PowerShell para executar o pipeline. Para obter instruções com instruções passo a passo para criar um pipeline do Data Factory usando definições de JSON e do Azure PowerShell, consulte o [tutorial: criar um Data Factory usando o Azure PowerShell](quickstart-create-data-factory-powershell.md).

### <a name="pipeline-with-switch-activity-adfv2quickstartpipelinejson"></a>Pipeline com atividade Switch (Adfv2QuickStartPipeline.jsem)

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "MySwitch",
                "type": "Switch",
                "typeProperties": {
                    "expression": {
                        "value": "@pipeline().parameters.routeSelection",
                        "type": "Expression"
                    },
                    "cases": [
                        {
                            "value": "1",
                            "activities": [
                                {
                                    "name": "CopyFromBlobToBlob1",
                                    "type": "Copy",
                                    "inputs": [
                                        {
                                            "referenceName": "BlobDataset",
                                            "parameters": {
                                                "path": "@pipeline().parameters.inputPath"
                                            },
                                            "type": "DatasetReference"
                                        }
                                    ],
                                    "outputs": [
                                        {
                                            "referenceName": "BlobDataset",
                                            "parameters": {
                                                "path": "@pipeline().parameters.outputPath1",
                                            },
                                            "type": "DatasetReference"
                                        }
                                    ],
                                    "typeProperties": {
                                        "source": {
                                            "type": "BlobSource"
                                        },
                                        "sink": {
                                            "type": "BlobSink"
                                        }
                                    }
                                }
                            ]
                        },
                        {
                            "value": "2",    
                                "activities": [
                                    {
                                        "name": "CopyFromBlobToBlob2",
                                        "type": "Copy",
                                        "inputs": [
                                            {
                                                "referenceName": "BlobDataset",
                                                "parameters": {
                                                    "path": "@pipeline().parameters.inputPath",
                                                },
                                                "type": "DatasetReference"
                                            }
                                        ],
                                        "outputs": [
                                            {
                                                "referenceName": "BlobDataset",
                                                "parameters": {
                                                    "path": "@pipeline().parameters.outputPath2",
                                                },
                                                "type": "DatasetReference"
                                            }
                                        ],
                                        "typeProperties": {
                                            "source": {
                                                "type": "BlobSource"
                                            },
                                            "sink": {
                                                "type": "BlobSink"
                                            }
                                        }
                                    }
                                ]
                            },
                            {
                                "value": "3",
                                "activities": [
                                    {
                                        "name": "CopyFromBlobToBlob3",
                                        "type": "Copy",
                                        "inputs": [
                                                {
                                                "referenceName": "BlobDataset",
                                                "parameters": {
                                                    "path": "@pipeline().parameters.inputPath",
                                                },
                                                "type": "DatasetReference"
                                            }
                                        ],
                                        "outputs": [
                                            {
                                                "referenceName": "BlobDataset",
                                                "parameters": {
                                                    "path": "@pipeline().parameters.outputPath3",
                                                },
                                                "type": "DatasetReference"
                                            }
                                        ],
                                        "typeProperties": {
                                            "source": {
                                                "type": "BlobSource"
                                            },
                                            "sink": {
                                                "type": "BlobSink"
                                            }
                                        }
                                    }
                                ]
                        },
                    ],
                    "defaultActivities": []
                }                    
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath1": {
                "type": "String"
            },
            "outputPath2": {
                "type": "String"
            },
            "outputPath3": {
                "type": "String"
            },
            "routeSelection": {
                "type": "String"
            }
        }
    }
}

```

### <a name="azure-storage-linked-service-azurestoragelinkedservicejson"></a>Serviço vinculado do Armazenamento do Azure (AzureStorageLinkedService.json)

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<Azure Storage account name>;AccountKey=<Azure Storage account key>"
        }
    }
}
```

### <a name="parameterized-azure-blob-dataset-blobdatasetjson"></a>Conjunto de dados parametrizado do Blob do Azure (BlobDataset.json)

Os pipeline define o **folderPath** com o valor do parâmetro **outputPath1** ou **outputPath2** do pipeline. 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": {
                "value": "@{dataset().path}",
                "type": "Expression"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

### <a name="pipeline-parameter-json-pipelineparametersjson"></a>JSON do parâmetro do pipeline (PipelineParameters.json)

```json
{
    "inputPath": "adftutorial/input",
    "outputPath1": "adftutorial/outputCase1",
    "outputPath2": "adftutorial/outputCase2",
    "outputPath2": "adftutorial/outputCase3",
    "routeSelection": "1"
}
```

### <a name="powershell-commands"></a>Comandos do PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Esses comandos pressupõem que você salvou os arquivos JSON na pasta: C:\ADF. 

```powershell
Connect-AzAccount
Select-AzSubscription "<Your subscription name>"

$resourceGroupName = "<Resource Group Name>"
$dataFactoryName = "<Data Factory Name. Must be globally unique>";
Remove-AzDataFactoryV2 $dataFactoryName -ResourceGroupName $resourceGroupName -force


Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -DefinitionFile "C:\ADF\AzureStorageLinkedService.json"
Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "BlobDataset" -DefinitionFile "C:\ADF\BlobDataset.json"
Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "Adfv2QuickStartPipeline" -DefinitionFile "C:\ADF\Adfv2QuickStartPipeline.json"
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName "Adfv2QuickStartPipeline" -ParameterFile C:\ADF\PipelineParameters.json
while ($True) {
    $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

    if ($run) {
        if ($run.Status -ne 'InProgress') {
            Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
            $run
            break
        }
        Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
    }

    Start-Sleep -Seconds 30
}
Write-Host "Activity run details:" -foregroundcolor "Yellow"
$result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
$result

Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
$result.Output -join "`r`n"

Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
$result.Error -join "`r`n"
```

## <a name="next-steps"></a>Próximas etapas

Consulte outras atividades de fluxo de controle com suporte pelo Data Factory: 

- [Atividade Condição Se](control-flow-if-condition-activity.md)
- [Atividade de execução de pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade de obtenção de metadados](control-flow-get-metadata-activity.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade da Web](control-flow-web-activity.md)
