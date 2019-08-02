---
title: Logs de diagnóstico do Azure Stream ao espaço de trabalho do Log Analytics no Azure Monitor
description: Saiba como transmitir logs de diagnóstico do Azure para um espaço de trabalho do Log Analytics no Azure Monitor.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: e8e6276a38f06b5c6ebb24c89f3733b9fd7220f7
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612834"
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics-workspace-in-azure-monitor"></a>Logs de diagnóstico do Azure Stream ao espaço de trabalho do Log Analytics no Azure Monitor

**[Os logs de diagnóstico do Azure](diagnostic-logs-overview.md)**  podem ser transmitidos quase em tempo real para um espaço de trabalho do Log Analytics no Azure Monitor usando o portal, cmdlets do PowerShell ou CLI do Azure.

## <a name="what-you-can-do-with-diagnostics-logs-in-a-log-analytics-workspace"></a>O que você pode fazer com o diagnóstico registra em log em um espaço de trabalho do Log Analytics

O Azure Monitor fornece uma ferramenta de consulta e análise de log flexível que permite que você obtenha informações sobre os dados brutos de log gerados a partir de recursos do Azure. Algumas funcionalidades incluem:

* **Consulta de log** -gravação de consultas avançadas sobre seus dados de log, correlacionar logs de várias fontes e geram gráficos que podem ser fixadas no painel do Azure.
* **Alertas** -detectar quando um ou mais eventos correspondem a uma consulta específica e ser notificados com uma chamada de email ou webhook usando alertas do Azure Monitor.
* **Análise avançada** – aplicar o aprendizado de máquina e algoritmos de correspondência de padrão para identificar possíveis problemas revelados por seus logs.

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics-workspace"></a>Habilitar o streaming de logs de diagnóstico ao espaço de trabalho do Log Analytics

Você pode habilitar programaticamente o streaming de logs de diagnóstico por meio do portal ou usando a [API REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). De qualquer forma, você cria uma configuração de diagnóstico na qual especifica um espaço de trabalho do Log Analytics e as categorias de log e as métricas que deseja enviar para esse espaço de trabalho. Uma **categoria de log** de diagnóstico é um tipo de log que um recurso pode fornecer.

O espaço de trabalho do Log Analytics não precisa estar na mesma assinatura que o recurso que emite os logs, contanto que o usuário que define a configuração tenha acesso RBAC apropriado a ambas as assinaturas.

> [!NOTE]
> Atualmente, não há suporte para o envio da métrica multidimensional por meio das configurações de diagnóstico. As métricas com dimensões são exportadas como métricas dimensionais simples, agregadas nos valores da dimensão.
>
> *Por exemplo*: A métrica “Mensagens de Entrada” em um Hub de Eventos pode ser explorada e mapeada por nível da fila. No entanto, quando exportada por meio das configurações de diagnóstico, a métrica será representada como todas as mensagens de entrada em todas as filas no Hub de Eventos.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>Transmitir logs de diagnóstico usando o portal
1. No portal, navegue para o Azure Monitor e clique em **configurações de diagnóstico** na **configurações** menu.


2. Se desejar filtrar a lista por tipo de recurso ou grupo de recursos, clique no recurso para o qual você deseja definir uma configuração de diagnóstica.

3. Se nenhuma configuração existir no recurso que você selecionou, será solicitada a criação de uma configuração. Clique em “Ativar diagnóstico”.

   ![Adicionar configuração de diagnóstico - nenhuma configuração existente](media/diagnostic-logs-stream-log-store/diagnostic-settings-none.png)

   Se houver configurações existentes no recurso, você verá uma lista de configurações já definidas nesse recurso. Clique em "Adicionar configuração de diagnóstico".

   ![Adicionar configuração de diagnóstico - configurações existentes](media/diagnostic-logs-stream-log-store/diagnostic-settings-multiple.png)

3. Dê um nome à sua configuração e marque a caixa **Enviar para o Log Analytics**, em seguida, selecione um espaço de trabalho do Log Analytics.

   ![Adicionar configuração de diagnóstico - configurações existentes](media/diagnostic-logs-stream-log-store/diagnostic-settings-configure.png)

4. Clique em **Salvar**.

Após alguns instantes, a nova configuração aparece na lista de configurações para esse recurso e os logs de diagnóstico são transmitidos para esse workspace assim que os novos dados de evento são gerados. Pode haver até 15 minutos entre quando um evento é emitido e quando ele for exibido no Log Analytics.

### <a name="via-powershell-cmdlets"></a>Via Cmdlets do PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para habilitar o streaming por meio de [Cmdlets do Azure PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md), use o cmdlet `Set-AzDiagnosticSetting` com estes parâmetros:

```powershell
Set-AzDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

Observe que a propriedade workspaceID usa a ID de recurso completa do Azure do workspace, não a ID/chave do workspace mostrada no portal do Log Analytics.

### <a name="via-azure-cli"></a>Via CLI do Azure

Para habilitar o streaming por meio da [CLI do Azure](../../azure-monitor/platform/cli-samples.md), você pode usar o comando [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create).

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --workspace <log analytics name or object ID> \
    --resource <target resource object ID> \
    --resource-group <log analytics workspace resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

É possível adicionar categorias adicionais ao log de diagnóstico, adicionando dicionários à matriz JSON transmitida como o parâmetro `--logs`.

O argumento `--resource-group` somente será necessário se `--workspace` não for uma ID de objeto.

## <a name="how-do-i-query-the-data-from-a-log-analytics-workspace"></a>Como faço para consultar os dados de espaço de trabalho do Log Analytics?

Na folha de Logs no portal do Azure Monitor, você pode consultar os logs de diagnóstico como parte da solução de gerenciamento de Log na tabela do AzureDiagnostics. Também há [várias soluções de monitoramento para recursos do Azure](../../azure-monitor/insights/solutions.md) podem ser instalados para obter informações imediatas sobre os dados de log que está enviando para o Azure Monitor.

### <a name="examples"></a>Exemplos

```Kusto
// Resources that collect diagnostic logs into this Log Analytics workspace, using Diagnostic Settings
AzureDiagnostics
| distinct _ResourceId
```
```Kusto
// Resource providers collecting diagnostic logs into this Log Analytics worksapce, with log volume per category
AzureDiagnostics
| summarize count() by ResourceProvider, Category
```
```Kusto
// Resource types collecting diagnostic logs into this Log Analytics workspace, with number of resources onboarded
AzureDiagnostics
| summarize ResourcesOnboarded=dcount(_ResourceId) by ResourceType
```
```Kusto
// Operations logged by specific resource provider, in this example - KeyVault
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.KEYVAULT"
| distinct OperationName
```

## <a name="azure-diagnostics-vs-resource-specific"></a>Azure versus de diagnóstico específicas do recurso  
Depois que um destino de análise de Log estiver habilitado em uma configuração de diagnóstico do Azure, há duas maneiras distintas que dados serão exibidos no espaço de trabalho:  
- **O diagnóstico do Azure** -esse é o método herdado usado atualmente pela maioria dos serviços do Azure. Nesse modo, todos os dados de qualquer configuração de diagnóstico apontados para um determinado espaço de trabalho irão para o _AzureDiagnostics_ tabela. 
<br><br>Porque muitos recursos enviar dados para a mesma tabela (_AzureDiagnostics_), o esquema da tabela é o conjunto de superusuários dos esquemas de todos os tipos de dados diferentes que estão sendo coletados. Por exemplo, se você tiver criado as configurações de diagnóstico para a coleção dos seguintes tipos de dados, todos sendo enviados para o mesmo espaço de trabalho:
    - Registros de recurso 1 (com um esquema consiste em colunas A, B e C) de auditoria  
    - Logs de erros de 2 de recurso (com um esquema consiste em colunas, D, E e F)  
    - Logs de fluxo de dados de 3 de recurso (com um esquema consiste em colunas G, H e eu)  

    A tabela do AzureDiagnostics será semelhante ao seguinte, com alguns dados de exemplo:  

    | ResourceProvider | Categoria | O | b | C | D | E | F | G | H | I |
    | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
    | Microsoft.Resource1 | AuditLogs | x1 | y1 | z1 |
    | Microsoft.Resource2 | ErrorLogs | | | | q1 | W1 | e1 |
    | Microsoft.Resource3 | DataFlowLogs | | | | | | | j1 | k1 | l1|
    | Microsoft.Resource2 | ErrorLogs | | | | q2 | w2 | e2 |
    | Microsoft.Resource3 | DataFlowLogs | | | | | | | j3 | k3 | l3|
    | Microsoft.Resource1 | AuditLogs | x5 | y5 | z5 |
    | ... |

- **Específicas do recurso** -nesse modo, as tabelas individuais no espaço de trabalho selecionado são criadas por cada categoria selecionada na configuração de configurações de diagnóstico. Esse método mais recente torna muito mais fácil de localizar exatamente você deseja encontrar por meio de explícita separação de preocupações: uma tabela para cada categoria. Além disso, ele fornece benefícios em seu suporte para tipos dinâmicos. Você já pode ver esse modo para selecionar tipos de recursos do Azure, por exemplo [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) ou [Intune](https://docs.microsoft.com/intune/review-logs-using-azure-monitor) logs. Por fim, esperamos que cada tipo de dados para migrar para o modo de recurso específico. 

    No exemplo acima, isso resultaria em três tabelas que está sendo criadas: 
    - Tabela _AuditLogs_ da seguinte maneira:

        | ResourceProvider | Categoria | O | b | C |
        | -- | -- | -- | -- | -- |
        | Microsoft.Resource1 | AuditLogs | x1 | y1 | z1 |
        | Microsoft.Resource1 | AuditLogs | x5 | y5 | z5 |
        | ... |

    - Tabela _ErrorLogs_ da seguinte maneira:  

        | ResourceProvider | Categoria | D | E | F |
        | -- | -- | -- | -- | -- | 
        | Microsoft.Resource2 | ErrorLogs | q1 | W1 | e1 |
        | Microsoft.Resource2 | ErrorLogs | q2 | w2 | e2 |
        | ... |

    - Tabela _DataFlowLogs_ da seguinte maneira:  

        | ResourceProvider | Categoria | G | H | I |
        | -- | -- | -- | -- | -- | 
        | Microsoft.Resource3 | DataFlowLogs | j1 | k1 | l1|
        | Microsoft.Resource3 | DataFlowLogs | j3 | k3 | l3|
        | ... |

    Outros benefícios de usar o modo de específicos de recursos incluem melhor desempenho em latência de ingestão e consulta vezes, melhor capacidade de descoberta de esquemas e sua estrutura, a capacidade de conceder direitos RBAC em uma tabela específica e muito mais.

### <a name="selecting-azure-diagnostic-vs-resource-specific-mode"></a>Selecionar modo de diagnóstico do Azure vs específicas do recurso
Mais recursos do Azure, você não terá uma escolha se deseja usar o modo de diagnóstico do Azure ou recursos específicos; os dados fluam automaticamente por meio do método que o recurso selecionado usar. Consulte a documentação fornecida pelo recurso que você habilitou para enviar dados ao Log Analytics para obter detalhes sobre qual modo é empregado. 

Conforme mencionado na seção anterior, é definitivamente a meta do Azure Monitor para ter todos os serviços no Azure, use o modo de recurso específico. Para facilitar essa transição e certifique-se de que nenhum dado seja perdido como parte do mesmo, alguns serviços do Azure quando a integração ao Log Analytics fornecerá a você uma seleção de modo:  
   ![Seletor de modo de configurações de diagnóstico](media/diagnostic-logs-stream-log-store/diagnostic-settings-mode-selector.png)

Estamos **fortemente** recomendável que, para evitar potencialmente difícil migrações no futuro, qualquer recém-criados configurações de diagnóstico usam o modo centrado em recursos.  

Para configurações de diagnóstico existentes, uma vez habilitada por um recurso específico do Azure, você poderá alternar retroativamente do diagnóstico do Azure para o modo de recurso específico. Seus dados ingeridos anteriormente continuarão disponíveis na _AzureDiagnostics_ tabela até que as antigas como definido nas suas configurações de retenção no espaço de trabalho, mas nenhum novo dado será enviado à tabela de dados dedicada. Isso significa que, para qualquer consulta que tenham que incluir dados antigos e novos (até que os dados antigos seja totalmente desativado), uma [união](https://docs.microsoft.com/azure/kusto/query/unionoperator) deverão operador em suas consultas para combinar os dois conjuntos de dados.

Consulte para notícias sobre o novo Azure dos serviços de logs de suporte no modo específicas do recurso nas [atualizações do Azure](https://azure.microsoft.com/updates/) blog!

### <a name="known-limitation-column-limit-in-azurediagnostics"></a>Limitação conhecida: limite de coluna em AzureDiagnostics
Há um limite explícito de qualquer tabela de Log do Azure determinada não precisar mais de 500 colunas. Uma vez atingido, quaisquer linhas que contêm dados com qualquer outra coluna fora os primeiros 500 serão removidas em tempo de ingestão. A tabela do AzureDiagnostics é especialmente suscetíveis ser afetado esse limite. Isso geralmente acontece porque uma grande variedade de fontes de dados são enviados para o mesmo espaço de trabalho, ou várias fontes de dados detalhados que estão sendo enviados para o mesmo espaço de trabalho. 

#### <a name="azure-data-factory"></a>Fábrica de dados do Azure  
O Azure Data Factory, devido a um conjunto bastante detalhado de logs, é um recurso que é conhecido por ser especialmente afetados por esse limite. Em particular, para as configurações de diagnóstico configurado antes da específicas do recurso modo foi habilitado ou escolher explicitamente para usar o modo de recurso específicas por motivos de compatibilidade reversa:  
- *Parâmetros do usuário definidos em relação a qualquer atividade em seu pipeline*: haverá uma nova coluna criada para cada parâmetro de usuário nomeado exclusivamente em relação a qualquer atividade. 
- *Atividade de entradas e saídas*: elas variam de atividade para atividade e gerar um grande número de colunas devido à sua natureza detalhada. 
 
Como com as mais amplas propostas de solução alternativa abaixo, é recomendável migrar seus logs para usar o modo de recurso específico assim que possível. Se você não conseguir fazer isso imediatamente, uma alternativa provisória é isolar os logs do ADF em seu próprio espaço de trabalho para minimizar a chance desses logs que afetam outros tipos de log que estão sendo coletados em seus espaços de trabalho. 
 
#### <a name="workarounds"></a>Soluções alternativas
Curto prazo, até que todos os serviços do Azure não estão habilitados no modo de recurso específico, para todos os serviços ainda dar suporte ao modo de recurso específico, é recomendável separar os tipos de dados detalhado publicados por esses serviços em espaços de trabalho separados para reduzir a possibilidade de atingir o limite.  
 
Longo prazo, o diagnóstico do Azure será mudar para todos os serviços do Azure que dão suporte ao modo de recurso específico. Recomendamos a movimentação para esse modo assim que possível para reduzir a possibilidade de que está sendo afetado por essa limitação 500 coluna.  


## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre os Logs de Diagnóstico do Azure](diagnostic-logs-overview.md)

