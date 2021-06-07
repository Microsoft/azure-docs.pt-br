---
title: Integrar com os logs do Azure Monitor
description: Este artigo informa como enviar dados de relatório de Desired State Configuration da State Configuration de Automação do Azure para os logs do Azure Monitor.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0afe349473bcddcbf1ac35136f2991ffe82670c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100576059"
---
# <a name="integrate-with-azure-monitor-logs"></a>Integrar com os logs do Azure Monitor

A State Configuration de Automação do Azure retém os dados de status do nó por 30 dias. Você poderá enviar dados de status do nó para seu workspace do Log Analytics se preferir manter esses dados por um período maior. O status de conformidade é visível no portal do Microsoft Azure, ou com o PowerShell, para nós e recursos individuais de DSC em configurações de nó. 

Os logs do Azure Monitor oferecem maior visibilidade operacional para os dados da Configuração de State Configuration e podem ajudar a tratar de incidentes mais rapidamente. Com os logs do Azure Monitor, você pode:

- Obter informações de conformidade para nós gerenciados e recursos individuais.
- Disparar um email ou alerta com base no status de conformidade.
- Escrever consultas avançadas em seus nós gerenciados.
- Correlacionar o status de conformidade em contas de Automação.
- Usar exibições personalizadas e consultas de pesquisa para visualizar os resultados de runbook, o status do trabalho de runbook e outros principais indicadores ou métricas relacionadas.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para começar a enviar seus relatórios de State Configuration de Automação para logs do Azure Monitor, você precisará:

- Versão de novembro 2016 ou posterior do [Azure PowerShell](/powershell/azure/) ( versão 2.3.0).
- Uma conta de Automação do Azure. Confira mais informações em [Uma introdução à Automação do Azure](automation-intro.md).
- Um espaço de trabalho do Log Analytics com uma oferta de serviço Automação e Controle. Para obter mais informações, confira [Introdução ao Log Analytics no Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md).
- No mínimo, um nó de Configuração do Estado de Automação do Azure. Para saber mais, veja [Máquinas de integração para o gerenciamento pela State Configuration de Automação do Azure](automation-dsc-onboarding.md).
- O módulo [xDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0), versão 2.7.0.0 ou superior. Para obter as etapas de instalação, consulte [Solucionar problemas com a Desired State Configuration da Automação do Azure](./troubleshoot/desired-state-configuration.md).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Configurar a integração com os logs do Azure Monitor

Para começar a importar dados da State Configuration da Automação do Azure para logs do Azure Monitor, conclua as seguintes etapas:

1. Faça logon em sua conta do Azure no PowerShell. Consulte [Entrar com o Azure PowerShell](/powershell/azure/authenticate-azureps).
1. Para obter a ID do recurso da sua conta de Automação, execute o seguinte cmdlet do PowerShell. Se você tiver mais de uma conta de automação, escolha a ID do recurso da conta que deseja configurar.

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Obtenha a ID do recurso do workspace do Log Analytics usando o seguinte cmdlet do PowerShell. Se você tiver mais de um workspace, escolha a ID do recurso para o workspace que deseja configurar.

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Execute o seguinte cmdlet do PowerShell, substituindo `<AutomationResourceId>` e `<WorkspaceResourceId>` pelos valores de `ResourceId` de cada uma das etapas anteriores.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. Se você quiser parar de importar dados da State Configuration da Automação do Azure para os logs do Azure Monitor, execute o seguinte cmdlet do PowerShell.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>Exibir os logs de configuração de estado

Depois de configurar a integração com os logs do Azure Monitor para os dados de State Configuration de automação, você poderá exibi-los selecionando **Logs** na seção **Monitoramento** no painel esquerdo da página de configuração de estado (DSC).

![Logs](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

O painel Pesquisa de Logs é aberto com uma região de consulta com o escopo definido para o recurso de conta da Automação do Azure. Você pode procurar nos logs de State Configuration operações de DSC. Para isso, pesquise em logs do Azure Monitor. Os registros de operações de DSC são armazenados na tabela `AzureDiagnostics`. Por exemplo, para localizar nós que não sejam compatíveis, digite a consulta a seguir.

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```

Detalhes da filtragem:

* Filtre `DscNodeStatusData` para retornar operações para cada nó de State Configuration.
* Filtre `DscResourceStatusData` para retornar as operações de cada recurso de DSC chamado na configuração de nó aplicada a esse recurso. 
* Filtre `DscResourceStatusData` para retornar informações de erro para todos os recursos de DSC que falharem.

Para saber mais sobre como construir consultas de log para localizar dados, consulte [Visão geral das consultas de log no Azure Monitor](../azure-monitor/logs/log-query-overview.md).

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Enviar um email quando uma verificação de conformidade da Configuração do Estado falhar

Uma das principais solicitações de nossos clientes é a capacidade de enviar um email ou uma mensagem de texto quando algo dá errado com uma configuração de DSC.

Para criar uma regra de alerta, comece criando uma pesquisa de log para os registros do relatório de State Configuration que devem invocar o alerta. Clique no botão **Nova Regra de Alerta**  para criar e configurar a regra de alerta.

1. Na página Visão geral do workspace do Log Analytics, clique em **Logs**.
1. Crie uma consulta de pesquisa de logs para o alerta digitando a seguinte pesquisa no campo de consulta:  `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Se você tiver configurado logs de mais de uma Conta de automação ou assinatura para o workspace, também poderá agrupar os alertas por assinatura e por conta de Automação. Derive o nome da conta de Automação do campo `Resource` na pesquisa dos registros do `DscNodeStatusData`.
1. Para abrir a tela **Criar regra**, clique em **Nova regra de alerta** na parte superior da página. 

Para obter mais informações sobre as opções para configurar o alerta, consulte [Criar uma regra de alerta](../azure-monitor/alerts/alerts-metric.md).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Encontrar recursos DSC com falha em todos os nós

Uma vantagem de usar os logs do Azure Monitor é que você pode procurar falhas de verificações em nós. Para localizar todas as instâncias de recursos DSC que falharam:

1. Na página Visão geral do workspace do Log Analytics, clique em **Logs**.
1. Crie uma consulta de pesquisa de log para o alerta digitando a seguinte pesquisa no campo de consulta: `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Exibir status do nó DSC histórico

Para visualizar o histórico de status do nó DSC ao longo do tempo, use esta consulta:

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

Essa consulta exibe um gráfico do status do nó ao longo do tempo.

## <a name="azure-monitor-logs-records"></a>Registros de logs do Azure Monitor

O diagnóstico da Automação do Azure cria duas categorias de registros nos logs do Azure Monitor:

* Dados do status do nó (`DscNodeStatusData`)
* Dados do status do recurso (`DscResourceStatusData`)

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora quando a verificação de conformidade foi executada. |
| OperationName |`DscNodeStatusData`. |
| ResultType |Valor que indica se o nó está em conformidade. |
| NodeName_s |O nome do nó gerenciado. |
| NodeComplianceStatus_s |Valor de status que especifica se o nó é compatível. |
| DscReportStatus |Valor de status que indica se a verificação de conformidade foi executada com êxito. |
| ConfigurationMode | O modo usado para aplicar a configuração ao nó. Os valores possíveis são: <ul><li>`ApplyOnly`: O DSC somente aplica a configuração e nada mais, exceto se uma nova configuração for enviada por push ao nó de destino ou quando uma nova configuração é efetuada por pull de um servidor. Depois da aplicação inicial de uma nova configuração, o DSC não procura descompasso de um estado previamente configurado. O DSC tenta aplicar a configuração até que seja bem-sucedida antes que o valor `ApplyOnly` entre em vigor. </li><li>`ApplyAndMonitor`: Esse é o valor padrão. O LCM aplica as novas configurações. Depois da aplicação inicial de uma nova configuração, se o nó de destino tiver um descompasso do estado desejado, o DSC relatará a discrepância nos logs. O DSC tenta aplicar a configuração até que seja bem-sucedida antes que o valor `ApplyAndMonitor` entre em vigor.</li><li>`ApplyAndAutoCorrect`: o DSC aplica as novas configurações. Depois da aplicação inicial de uma nova configuração, se o nó de destino tiver um descompasso do estado desejado, o DSC relatará a discrepância nos logs e, em seguida, reaplicará a configuração atual.</li></ul> |
| HostName_s | O nome do nó gerenciado. |
| IPAddress | O endereço IPv4 do nó gerenciado. |
| Categoria | `DscNodeStatus`. |
| Recurso | O nome da conta de Automação do Azure. |
| Tenant_g | GUID que identifica o locatário para o chamador. |
| NodeId_g | A GUID que identifica o nó gerenciado. |
| DscReportId_g | A GUID que identifica o relatório. |
| LastSeenTime_t | Data e hora em que o relatório foi exibido pela última vez. |
| ReportStartTime_t | Data e hora em que o relatório foi iniciado. |
| ReportEndTime_t | Data e hora em que o relatório foi concluído. |
| NumberOfResources_d | O número de recursos de DSC chamado na configuração aplicada ao nó. |
| SourceSystem | O sistema de origem que identifica como os logs do Azure Monitor coletaram os dados. Sempre `Azure` para o diagnóstico do Azure. |
| ResourceId |O identificador de recurso da conta de Automação do Azure. |
| ResultDescription | A descrição de recurso para esta operação. |
| SubscriptionId | A ID da assinatura do Azure (GUID) para a conta de Automação. |
| ResourceGroup | O nome do grupo de recursos para a conta de Automação. |
| ResourceProvider | MICROSOFT.AUTOMATION. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId | O GUID que é o identificador de correlação do relatório de conformidade. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora quando a verificação de conformidade foi executada. |
| OperationName |`DscResourceStatusData`.|
| ResultType |Se o recurso está em conformidade. |
| NodeName_s |O nome do nó gerenciado. |
| Categoria | DscNodeStatus. |
| Recurso | O nome da conta de Automação do Azure. |
| Tenant_g | GUID que identifica o locatário para o chamador. |
| NodeId_g |A GUID que identifica o nó gerenciado. |
| DscReportId_g |A GUID que identifica o relatório. |
| DscResourceId_s |O nome da instância do recurso de DSC. |
| DscResourceName_s |O nome do recurso de DSC. |
| DscResourceStatus_s |Se o recurso de DSC está em conformidade. |
| DscModuleName_s |O nome do módulo do PowerShell que contém o recurso de DSC. |
| DscModuleVersion_s |A versão do módulo do PowerShell que contém o recurso de DSC. |
| DscConfigurationName_s |O nome da configuração aplicada ao nó. |
| ErrorCode_s | O código de erro se o recurso tiver falhado. |
| ErrorMessage_s |A mensagem de erro se o recurso tiver falhado. |
| DscResourceDuration_d |O tempo, em segundos, em que o recurso de DSC foi executado. |
| SourceSystem | Como os logs do Azure Monitor coletaram os dados. Sempre `Azure` para o diagnóstico do Azure. |
| ResourceId |O identificador da conta de Automação do Azure. |
| ResultDescription | A descrição para esta operação. |
| SubscriptionId | A ID da assinatura do Azure (GUID) para a conta de Automação. |
| ResourceGroup | O nome do grupo de recursos para a conta de Automação. |
| ResourceProvider | MICROSOFT.AUTOMATION. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId |O GUID que é a ID de correlação do relatório de conformidade. |

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral, consulte [Visão geral da State Configuration da Automação do Azure](automation-dsc-overview.md).
- Para começar, confira [Introdução ao State Configuration da Automação do Azure](automation-dsc-getting-started.md).
- Para saber como compilar configurações DSC para que possam ser atribuídas a nós de destino, confira [Compilar configurações DSC no State Configuration da Automação do Azure](automation-dsc-compile.md).
- Para obter uma referência de cmdlet do PowerShell, confira [Az.Automation](/powershell/module/az.automation).
- Para obter informações sobre preços, consulte [Preço da State Configuration da Automação do Azure](https://azure.microsoft.com/pricing/details/automation/).
- Para ver um exemplo de uso do State Configuration da Automação do Azure em um pipeline de implantação contínua, confira [Configurar a implantação contínua com o Chocolatey](automation-dsc-cd-chocolatey.md).
- Para saber mais sobre como construir consultas de pesquisa diferentes e examinar os logs de State Configuration da Automação do Azure com os logs do Azure Monitor, confira [Pesquisas de logs em logs do Azure Monitor](../azure-monitor/logs/log-query-overview.md).
- Para saber mais sobre os logs do Azure Monitor e as fontes de coleta de dados, confira a [Visão geral sobre a coleta de dados do armazenamento do Azure nos logs do Azure Monitor](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).