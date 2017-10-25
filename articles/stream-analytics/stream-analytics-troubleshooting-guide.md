---
title: "Guia de solução de problemas do Stream Analytics do Azure | Microsoft Docs"
description: Como resolver problemas do trabalho do Stream Analytics
keywords: "guia de solução de problemas"
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
ms.openlocfilehash: dcff312e4a282b15e76ea32aadb1981a496a2446
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-guide-for-azure-stream-analytics"></a>Guia de solução de problemas do Stream Analytics do Azure

A solução de problemas do Stream Analytics do Azure pode parecer ser um esforço complexo à primeira vista. Depois de trabalhar com vários usuários, criamos este guia para ajudar a simplificar o processo e remover as suposições sobre suas entradas, saídas, consultas e funções.

## <a name="troubleshoot-your-stream-analytics-job"></a>Resolver problemas do trabalho do Stream Analytics

Para obter melhores resultados na solução de problemas do trabalho do Stream Analytics, use as seguintes diretrizes:

1.  Testar a conectividade:
    - Verifique a conectividade com as entradas e saídas usando o botão **Testar Conexão** em cada entrada e saída.

2.  Examinar os dados de entrada:
    - Para verificar se os dados de entrada estão fluindo para o Hub de Eventos, use o [Gerenciador do Barramento de Serviço](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a) para se conectar ao Hub de Eventos do Azure (caso a entrada do Hub de Eventos seja usada).  
    - Use o botão [**Dados de Exemplo**](stream-analytics-sample-data-input.md) para cada entrada e baixe os dados de exemplo de entrada.
    - Inspecione os dados de exemplo para entender a forma deles: o esquema e os [tipos de dados](https://msdn.microsoft.com/library/azure/dn835065.aspx).

3.  Testar a consulta:
    - Na guia **Consulta**, use o botão **Testar** para testar a consulta e use os dados de exemplo baixados para [testar a consulta](stream-analytics-test-query.md). Examine os erros e tente corrigi-los.
    - Se você usar [**Carimbo de Data/Hora Por**](https://msdn.microsoft.com/library/azure/mt573293.aspx), verifique se os eventos têm carimbos de data/hora maiores que a [hora de início do trabalho](stream-analytics-out-of-order-and-late-events.md).

4.  Depurar a consulta:
    - Recompile a consulta progressivamente desde uma instrução select simples até agregações mais complexas usando etapas. Para criar a lógica da consulta passo a passo, use cláusulas [WITH](https://msdn.microsoft.com/library/azure/dn835049.aspx).
    - Use [SELECT INTO](stream-analytics-select-into.md) para depurar as etapas da consulta.

5.  Elimine armadilhas comuns, como:
    - Uma cláusula [**WHERE**](https://msdn.microsoft.com/library/azure/dn835048.aspx) na consulta filtrou todos os eventos, impedindo que uma saída seja gerada.
    - Ao usar funções de janela, aguarde a duração de toda a janela para ver uma saída da consulta.
    - O carimbo de data/hora de eventos precede a hora de início do trabalho e, portanto, os eventos são removidos.

6.  Usar a ordenação de eventos:
    - Se todas as etapas anteriores funcionaram bem, acesse a folha **Configurações** e selecione [**Ordenação de Eventos**](stream-analytics-out-of-order-and-late-events.md). Verificar se essa política está configurada para o que faz sentido em seu cenário. A política *não* é aplicada quando você usa o botão **Testar** para testar a consulta. Esse resultado é uma das diferenças entre o teste no navegador comparado à execução do trabalho em produção.

7.  Depurar usando métricas:
    - Se você não obter nenhuma saída após a duração esperada (com base na consulta), tente o seguinte:
        - Examine [**Métricas de Monitoramento**](stream-analytics-monitoring.md) na guia **Monitor**. Como os valores são agregados, as métricas são atrasadas em alguns minutos.
            - Se os Eventos de Entrada forem > 0, o trabalho poderá ler os dados de entrada. Se os Eventos de Entrada não forem > 0:
                - Para ver se a fonte de dados tem dados válidos, verifique-a usando o [Gerenciador do Barramento de Serviço](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a). Essa seleção se aplica se o trabalho está usando o Hub de Eventos como entrada.
                - Verifique se o formato de serialização de dados e a codificação de dados são como esperado.
                - Se o trabalho estiver usando um Hub de Eventos, verifique se o corpo da mensagem será *Null*.
            - Se os Erros de Conversão de Dados forem > 0 e estiverem aumentando, os seguintes casos poderão ser verdadeiros:
                - O trabalho pode não conseguir desserializar os eventos.
                - O esquema de eventos pode não corresponder ao esquema definido ou esperado dos eventos na consulta.
                - Os tipos de dados de alguns dos campos no evento podem não corresponder às expectativas.
            - Se os Erros em Tempo de Execução forem > 0, isso significa que o trabalho pode receber os dados, mas está gerando erros durante o processamento da consulta.
                - Para encontrar os erros, acesse os [Logs de Auditoria](../azure-resource-manager/resource-group-audit.md) e filtre o status *Com Falha*.
            - Se os InputEvents forem > 0 e os OutputEvents forem = 0, isso significa que um dos seguintes casos é verdadeiro:
                - O processamento de consulta resultou em zero evento de saída.
                - Os eventos ou seus campos podem estar malformados, resultando em nenhuma saída após o processamento da consulta.
                - O trabalho não pôde enviar dados por push para o [coletor de saída](stream-analytics-select-into.md) por motivos de conectividade ou autenticação.
        - Em todos os casos de erro mencionados anteriormente, as mensagens do log de operações explicam os detalhes adicionais (incluindo o que está acontecendo), exceto nos casos em que a lógica da consulta filtrou todos os eventos. Se o processamento de vários eventos gerar erros, o Stream Analytics registrará as três primeiras mensagens de erro do mesmo tipo em até 10 minutos nos Logs de operações. Em seguida, ele suprimirá erros idênticos adicionais com a mensagem “Os erros estão ocorrendo muito rapidamente e estão sendo suprimidos”.

8. Depurar usando os logs de auditoria e de diagnóstico:
    - Use os [Logs de Auditoria](../azure-resource-manager/resource-group-audit.md) e filtre para identificar e depurar erros.
    - Use os [logs de diagnóstico do trabalho](stream-analytics-job-diagnostic-logs.md) para identificar e depurar erros.

9. Examinar as saídas:
    - Quando o status do trabalho for *Em Execução*, dependendo da duração estipulada na consulta, você poderá ver a saída na fonte de dados do coletor.
    - Se você não vir as saídas que estão indo para um tipo de saída específico, redirecione-as para um tipo de saída menos complexo, como um Blob do Azure. Com o Gerenciador de Armazenamento, verifique se a saída pode ser vista. Além disso, verifique se as limitações na saída estão impedindo o recebimento de dados.

10. Use a análise de fluxo de dados com métricas do diagrama de trabalho:
    - Para analisar o fluxo de dados e identificar problemas, use o [diagrama de trabalho com métricas](stream-analytics-job-diagram-with-metrics.md).

11. Abrir um caso de suporte:
    - Por fim, se todas as outras soluções falharem, abra um caso de suporte da Microsoft usando a SubscriptionID que contém o trabalho.

## <a name="get-help"></a>Obter ajuda

Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
