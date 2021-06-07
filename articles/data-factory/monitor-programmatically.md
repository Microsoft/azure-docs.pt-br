---
title: Monitorar programaticamente um Azure Data Factory
description: Saiba como monitorar um pipeline em um data factory usando diferentes SDKs (Software Development Kit).
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/16/2018
author: dcstwh
ms.author: weetok
ms.custom: devx-track-python
ms.openlocfilehash: 6c913c7c623c77baea0c575d06d2c44709af43fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101740426"
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>Monitorar programaticamente um Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve como monitorar um pipeline em um data factory usando diferentes SDKs (Software Development Kit). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="data-range"></a>Intervalo de dados

O Data Factory armazena apenas os dados executados no pipeline por 45 dias. Quando você consulta por meio de programação dados sobre execuções de pipeline do Data Factory, por exemplo, com o comando do PowerShell `Get-AzDataFactoryV2PipelineRun`, não há nenhuma data máxima para os parâmetros `LastUpdatedAfter` e `LastUpdatedBefore` opcionais. Mas se você consultar dados para o ano passado, por exemplo, você não receberá um erro, mas somente o pipeline executará os dados dos últimos 45 dias.

Se você quiser manter os dados de execução do pipeline por mais de 45 dias, configure seu próprio log de diagnóstico com [Azure monitor](monitor-using-azure-monitor.md).

## <a name="pipeline-run-information"></a>Informações de execução de pipeline

Para propriedades de execução de pipeline, consulte [referência de API do PipelineRun](/rest/api/datafactory/pipelineruns/get#pipelinerun). Uma execução de pipeline tem status diferente durante seu ciclo de vida, os valores possíveis de status de execução são listados abaixo:

* Em fila
* InProgress
* Com sucesso
* Com falha
* Cancelando
* Canceled

## <a name="net"></a>.NET
Para obter um passo a passo completo de criação e monitoramento de um pipeline usando o SDK do .NET, consulte [criar um data Factory e um pipeline usando o .net](quickstart-create-data-factory-dot-net.md).

1. Adicione o código a seguir para verificar continuamente o status da execução do pipeline até que ele termine de copiar os dados.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress" || pipelineRun.Status == "Queued")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Adicione o código a seguir para recuperar os detalhes de execução da atividade de cópia, por exemplo, o tamanho dos dados lidos/gravados.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");
   
    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
    if (pipelineRun.Status == "Succeeded")
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

Consulte [Referência do SDK do .NET do Data Factory](/dotnet/api/microsoft.azure.management.datafactory) para obter uma documentação completa sobre o SDK do .NET.

## <a name="python"></a>Python
Para obter um passo a passo completo de criação e monitoramento de um pipeline usando o SDK do Python, consulte [criar um data Factory e um pipeline usando o Python](quickstart-create-data-factory-python.md).

Para monitorar a execução de pipeline, adicione o código a seguir:

```python
# Monitor the pipeline run
time.sleep(30)
pipeline_run = adf_client.pipeline_runs.get(
    rg_name, df_name, run_response.run_id)
print("\n\tPipeline run status: {}".format(pipeline_run.status))
activity_runs_paged = list(adf_client.activity_runs.list_by_pipeline_run(
    rg_name, df_name, pipeline_run.run_id, datetime.now() - timedelta(1),  datetime.now() + timedelta(1)))
print_activity_run_details(activity_runs_paged[0])
```

Para obter uma documentação completa sobre o SDK do Python, consulte [Referência do SDK do Python do Data Factory](/python/api/overview/azure/datafactory).

## <a name="rest-api"></a>API REST
Para obter um passo a passo completo de criação e monitoramento de um pipeline usando a API REST, consulte [criar um data Factory e um pipeline usando a API REST](quickstart-create-data-factory-rest-api.md).
 
1. Execute o script a seguir para verificar continuamente o status da execução de pipeline até que ela termine de copiar os dados.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ( ($response.Status -eq "InProgress") -or ($response.Status -eq "Queued") ) {
            Start-Sleep -Seconds 15
        }
        else {
            $response | ConvertTo-Json
            break
        }
    }
    ```
2. Execute o script a seguir para recuperar os detalhes de execução da atividade de cópia, por exemplo, o tamanho dos dados lidos/gravados.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}/activityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```

Para obter uma documentação completa sobre a API REST, consulte a [Referência da API REST do Data Factory](/rest/api/datafactory/).

## <a name="powershell"></a>PowerShell
Para obter um passo a passo completo de criação e monitoramento de um pipeline usando o PowerShell, consulte [criar um data Factory e um pipeline usando o PowerShell](quickstart-create-data-factory-powershell.md).

1. Execute o script a seguir para verificar continuamente o status da execução de pipeline até que ela termine de copiar os dados.

    ```powershell
    while ($True) {
        $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ( ($run.Status -ne "InProgress") -and ($run.Status -ne "Queued") ) {
                Write-Output ("Pipeline run finished. The status is: " +  $run.Status)
                $run
                break
            }
            Write-Output ("Pipeline is running...status: " + $run.Status)
        }

        Start-Sleep -Seconds 30
    }
    ```
2. Execute o script a seguir para recuperar os detalhes de execução da atividade de cópia, por exemplo, o tamanho dos dados lidos/gravados.

    ```powershell
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```

Para ver uma documentação abrangente sobre os cmdlets do PowerShell, consulte [Referência de cmdlets do PowerShell do Data Factory](/powershell/module/az.datafactory).

## <a name="next-steps"></a>Próximas etapas
Consulte o artigo [Monitor pipelines usando o Azure Monitor](monitor-using-azure-monitor.md) para saber mais sobre como usar o Azure Monitor para monitorar os pipelines do Data Factory.