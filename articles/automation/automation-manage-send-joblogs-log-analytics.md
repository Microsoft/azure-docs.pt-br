---
title: Encaminhar os dados de trabalho da Automação do Azure para os logs do Azure Monitor
description: Este artigo mostra como enviar status de trabalho e fluxos de trabalho de runbook para logs do Azure Monitor.
services: automation
ms.subservice: process-automation
ms.date: 09/02/2020
ms.topic: conceptual
ms.openlocfilehash: 4199c5576662eee1dd6cedc388440a71e21f8b74
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100581205"
---
# <a name="forward-azure-automation-job-data-to-azure-monitor-logs"></a>Encaminhar os dados de trabalho da Automação do Azure para os logs do Azure Monitor

A Automação do Azure pode enviar fluxos de trabalho e status de trabalho de runbook para seu workspace do Log Analytics. Esse processo não envolve a vinculação de workspace e é completamente independente. Os logs e fluxos de trabalho podem ser vistos no portal do Azure ou com o PowerShell, no caso de trabalhos individuais, e isso permite a você fazer investigações simples. Agora, com os logs do Azure Monitor, você pode:

* Obter informações sobre o status de seus trabalhos de Automação.
* Disparar um email ou um alerta com base no status de trabalho de runbook (por exemplo, com falha ou suspenso).
* Escrever consultas avançadas em seus fluxos de trabalho.
* Correlacionar trabalhos em Contas de automação.
* Usar exibições personalizadas e consultas de pesquisa para visualizar os resultados do runbook, o status do trabalho de runbook e outros indicadores ou métricas principais relacionados.

## <a name="prerequisites"></a>Pré-requisitos

Para começar a enviar seus logs de Automação para os logs do Azure Monitor, você precisa:

* Da última versão do [Azure PowerShell](/powershell/azure/).

* Um espaço de trabalho Log Analytics e sua ID de recurso. Para obter mais informações, confira [Introdução aos logs do Azure Monitor](../azure-monitor/overview.md).

* A ID de recurso da sua conta de automação do Azure.

## <a name="how-to-find-resource-ids"></a>Como localizar IDs de recurso

1. Use o seguinte comando para localizar a ID de recurso da sua conta de Automação do Azure:

    ```powershell-interactive
    # Find the ResourceId for the Automation account
    Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
    ```

2. Copie o valor de **ResourceId**.

3. Use o comando a seguir para localizar a ID de recurso do seu espaço de trabalho do Log Analytics:

    ```powershell-interactive
    # Find the ResourceId for the Log Analytics workspace
    Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
    ```

4. Copie o valor de **ResourceId**.

Para retornar os resultados de um grupo de recursos específico, inclua o `-ResourceGroupName` parâmetro. Para obter mais informações, consulte [Get-AzResource](/powershell/module/az.resources/get-azresource).

Se você tiver mais de uma conta de Automação ou workspace na saída dos comandos anteriores, poderá encontrar o nome e outras propriedades relacionadas que fazem parte da ID do recurso completa da sua conta de Automação executando o seguinte:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Na portal do Azure, selecione sua conta de Automação na página **Contas de Automação**.
1. Na página da conta de Automação selecionada, em **Configurações da Conta**, selecione **Propriedades**.
1. Na página **Propriedades**, observe os detalhes mostrados abaixo.

    ![Propriedades da Conta de Automação](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="configure-diagnostic-settings"></a>Definir as configurações de diagnóstico

As configurações de diagnóstico de automação oferecem suporte ao encaminhamento dos seguintes logs de plataforma e dados de métrica:

* JobLogs
* JobStreams
* DSCNodeStatus
* Métricas-total de trabalhos, execuções de máquina de implantação de atualização total, total de execuções de implantação de atualização

Para começar a enviar seus logs de automação para Azure Monitor logs, examine [criar configurações de diagnóstico](../azure-monitor/essentials/diagnostic-settings.md) para entender o recurso e os métodos disponíveis para definir as configurações de diagnóstico para enviar logs da plataforma.

## <a name="azure-monitor-log-records"></a>Registros de log do Azure Monitor

O diagnóstico da Automação do Azure cria dois tipos de registros em logs do Azure Monitor, marcados como `AzureDiagnostics`. As tabelas nas próximas seções são exemplos de registros que a Automação do Azure gera e os tipos de dados que aparecem nos resultados da pesquisa de logs.

### <a name="job-logs"></a>Logs de trabalho

| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora da execução do trabalho de runbook. |
| RunbookName_s |O nome do runbook. |
| Caller_s |O chamador que iniciou a operação. Os valores possíveis são um endereço de email ou o sistema para trabalhos agendados. |
| Tenant_g | GUID que identifica o locatário do chamador. |
| JobId_g |A GUID que identifica o trabalho do runbook. |
| ResultType |O status do trabalho de runbook. Os valores possíveis são:<br>- Novo<br>– Criado<br>- Iniciado<br>- Parado<br>- Suspenso<br>- Com falha<br>– Concluído |
| Categoria | Classificação do tipo de dados. Para a Automação, o valor é JobLogs. |
| OperationName | O tipo de operação executada no Azure. Para a Automação, o valor é Job. |
| Recurso | O nome da conta de Automação |
| SourceSystem | O sistema que os logs do Azure Monitor usam para coletar os dados. O valor é sempre Azure para o diagnóstico do Azure. |
| ResultDescription |O estado do resultado do trabalho de runbook. Os valores possíveis são:<br>- O trabalho foi iniciado<br>- O trabalho falhou<br>- Trabalho Concluído |
| CorrelationId |O GUID de correlação do trabalho de runbook. |
| ResourceId |A ID de recurso da conta de Automação do Azure do runbook. |
| SubscriptionId | O GUID da assinatura do Azure para a conta de Automação. |
| ResourceGroup | O nome do grupo de recursos para a conta de Automação. |
| ResourceProvider | O provedor de recursos. O valor é MICROSOFT.AUTOMATION. |
| ResourceType | O tipo de recurso. O valor é AUTOMATIONACCOUNTS. |

### <a name="job-streams"></a>Transmissões de trabalho
| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora da execução do trabalho de runbook. |
| RunbookName_s |O nome do runbook. |
| Caller_s |O chamador que iniciou a operação. Os valores possíveis são um endereço de email ou o sistema para trabalhos agendados. |
| StreamType_s |O tipo de fluxo de trabalho. Os valores possíveis são:<br>\- Andamento<br>- Saída<br>- Aviso<br>- Erro<br>- Depurar<br>- Detalhado |
| Tenant_g | GUID que identifica o locatário do chamador. |
| JobId_g |A GUID que identifica o trabalho do runbook. |
| ResultType |O status do trabalho de runbook. Os valores possíveis são:<br>- em andamento |
| Categoria | Classificação do tipo de dados. Para a Automação, o valor é JobStreams. |
| OperationName | O tipo de operação executada no Azure. Para a Automação, o valor é Job. |
| Recurso | O nome da conta de Automação. |
| SourceSystem | O sistema que os logs do Azure Monitor usam para coletar os dados. O valor é sempre Azure para o diagnóstico do Azure. |
| ResultDescription |Descrição que inclui o fluxo de saída do runbook. |
| CorrelationId |O GUID de correlação do trabalho de runbook. |
| ResourceId |A ID de recurso da conta de Automação do Azure do runbook. |
| SubscriptionId | O GUID da assinatura do Azure para a conta de Automação. |
| ResourceGroup | O nome do grupo de recursos para a conta de Automação. |
| ResourceProvider | O provedor de recursos. O valor é MICROSOFT.AUTOMATION. |
| ResourceType | O tipo de recurso. O valor é AUTOMATIONACCOUNTS. |

## <a name="view-automation-logs-in-azure-monitor-logs"></a>Exibir logs de Automação em logs do Azure Monitor

Agora que você começou a enviar seus fluxos de trabalho de automação e logs para Azure Monitor logs, vamos ver o que você pode fazer com esses logs dentro de logs de Azure Monitor.

Para ver os logs, execute a seguinte consulta: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Enviar um email quando um trabalho de runbook falhar ou for suspenso

As etapas a seguir mostram como configurar alertas no Azure Monitor para notificá-lo quando algo der errado com um trabalho de runbook.

Para criar uma regra de alerta, comece criando uma pesquisa de logs para os registros de trabalho de runbook que devem invocar o alerta. Clique no botão **Alerta** para criar e configurar a regra de alerta.

1. Na página Visão geral do workspace do Log Analytics, clique em **Exibir logs**.

2. Crie uma consulta de pesquisa de logs para seu alerta digitando a seguinte pesquisa no campo de consulta: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>Você também pode agrupar pelo nome do runbook usando: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Se você configurou logs de mais de uma Conta de automação ou assinatura para o workspace, também poderá agrupar os alertas por assinatura e por Conta de automação. O nome da conta de Automação pode ser encontrado no campo `Resource` na pesquisa de `JobLogs`.

3. Para abrir a tela **Criar regra**, clique em **Nova Regra de Alerta** na parte superior da página. Para obter mais informações sobre as opções para configurar o alerta, consulte [ Logar alertas no Azure ](../azure-monitor/alerts/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Localizar todos os trabalhos que foram concluídos com erros

Além de alertas de falhas, você pode descobrir quando um trabalho de runbook tem um erro não fatal. Nesses casos, o PowerShell produz um fluxo de erro, mas os erros de não encerramento não fazem com que seu trabalho seja suspenso ou falhe.

1. No workspace do Log Analytics, clique em **Logs**.

2. No campo de consulta, digite `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`.

3. Clique no botão **Pesquisar**.

### <a name="view-job-streams-for-a-job"></a>Exibir fluxos de trabalho para um trabalho

Ao depurar um trabalho, também pode ser conveniente examinar os fluxos de trabalho. A consulta a seguir mostra todos os fluxos de um único trabalho com GUID `2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0` :

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0"
| sort by TimeGenerated asc
| project ResultDescription
```

### <a name="view-historical-job-status"></a>Exibir o status do histórico de trabalho

Por fim, você pode visualizar o histórico de trabalhos ao longo do tempo. Você pode usar essa consulta para pesquisar o status dos trabalhos ao longo do tempo.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started"
| summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)
```

![Gráfico de status de trabalho histórico do Log Analytics](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)

### <a name="filter-job-status-output-converted-into-a-json-object"></a>Filtrar saída de status do trabalho convertido em um objeto JSON

Recentemente, alteramos o comportamento de como os dados de log de automação são gravados `AzureDiagnostics` na tabela no serviço de log Analytics, em que ele não divide as propriedades JSON em campos separados. Se você tiver configurado seu runbook para formatar objetos no fluxo de saída no formato JSON como colunas separadas, será necessário reconfigurar suas consultas para analisar esse campo para um objeto JSON a fim de acessar essas propriedades. Isso é feito usando [parseJSON](/azure/data-explorer/kusto/query/samples?pivots=#parsejson) para acessar um elemento JSON específico em um caminho conhecido.

Por exemplo, um runbook formata a propriedade *ResultDescription* no fluxo de saída no formato JSON com vários campos. Para procurar o status de seus trabalhos que estão em um estado de falha, conforme especificado em um campo chamado **status**, use esta consulta de exemplo para pesquisar o *ResultDescription* com um status de **falha**:

```kusto
AzureDiagnostics
| where Category == 'JobStreams'
| extend jsonResourceDescription = parse_json(ResultDescription)
| where jsonResourceDescription.Status == 'Failed'
```

![Log Analytics formato JSON do fluxo de trabalho histórico](media/automation-manage-send-joblogs-log-analytics/job-status-format-json.png)

## <a name="next-steps"></a>Próximas etapas

* Para saber como construir consultas de pesquisa e examinar os logs de trabalho de Automação com os logs do Azure Monitor, confira [Pesquisas de logs em logs do Azure Monitor](../azure-monitor/logs/log-query-overview.md).
* Para entender como criar e recuperar mensagens de erro e de saída de runbooks, confira [Monitorar a saída do runbook](automation-runbook-output-and-messages.md).
* Para saber mais sobre a execução de runbooks, como monitorar trabalhos de runbook e outros detalhes técnicos, confira [Execução de runbook na Automação do Azure](automation-runbook-execution.md).
* Para saber mais sobre os logs do Azure Monitor e as fontes de coleta de dados, confira a [Visão geral sobre a coleta de dados do armazenamento do Azure nos logs do Azure Monitor](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).
* Para obter ajuda na solução de problemas do Log Analytics, confira [Solucionar problemas se o Log Analytics não está mais coletando dados](../azure-monitor/logs/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).