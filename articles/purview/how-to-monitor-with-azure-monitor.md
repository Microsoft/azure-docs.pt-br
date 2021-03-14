---
title: Como monitorar o Azure alcance
description: Saiba como configurar métricas, alertas e configurações de diagnóstico do Azure alcance usando Azure Monitor.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: 4cb3965d359980856c238cd563ed8b761754660b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667735"
---
# <a name="azure-purview-metrics-in-azure-monitor"></a>Métricas do Azure alcance no Azure Monitor

Este artigo descreve como configurar métricas, alertas e configurações de diagnóstico para o Azure alcance usando o Azure Monitor.

## <a name="monitor-azure-purview"></a>Monitorar alcance do Azure

Os administradores de alcance do Azure podem usar Azure Monitor para acompanhar o estado operacional da conta alcance. As métricas são coletadas para fornecer pontos de dados para você controlar possíveis problemas, solucionar problemas e melhorar a confiabilidade da conta do alcance. As métricas são enviadas ao Azure monitor para eventos que ocorrem no Azure alcance.

## <a name="aggregated-metrics"></a>Métricas agregadas

As métricas podem ser acessadas no portal do Azure para uma conta do alcance. O acesso às métricas é controlado pela atribuição de função da conta alcance. Os usuários precisam fazer parte da função de "leitor de monitoramento" no Azure alcance para ver as métricas. Confira [as permissões da função leitor de monitoramento](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles) para saber mais sobre os níveis de acesso de funções.

A pessoa que criou a conta alcance automaticamente Obtém permissões para exibir as métricas. Se qualquer outra pessoa quiser ver as métricas, adicione-as à função **leitor de monitoramento** , seguindo estas etapas:

### <a name="add-a-user-to-the-monitoring-reader-role"></a>Adicionar um usuário à função leitor de monitoramento

Para adicionar um usuário à função **leitor de monitoramento** , o proprietário da conta alcance ou o proprietário da assinatura pode seguir estas etapas:

1. Vá para a [portal do Azure](https://portal.azure.com) e procure o nome da conta do Azure alcance.

2. Selecione **IAM (Controle de acesso)** .

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/access-iam.png" alt-text="Captura de tela mostrando como acessar IAM.":::

3. Selecione **Adicionar uma atribuição de função**.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-role-assignment.png" alt-text="Captura de tela mostrando como adicionar atribuição de função.":::

4. Selecione o **leitor de monitoramento** de função e defina atribuir acesso ao **usuário, grupo ou entidade de serviço do Azure ad**. E atribua a conta do AAD para acessar as métricas.  

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-monitoring-reader.png" alt-text="Captura de tela mostrando como adicionar a função de leitor de monitoramento.":::

## <a name="metrics-visualization"></a>Visualização de métricas

Os usuários na função **leitor de monitoramento** podem ver as métricas agregadas e os logs de diagnóstico enviados para Azure monitor. As métricas são listadas no portal do Azure para a conta alcance correspondente. Na portal do Azure, selecione a seção métricas para ver a lista de todas as métricas disponíveis.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/purview-metrics.png" alt-text="Captura de tela mostrando a seção de métricas alcance disponíveis." lightbox="./media/how-to-monitor-with-azure-monitor/purview-metrics.png":::

Os usuários do Azure alcance também podem acessar a página de métricas diretamente do centro de gerenciamento da conta do Azure alcance. Selecione Azure Monitor na página principal do alcance Management Center para iniciar o portal do Azure.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png" alt-text="Captura de tela para iniciar as métricas de alcance do centro de gerenciamento." lightbox="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png":::

### <a name="available-metrics"></a>Métricas disponíveis

Para se familiarizar com como usar a seção métrica no portal do Azure antes, leia os dois documentos a seguir. [Introdução ao Gerenciador de métricas](../azure-monitor/essentials/metrics-getting-started.md) e [recursos avançados do Metrics Explorer](../azure-monitor/essentials/metrics-charts.md).

A tabela a seguir contém a lista de métricas disponíveis para explorar no portal do Azure:

| Nome da métrica | Namespace da métrica | Tipo de agregação | Descrição |
| ------------------- | ------------------- | ------------------- | ----------------- |
| Verificação cancelada | Verificação automatizada | Somar <br> Contagem | Agregar as verificações da fonte de dados cancelada com o período de tempo |
| Verificação concluída | Verificação automatizada | Somar <br> Contagem | Agregar as verificações de fonte de dados concluídas ao longo do período de tempo |
| Falha na verificação | Verificação automatizada | Somar <br> Contagem | Agregar as verificações de fonte de dados com falha ao longo do período de tempo |
| Tempo de verificação tomado | Verificação automatizada | Mín <br> Max <br> Somar <br> Méd | Agregar o tempo total gasto pelas verificações no período de tempo |

## <a name="diagnostic-logs-to-azure-storage-account"></a>Logs de diagnóstico para a conta de armazenamento do Azure

Os eventos de telemetria brutos são emitidos para Azure Monitor. Os eventos podem ser registrados em uma conta de armazenamento de cliente escolhida para análise posterior. A exportação de logs é feita por meio das configurações de diagnóstico para a conta alcance no portal do Azure.

Siga as etapas para criar uma configuração de diagnóstico para sua conta do Azure alcance.

1. Crie uma nova configuração de diagnóstico para coletar logs e métricas de plataforma seguindo este artigo: [criar configurações de diagnóstico para enviar logs e métricas de plataforma para destinos diferentes](../azure-monitor/essentials/diagnostic-settings.md). Selecione o destino somente como conta de armazenamento do Azure.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png" alt-text="Captura de tela mostrando a criação do log de diagnóstico." lightbox="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png":::

2. Registre os eventos em uma conta de armazenamento. Uma conta de armazenamento dedicada é recomendada para arquivar os logs de diagnóstico. Seguindo este artigo para [criar uma conta de armazenamento](../storage/common/storage-account-create.md?tabs=azure-portal).

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png" alt-text="Captura de tela mostrando a atribuição da conta de armazenamento para o log de diagnóstico." lightbox="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png":::

Aguarde até 15 minutos para começar a receber logs na conta de armazenamento recém-criada. [Consulte retenção de dados e esquema de logs de recursos na conta de armazenamento do Azure](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage). Depois que os logs de diagnóstico são configurados, os eventos fluem para a conta de armazenamento.

### <a name="scanstatuslogevent"></a>ScanStatusLogEvent

O evento acompanha o ciclo de vida da verificação. Uma operação de verificação segue o progresso por meio de uma sequência de Estados, de em fila, em execução e, finalmente, um estado de terminal de êxito | Com falha | Cancel. Um evento é registrado para cada transição de estado e o esquema do evento terá as propriedades a seguir.

```JSON
{
  "time": "<The UTC time when the event occurred>",
  "properties": {
    "dataSourceName": "<Registered data source friendly name>",
    "dataSourceType": "<Registered data source type>",
    "scanName": "<Scan instance friendly name>",
    "assetsDiscovered": "<If the resultType is succeeded, count of assets discovered in scan run>",
    "assetsClassified": "<If the resultType is succeeded, count of assets classified in scan run>",
    "scanQueueTimeInSeconds": "<If the resultType is succeeded, total seconds the scan instance in queue>",
    "scanTotalRunTimeInSeconds": "<If the resultType is succeeded, total seconds the scan took to run>",
    "runType": "<How the scan is triggered>",
    "errorDetails": "<Scan failure error>",
    "scanResultId": "<Unique GUID for the scan instance>"
  },
  "resourceId": "<The azure resource identifier>",
  "category": "<The diagnostic log category>",
  "operationName": "<The operation that cause the event Possible values for ScanStatusLogEvent category are: 
                    |AdhocScanRun 
                    |TriggeredScanRun 
                    |StatusChangeNotification>",
  "resultType": "Queued – indicates a scan is queued. 
                 Running – indicates a scan entered a running state. 
                 Succeeded – indicates a scan completed successfully. 
                 Failed – indicates a scan failure event. 
                 Cancelled – indicates a scan was cancelled. ",
  "resultSignature": "<Not used for ScanStatusLogEvent category. >",
  "resultDescription": "<This will have an error message if the resultType is Failed. >",
  "durationMs": "<Not used for ScanStatusLogEvent category. >",
  "level": "<The log severity level. Possible values are:
            |Informational
            |Error >",
  "location": "<The location of the Azure Purview account>",
}
```

O log de exemplo para uma instância de evento é mostrado na seção abaixo.

```JSON
{
  "time": "2020-11-24T20:25:13.022860553Z",
  "properties": {
    "dataSourceName": "AzureDataExplorer-swD",
    "dataSourceType": "AzureDataExplorer",
    "scanName": "Scan-Kzw-shoebox-test",
    "assetsDiscovered": "0",
    "assetsClassified": "0",
    "scanQueueTimeInSeconds": "0",
    "scanTotalRunTimeInSeconds": "0",
    "runType": "Manual",
    "errorDetails": "empty_value",
    "scanResultId": "0dc51a72-4156-40e3-8539-b5728394561f"
  },
  "resourceId": "/SUBSCRIPTIONS/111111111111-111-4EB2/RESOURCEGROUPS/FOOBAR-TEST-RG/PROVIDERS/MICROSOFT.PURVIEW/ACCOUNTS/FOOBAR-HEY-TEST-NEW-MANIFEST-EUS",
  "category": "ScanStatusLogEvent",
  "operationName": "TriggeredScanRun",
  "resultType": "Delayed",
  "resultSignature": "empty_value",
  "resultDescription": "empty_value",
  "durationMs": 0,
  "level": "Informational",
  "location": "eastus",
}
```

## <a name="next-steps"></a>Próximas etapas

[Exibir informações de ativos](asset-insights.md)