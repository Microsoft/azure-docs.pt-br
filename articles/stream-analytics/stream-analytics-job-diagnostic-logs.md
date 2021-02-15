---
title: Solucionar problemas Azure Stream Analytics usando logs de recursos
description: Este artigo descreve como analisar os logs de recursos no Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: troubleshooting
ms.custom: contperf-fy21q1
ms.date: 06/18/2020
ms.openlocfilehash: 56c7ad49fc9af3ca4f30aa5309623c31e9db7385
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98011111"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-resource-logs"></a>Solucionar problemas do Azure Stream Analytics usando logs de recursos

Ocasionalmente, um trabalho do Stream Analytics do Azure interrompe o processamento de forma inesperada. É importante conseguir resolver esse tipo de evento. As falhas podem ser causadas por um resultado de consulta inesperado, pela conexão com dispositivos ou por uma interrupção inesperada do serviço. Os logs de recursos no Stream Analytics podem ajudá-lo a identificar a causa dos problemas quando eles ocorrem e reduzir o tempo de recuperação.

É altamente recomendável habilitar os logs de recursos para todos os trabalhos, pois isso ajudará muito na depuração e no monitoramento.

## <a name="log-types"></a>Tipos de logs

O Stream Analytics oferece dois tipos de logs:

* [Logs de atividades](../azure-monitor/platform/platform-logs-overview.md) (Always On), que fornecem informações sobre as operações realizadas nos trabalhos.

* [Logs de recursos](../azure-monitor/platform/platform-logs-overview.md) (configuráveis), que fornecem informações mais ricas sobre tudo o que acontece com um trabalho. Os logs de recursos iniciam quando o trabalho é criado e terminam quando o trabalho é excluído. Eles abrangem eventos quando o trabalho é atualizado e durante sua execução.

> [!NOTE]
> Você pode usar serviços como o armazenamento do Azure, hubs de eventos do Azure e logs de Azure Monitor para analisar dados não conformes. Você é cobrado com base no modelo de preço desses serviços.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>Depurando com o uso de logs de atividades

Os logs de atividades são ativados por padrão e fornecem insights de alto nível sobre as operações realizadas pelo trabalho do Stream Analytics. As informações presentes nos logs de atividades podem ajudar a encontrar a causa raiz dos problemas que afetam seu trabalho. Execute as seguintes etapas para usar os logs de atividades no Stream Analytics:

1. Entre no portal do Azure e selecione **Log de atividades** em **Visão Geral**.

   ![Log de atividades do Stream Analytics](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. Você pode ver uma lista das operações que foram executadas. As operações que causaram a falha do trabalho têm uma bolha de informações em vermelho.

3. Clique em uma operação para exibir o resumo. As informações fornecidas aqui geralmente são limitadas. Para obter mais detalhes sobre a operação, clique no **JSON**.

   ![Resumo de operações do log de atividades do Stream Analytics](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. Role para baixo até a seção **Propriedades** do JSON, que fornece os detalhes do erro que causou a operação com falha. Neste exemplo, a falha ocorreu devido a um erro de runtime de valores de latitude fora do limite. A discrepância nos dados que são processados por um trabalho de Stream Analytics causa um erro de dados. Você pode aprender sobre [erros de dados de entrada e saída diferentes e por que eles ocorrem](./data-errors.md).

   ![Detalhes do erro JSON](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. Você pode realizar ações corretivas com base na mensagem de erro no JSON. Neste exemplo, será necessário adicionar verificações do valor de latitude entre -90 graus e 90 graus à consulta.

6. Se a mensagem de erro nos logs de atividade não for útil para identificar a causa raiz, habilite os logs de recursos e use os logs de Azure Monitor.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Enviar diagnósticos para logs de Azure Monitor

É altamente recomendável ativar os logs de recursos e enviá-los para Azure Monitor logs. Eles estão **desativados** por padrão. Para ativá-los, conclua estas etapas:

1.  Crie um espaço de trabalho Log Analytics se você ainda não tiver um. É recomendável ter seu espaço de trabalho do Log Analytics na mesma região que o seu trabalho de Stream Analytics.

2.  Entre no portal do Azure e navegue até o trabalho do Stream Analytics. Em **Monitoramento**, selecione **Logs de diagnóstico**. Em seguida, selecione **ativar o diagnóstico**.

    ![Navegação de folha para logs de recursos](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Forneça um **nome** no **nome das configurações de diagnóstico** e marque as caixas de **execução** e **criação** em **log** e **biométricas** em **métrica**. Em seguida, selecione **Enviar para log Analytics** e escolha seu espaço de trabalho. Clique em **Salvar**.

    ![Configurações para logs de recursos](./media/stream-analytics-job-diagnostic-logs/logs-setup.png)

3. Quando seu trabalho de Stream Analytics é iniciado, os logs de recursos são roteados para seu espaço de trabalho do Log Analytics. Para exibir os logs de recursos para seu trabalho, selecione **logs** na seção **monitoramento** .

   ![Captura de tela mostra o menu geral com logs selecionados.](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs.png)

4. Stream Analytics fornece consultas predefinidas que permitem pesquisar facilmente os logs nos quais você está interessado. Você pode selecionar qualquer consulta predefinida no painel esquerdo e, em seguida, selecionar **executar**. Você verá os resultados da consulta no painel inferior. 

   ![Captura de tela mostra os logs de um trabalho Stream Analytics.](./media/stream-analytics-job-diagnostic-logs/logs-example.png)

## <a name="resource-log-categories"></a>Categorias de log de recursos

Azure Stream Analytics captura duas categorias de logs de recursos:

* **Criação**: captura eventos de log relacionados a operações de criação de trabalho, como criação de trabalho, adição e exclusão de entradas e saídas, adição e atualização da consulta e início ou interrupção do trabalho.

* **Execução**: captura eventos que ocorrem durante a execução do trabalho.
    * Erros de conectividade
    * Erros de processamento de dados, incluindo:
        * Eventos que não estão em conformidade com a definição de consulta (valores e tipos de campo sem correspondência, campos ausentes e assim por diante)
        * Erros de avaliação da expressão
    * Outros erros e eventos

## <a name="resource-logs-schema"></a>Esquema de logs de recursos

Todos os logs são armazenados no formato JSON. Cada entrada tem os seguintes campos de cadeia de caracteres comuns:

Nome | Descrição
------- | -------
time | Carimbo de data/hora (em UTC) do log.
resourceId | ID do recurso em que a operação ocorreu, em maiúsculas. Inclui a ID da assinatura, o grupo de recursos e o nome do trabalho. Por exemplo, **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
category | Categoria do log, **Execução** ou **Criação**.
operationName | Nome da operação que está registrada. Por exemplo, **enviar eventos: falha de gravação de saída do SQL em em mysqloutput**.
status | Status da operação. Por exemplo, **Com Falha** ou **Com Êxito**.
nível | Nível do log. Por exemplo, **Erro**, **Aviso** ou **Informativo**.
properties | Detalhes específicos à entrada de log, serializados como uma cadeia de caracteres JSON. Para obter mais informações, consulte as próximas seções deste artigo.

### <a name="execution-log-properties-schema"></a>Esquema de propriedades do log de execução

Os logs de execução trazem informações sobre eventos que ocorreram durante a execução do trabalho do Stream Analytics. O esquema de propriedades varia dependendo se o evento é um erro de dados ou um evento genérico.

### <a name="data-errors"></a>Erros de dados

Qualquer erro ocorrido enquanto o trabalho processa os dados está nessa categoria de logs. Esses logs costumam ser criados durante operações de leitura, serialização e gravação de dados. Esses logs não incluem erros de conectividade. Os erros de conectividade são tratados como eventos genéricos. Você pode saber mais sobre a causa de vários [erros de dados de entrada e saída](./data-errors.md)diferentes.

Nome | Descrição
------- | -------
Fonte | Nome da entrada ou saída do trabalho em que ocorreu o erro.
Mensagem | Mensagem associada ao erro.
Type | Tipo de erro. Por exemplo, **DataConversionError**, **CsvParserError** ou **ServiceBusPropertyColumnMissingError**.
Dados | Contém dados que são úteis para localizar com precisão a origem do erro. Sujeito a truncamento, dependendo do tamanho.

Dependendo do valor de **operationName**, os erros de dados terão o seguinte esquema:

* Os **eventos serializados** ocorrem durante operações de leitura de eventos. Eles ocorrem quando os dados na entrada não atendem ao esquema de consulta por um destes motivos:

   * *Tipos incompatíveis durante (des)serialização de eventos*: identifica o campo que está causando o erro.

   * *Não é possível ler um evento, serialização inválida*: lista informações sobre o local nos dados de entrada em que ocorreu o erro. Inclui o nome do blob para a entrada do blob, o deslocamento e uma amostra dos dados.

* Os **eventos de envio** ocorrem durante operações de gravação. Ele identifica o evento de streaming que causou o erro.

### <a name="generic-events"></a>Eventos genéricos

Os eventos genéricos abrangem todo o resto.

Nome | Descrição
-------- | --------
Erro | (opcional) Informações sobre erros. Normalmente, essas são informações de exceção, se estiverem disponíveis.
Mensagem| Mensagem de log.
Type | Tipo de mensagem. É mapeado para a categorização interna de erros. Por exemplo, **JobValidationError** ou **BlobOutputAdapterInitializationFailure**.
ID de Correlação | GUID que identifica exclusivamente a execução do trabalho. Todas as entradas do log de execução desde a hora em que o trabalho é iniciado até ele ser interrompido têm o mesmo valor de **ID de Correlação**.

## <a name="next-steps"></a>Próximas etapas

* [Erros de dados de Stream Analytics](./data-errors.md)
* [Referência da linguagem de consulta do Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference)