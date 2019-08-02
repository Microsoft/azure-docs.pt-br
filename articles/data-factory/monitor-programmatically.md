---
title: Monitorar programaticamente um Azure Data Factory | Microsoft Docs
description: Saiba como monitorar um pipeline em um data factory usando diferentes SDKs (Software Development Kit).
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: 4538cb987d88c92e379640e69b29ad5c8c75a520
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360396"
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>Monitorar programaticamente um Azure Data Factory
Este artigo descreve como monitorar um pipeline em um data factory usando diferentes SDKs (Software Development Kit). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="data-range"></a>Intervalo de dados

O Data Factory armazena apenas os dados executados no pipeline por 45 dias. Quando você consulta por meio de programação dados sobre execuções de pipeline do Data Factory, por exemplo, com o comando do PowerShell `Get-AzDataFactoryV2PipelineRun`, não há nenhuma data máxima para os parâmetros `LastUpdatedAfter` e `LastUpdatedBefore` opcionais. Mas se você consultar dados do ano passado, por exemplo, a consulta não retornará um erro, mas retornará apenas dados executados no pipeline dos últimos 45 dias.

Se você quiser manter os dados executados no pipeline por mais de 45 dias, configure seu próprio log de diagnósticos com o [Azure Monitor](monitor-using-azure-monitor.md).

## <a name="net"></a>.NET
Para ver um passo a passo completo da criação e monitoramento de um pipeline usando o SDK do .NET, consulte [Criar um data factory e um pipeline usando o .NET](quickstart-create-data-factory-dot-net.md).

1. Adicione o código a seguir para verificar continuamente o status da execução do pipeline até que ele termine de copiar os dados.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
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

Consulte [Referência do SDK do .NET do Data Factory](/dotnet/api/microsoft.azure.management.datafactory?view=azure-dotnet) para obter uma documentação completa sobre o SDK do .NET.

## <a name="python"></a>Python
Para ver um passo a passo completo da criação e monitoramento de um pipeline usando o SDK de Python, consulte [Criar um data factory e um pipeline usando Python](quickstart-create-data-factory-python.md).

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

Para obter uma documentação completa sobre o SDK do Python, consulte [Referência do SDK do Python do Data Factory](/python/api/overview/azure/datafactory?view=azure-python).

## <a name="rest-api"></a>API REST
Para ver um passo a passo completo da criação e monitoramento de um pipeline usando a API REST, consulte [Criar um data factory e um pipeline usando a API REST](quickstart-create-data-factory-rest-api.md).
 
1. Execute o script a seguir para verificar continuamente o status da execução de pipeline até que ela termine de copiar os dados.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ($response.Status -eq "InProgress") {
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
Para ver um passo a passo completo da criação e monitoramento de um pipeline usando o PowerShell, consulte [Criar um data factory e um pipeline usando o PowerShell](quickstart-create-data-factory-powershell.md).

1. Execute o script a seguir para verificar continuamente o status da execução de pipeline até que ela termine de copiar os dados.

    ```powershell
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

