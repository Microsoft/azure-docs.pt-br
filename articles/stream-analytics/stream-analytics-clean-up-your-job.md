---
title: Limpar o trabalho do Azure Stream Analytics
description: Este artigo mostra diferentes métodos para excluir trabalhos do Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: 31812ac805bd3465b1b735842b45adb372286d66
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98016058"
---
# <a name="stop-or-delete-your-azure-stream-analytics-job"></a>Parar ou excluir seu trabalho de Azure Stream Analytics

Azure Stream Analytics trabalhos podem ser facilmente interrompidos ou excluídos por meio do portal do Azure, Azure PowerShell, SDK do Azure para .net ou API REST. Um trabalho de Stream Analytics não pode ser recuperado depois de ser excluído.

>[!NOTE] 
>Quando o trabalho do Azure Stream Analytics é interrompido, os dados persistem somente no armazenamento de entrada e saída, como no Hubs de Eventos ou no Banco de Dados SQL do Azure. Se for necessário para remover dados do Azure, lembre-se de seguir o processo de remoção para os recursos de entrada e saída do seu trabalho do Stream Analytics.

## <a name="stop-a-job-in-azure-portal"></a>Parar um trabalho no portal do Azure

Quando você interrompe um trabalho, os recursos são desprovisionados e o processamento de eventos é interrompido. Os encargos relacionados a esse trabalho também são interrompidos. No entanto, todas as suas configurações são mantidas e você pode reiniciar o trabalho mais tarde 

1. Entre no [portal do Azure](https://portal.azure.com). 

2. Localize o trabalho em execução do Stream Analytics e selecione-o.

3. Na página do trabalho no Stream Analytics, selecione **Parar** para interrompê-lo. 

   ![Interromper um trabalho do Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Excluir um trabalho no portal do Azure

>[!WARNING] 
>Um trabalho de Stream Analytics não pode ser recuperado depois de ser excluído.

1. Entre no portal do Azure. 

2. Localize o trabalho existente do Stream Analytics e selecione-o.

3. Na página do trabalho no Stream Analytics, selecione **Excluir** para excluí-lo. 

   ![Excluir um trabalho do Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Parar ou excluir um trabalho usando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para interromper um trabalho usando o PowerShell, use o cmdlet [Stop-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) . Para excluir um trabalho usando o PowerShell, use o cmdlet [Remove-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob) .

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Parar ou excluir um trabalho usando o SDK do Azure para .NET

Para interromper um trabalho usando o SDK do Azure para .NET, use o método [StreamingJobsOperationsExtensions.BeginStop](/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop). Para excluir um trabalho usando o SDK do Azure para .NET, use o método [StreamingJobsOperationsExtensions.BeginDelete](/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete).

## <a name="stop-or-delete-a-job-using-rest-api"></a>Parar ou excluir um trabalho usando a API REST

Para interromper um trabalho usando a API REST, confira o método [Parar](/rest/api/streamanalytics/2016-03-01/streamingjobs/stop). Para excluir um trabalho usando a API REST, confira o método [Excluir](/rest/api/streamanalytics/2016-03-01/streamingjobs/delete).