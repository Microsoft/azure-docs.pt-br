---
title: "Solucionar problemas do Stream Analytics do Azure com logs de diagnóstico | Microsoft Docs"
description: "Saiba como analisar logs de diagnóstico de trabalhos do Stream Analytics no Microsoft Azure."
keywords: 
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: samacha
ms.openlocfilehash: c9772df2c216d465ca6e90e69bce011969dd4f02
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Solucionar problemas do Stream Analytics do Azure usando logs de diagnóstico

Ocasionalmente, um trabalho do Stream Analytics do Azure interrompe o processamento de forma inesperada. É importante conseguir resolver esse tipo de evento. O evento pode ser causado por um resultado de consulta inesperado, pela conectividade a dispositivos ou por uma interrupção inesperada do serviço. Os logs de diagnóstico do Stream Analytics podem ajudá-lo a identificar a causa dos problemas assim que eles ocorrem e reduzir o tempo de recuperação.

## <a name="log-types"></a>Tipos de logs

O Stream Analytics oferece dois tipos de logs: 
* [Logs de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (sempre ativados). Os logs de atividades fornecem informações sobre as operações realizadas nos trabalhos.
* [Logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (configuráveis). Os logs de diagnóstico fornecem informações mais avançadas sobre tudo o que acontece com um trabalho. Os logs de diagnóstico são iniciados quando o trabalho é criado e terminam quando o trabalho é excluído. Eles abrangem eventos quando o trabalho é atualizado e durante sua execução.

> [!NOTE]
> Você pode usar serviços como o Armazenamento do Azure, os Hubs de Eventos do Azure e o Azure Log Analytics para analisar dados que não estão em conformidade. Você é cobrado com base no modelo de preço desses serviços.
>

## <a name="turn-on-diagnostics-logs"></a>Ativar os logs de diagnóstico

Os logs de diagnóstico estão **desativados** por padrão. Para ativar os logs de diagnóstico, realize estas etapas:

1.  Entre no portal do Azure e acesse a folha do trabalho de streaming. Em **Monitoramento**, selecione **Logs de diagnóstico**.

    ![Navegação na folha para os logs de diagnóstico](./media/stream-analytics-job-diagnostic-logs/image1.png)  

2.  Selecione **Ativar diagnóstico**.

    ![Ativar os logs de diagnóstico](./media/stream-analytics-job-diagnostic-logs/image2.png)

3.  Na página **Configurações de diagnóstico**, em **Status**, selecione **Ativado**.

    ![Alterar o status dos logs de diagnóstico](./media/stream-analytics-job-diagnostic-logs/image3.png)

4.  Configure o destino de arquivamento (conta de armazenamento, hub de eventos, Log Analytics) desejado. Depois, selecione as categorias de logs que você deseja coletar (Execução, Criação). 

5.  Salve a nova configuração de diagnóstico.

A configuração de diagnóstico leva cerca de 10 minutos para entrar em vigor. Depois disso, os logs começarão a aparecer no destino de arquivamento configurado (que pode ser visto na página **Logs de diagnóstico**):

![Navegação na folha para os logs de diagnóstico – destinos de arquivamento](./media/stream-analytics-job-diagnostic-logs/image4.png)

Para obter informações sobre como configurar o diagnóstico, consulte [Coletar e consumir dados de diagnóstico nos recursos do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="diagnostics-log-categories"></a>Categorias de logs de diagnóstico

Atualmente, capturamos duas categorias de logs de diagnóstico:

* **Criação**. Captura eventos de log relacionados a operações de criação de trabalho: criação de trabalho, adição e exclusão de entradas e saídas, adição e atualização da consulta, início e parada do trabalho.
* **Execução**. Captura eventos que ocorrem durante a execução do trabalho:
    * Erros de conectividade
    * Erros de processamento de dados, incluindo:
        * Eventos que não estão em conformidade com a definição de consulta (valores e tipos de campo sem correspondência, campos ausentes e assim por diante)
        * Erros de avaliação da expressão
    * Outros erros e eventos

## <a name="diagnostics-logs-schema"></a>Esquema dos logs de diagnóstico

Todos os logs são armazenados no formato JSON. Cada entrada tem os seguintes campos de cadeia de caracteres comuns:

Nome | Descrição
------- | -------
tempo real | Carimbo de data/hora (em UTC) do log.
resourceId | ID do recurso em que a operação ocorreu, em maiúsculas. Inclui a ID da assinatura, o grupo de recursos e o nome do trabalho. Por exemplo, **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
categoria | Categoria do log, **Execução** ou **Criação**.
operationName | Nome da operação que está registrada. Por exemplo, **Enviar Eventos: falha na gravação da Saída do SQL em mysqloutput**.
status | Status da operação. Por exemplo, **Com Falha** ou **Com Êxito**.
level | Nível do log. Por exemplo, **Erro**, **Aviso** ou **Informativo**.
propriedades | Detalhes específicos à entrada de log, serializados como uma cadeia de caracteres JSON. Para obter mais informações, consulte as próximas seções.

### <a name="execution-log-properties-schema"></a>Esquema de propriedades do log de execução

Os logs de execução trazem informações sobre eventos que ocorreram durante a execução do trabalho do Stream Analytics. O esquema de propriedades varia, dependendo do tipo de evento. No momento, temos os seguintes tipos de logs de execução:

### <a name="data-errors"></a>Erros de dados

Qualquer erro ocorrido enquanto o trabalho processa os dados está nessa categoria de logs. Esses logs costumam ser criados durante operações de leitura, serialização e gravação de dados. Esses logs não incluem erros de conectividade. Os erros de conectividade são tratados como eventos genéricos.

Nome | Descrição
------- | -------
Fonte | Nome da entrada ou saída do trabalho em que ocorreu o erro.
Mensagem | Mensagem associada ao erro.
Tipo | Tipo de erro. Por exemplo, **DataConversionError**, **CsvParserError** ou **ServiceBusPropertyColumnMissingError**.
Dados | Contém dados que são úteis para localizar com precisão a origem do erro. Sujeito a truncamento, dependendo do tamanho.

Dependendo do valor de **operationName**, os erros de dados terão o seguinte esquema:
* **Serializar eventos**. Serializar os eventos ocorridos durante operações de leitura de eventos. Eles ocorrem quando os dados na entrada não atendem ao esquema de consulta por um destes motivos:
    * *Tipos incompatíveis durante (des)serialização de eventos*: identifica o campo que está causando o erro.
    * *Não é possível ler um evento, serialização inválida*: lista informações sobre o local nos dados de entrada em que ocorreu o erro. Inclui o nome do blob para a entrada do blob, o deslocamento e uma amostra dos dados.
* **Enviar eventos**. Envia eventos ocorridos durante operações de gravação. Ele identifica o evento de streaming que causou o erro.

### <a name="generic-events"></a>Eventos genéricos

Os eventos genéricos abrangem todo o resto.

Nome | Descrição
-------- | --------
Erro | (opcional) Informações sobre erros. Normalmente, essas são informações de exceção, se estiverem disponíveis.
Mensagem| Mensagem de log.
Tipo | Tipo de mensagem. É mapeado para a categorização interna de erros. Por exemplo, **JobValidationError** ou **BlobOutputAdapterInitializationFailure**.
ID de Correlação | [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) que identifica exclusivamente a execução do trabalho. Todas as entradas do log de execução desde a hora em que o trabalho é iniciado até ele ser interrompido têm o mesmo valor de **ID de Correlação**.

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Stream Analytics](stream-analytics-introduction.md)
* [Introdução ao Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência da linguagem de consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST de gerenciamento do Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
