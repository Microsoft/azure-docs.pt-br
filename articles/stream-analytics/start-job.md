---
title: Como iniciar um trabalho do Azure Stream Analytics
description: Este artigo descreve como iniciar um trabalho do Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 9bc3e4132919e5fc5baadc78841e66efd3c34bcd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61362258"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Como iniciar um trabalho do Azure Stream Analytics

Você pode iniciar o trabalho do Azure Stream Analytics usando o portal do Azure, o Visual Studio e o PowerShell. Quando você inicia um trabalho, você pode selecionar um intervalo para o trabalho Iniciar a criação de saída. Portal do Azure, Visual Studio e PowerShell têm diferentes métodos para definir a hora de início. Esses métodos são descritos abaixo.

## <a name="start-options"></a>Opções de inicialização
As três opções seguintes estão disponíveis para iniciar um trabalho. Observe que todos os horários mencionados a seguir são aquelas especificadas [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics). Se TIMESTAMP BY não for especificado, a hora de chegada será usada.
* **Agora**: Faz com que o ponto de partida do evento de saída de transmitir o mesmo que quando o trabalho é iniciado. Se um operador temporal é usado (por exemplo, janela de tempo, LATÊNCIA ou junção), Azure Stream Analytics será automaticamente examinar novamente os dados na fonte de entrada. Por exemplo, se você iniciar um trabalho de "Agora" e se sua consulta usa uma janela em cascata de 5 minutos, o Azure Stream Analytics irá procurar dados de 5 minutos atrás na entrada.
O primeiro evento de saída possíveis teria um carimbo de hora igual ou maior que a hora atual, e o ASA garante que todos os eventos de entrada que logicamente podem contribuir para a saída tem sido considerado. Por exemplo, não há agregações em janela parciais são geradas. É sempre o valor agregado completo.

* **Personalizado**: Você pode escolher o ponto de partida da saída. Da mesma forma que o **agora** opção, o Azure Stream Analytics lerá automaticamente os dados antes desta vez se um operador temporal é usado 

* **Na última interrupção**. Essa opção está disponível quando o trabalho foi iniciado anteriormente, mas foi interrompido manualmente ou falha. Ao escolher essa opção do Azure Stream Analytics usará a última hora de saída para reiniciar o trabalho, portanto, nenhum dado será perdido. Da mesma forma para as opções anteriores, Azure Stream Analytics lerá automaticamente os dados antes desta vez se um operador temporal é usado. Como várias partições de entrada podem ter uma hora diferente, a primeira hora de parada de todas as partições é usada, como resultado, alguns duplicatas podem ser vistas na saída. Para obter mais informações sobre exatamente-uma vez processamento estão disponíveis na página [garantias de entrega de evento](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).


## <a name="azure-portal"></a>Portal do Azure

Navegue até o trabalho no portal do Azure e selecione **iniciar** na página de visão geral. Selecione uma **hora de início da saída do trabalho** e, em seguida, selecione **iniciar**.

Escolha uma das opções para **hora de início da saída do trabalho**. As opções são *agora*, *personalizado*, e, se o trabalho foi executado anteriormente, *na última interrupção*. Consulte acima para obter mais informações sobre essas opções.

## <a name="visual-studio"></a>Visual Studio

Na exibição de trabalho, selecione o botão de seta verde para iniciar o trabalho. Defina as **modo de início de saída de trabalho** e selecione **iniciar**. O status do trabalho será alterado para **executando**.

Há três opções para **modo de início de saída do trabalho**: *JobStartTime*, *CustomTime*, e *LastOutputEventTime*. Se essa propriedade estiver ausente, o padrão é *JobStartTime*. Consulte acima para obter mais informações sobre essas opções.


## <a name="powershell"></a>PowerShell

Use o seguinte cmdlet para iniciar seu trabalho usando o PowerShell:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

Há três opções para **OutputStartMode**: *JobStartTime*, *CustomTime*, e *LastOutputEventTime*. Se essa propriedade estiver ausente, o padrão é *JobStartTime*. Consulte acima para obter mais informações sobre essas opções.

Para obter mais informações sobre o `Start-AzStreamAnalyitcsJob` cmdlet, o modo de exibição de [AzStreamAnalyticsJob início referência](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob).

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Criar um trabalho do Stream Analytics usando o portal do Azure](stream-analytics-quick-create-portal.md)
* [Início Rápido: Criar um trabalho de Stream Analytics usando o Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Início Rápido: Criar um trabalho do Stream Analytics usando as ferramentas do Azure Stream Analytics para Visual Studio](stream-analytics-quick-create-vs.md)
