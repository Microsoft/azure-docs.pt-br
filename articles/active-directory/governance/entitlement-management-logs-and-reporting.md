---
title: Arquivar e relatar com o Azure Monitor — Gerenciamento de direitos do Azure Active Directory
description: Saiba como arquivar logs e criar relatórios com o Azure Monitor no gerenciamento de direitos do Azure Active Directory.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 354805b3e2b538c92ba2345df2bcd93968640068
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764379"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>Arquivar logs e relatórios no gerenciamento de direitos do Azure Active Directory no Azure Monitor

O Azure Active Directory armazena eventos de auditoria por até 30 dias no log de auditoria. No entanto, você pode manter os dados de auditoria por mais tempo do que o período de retenção padrão, descrito em [Por quanto tempo o Azure AD armazena dados de relatórios?](../reports-monitoring/reference-reports-data-retention.md), roteando-os para uma conta de Armazenamento do Azure ou usando o Azure Monitor. Você pode usar pastas de trabalho, consultas personalizadas e relatórios sobre esses dados.


## <a name="configure-azure-ad-to-use-azure-monitor"></a>Configurar o Azure AD para usar o Azure Monitor
Antes de usar as pastas de trabalho do Azure Monitor, você deve configurar o Azure AD para enviar uma cópia dos seus logs de auditoria para o Azure Monitor.

O arquivamento de logs de auditoria do Azure AD exige que você tenha o Azure Monitor em uma assinatura do Azure. Você pode ler mais sobre os pré-requisitos e os custos estimados de uso do Azure Monitor em [logs de atividade do Azure AD no Azure Monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md).

**Função de pré-requisito**: Administrador global

1. Entre no portal do Azure como um Administrador Global. Verifique se você tem acesso ao grupo de recursos que contém o workspace do Azure Monitor.
 
1. Selecione **Azure Active Directory**, clique em **Configurações de diagnóstico** em Monitoramento no menu de navegação à esquerda. Verifique se já existe uma configuração para enviar os logs de auditoria para esse workspace.

1. Se ainda não houver uma configuração, clique em **Adicionar configuração de diagnóstico**. Use as instruções no artigo [Integrar logs do Azure AD com logs do Azure Monitor](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor) para enviar o log de auditoria do Azure AD para o workspace do Azure Monitor.

    ![Painel Configurações de diagnóstico](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. Depois que o log for enviado para o Azure Monitor, selecione **Workspaces do Log Analytics** e selecione o workspace que contém os logs de auditoria do Azure AD.

1. Selecione **Uso e custos estimados** e clique em **Retenção de Dados**. Altere o controle deslizante para o número de dias que você deseja manter os dados para atender aos seus requisitos de auditoria.

    ![Painel Workspaces do Log Analytics](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

1. Posteriormente, para ver o intervalo de datas mantidas em seu workspace, você pode usar a pasta de trabalho *Intervalo de Datas de Log Arquivado*:  
    
    1. Selecione **Azure Active Directory** e clique em **Pastas de trabalho**. 
    
    1. Expanda a seção **Solução de problemas do Azure Active Directory** e clique em **Intervalo de Datas de Log Arquivado**. 


## <a name="view-events-for-an-access-package"></a>Exibir eventos para um pacote de acesso  

Para exibir eventos de um pacote de acesso, você deve ter acesso ao workspace do Azure monitor subjacente (consulte [Gerenciar o acesso a dados de log e workspaces no Azure Monitor](../../azure-monitor/logs/manage-access.md#manage-access-using-azure-permissions) para obter informações) em uma das seguintes funções: 

- Administrador global  
- Administrador de segurança  
- Leitor de segurança  
- Leitor de relatórios  
- Administrador de aplicativos  

Use o procedimento a seguir para exibir os eventos: 

1. No portal do Azure, selecione **Azure Active Directory** e, em seguida, clique em **Pastas de Trabalho**. Se você tiver apenas uma assinatura, pule para a etapa 3. 

1. Se você tiver várias assinaturas, selecione a assinatura que contém o workspace:  

1. Selecione a pasta de trabalho chamada *Atividade do Pacote de Acesso*. 

1. Nessa pasta de trabalho, selecione um intervalo de tempo (altere para **Todos** se não tiver certeza) e selecione uma ID do pacote de acesso na lista suspensa de todos os pacotes de acesso que tinham atividade durante esse intervalo de tempo. Os eventos relacionados ao pacote de acesso que ocorreram durante o intervalo de tempo selecionado serão exibidos.  

    ![Exibir eventos do pacote de acesso](./media/entitlement-management-logs-and-reporting/view-events-access-package.png) 

    Cada linha inclui a hora, a ID do pacote de acesso, o nome da operação, a ID do objeto, o UPN e o nome de exibição do usuário que iniciou a operação.  Detalhes adicionais são incluídos no JSON.   


## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>Criar consultas do Azure Monitor personalizadas usando o portal do Azure
Você pode criar suas próprias consultas em eventos de auditoria do Azure AD, incluindo eventos de gerenciamento de direitos.  

1. No Azure Active Directory do portal do Azure, clique em **Logs** na seção Monitoramento no menu de navegação à esquerda para criar uma nova página de consulta.

1. Seu workspace deve ser mostrado no canto superior esquerdo da página de consulta. Se você tiver vários workspaces do Azure Monitor e o workspace que você estiver usando para armazenar eventos de auditoria do Azure AD não for mostrado, clique em **Selecionar Escopo**. Em seguida, selecione a assinatura e o workspace corretos.

1. Em seguida, na área de texto da consulta, exclua a cadeia de caracteres “pesquisa *” e substitua-a pela seguinte consulta:

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. Em seguida, clique em **Executar**. 

    ![Clique em Executar para iniciar a consulta](./media/entitlement-management-logs-and-reporting/run-query.png)

Por padrão, a tabela mostrará os eventos do log de auditoria para gerenciamento de direitos da última hora. Você pode alterar a configuração de “intervalo de tempo” para exibir eventos mais antigos. No entanto, alterar essa configuração mostrará apenas os eventos que ocorreram após o Azure AD ter sido configurado para enviar eventos para o Azure Monitor.

Se você quiser saber os eventos de auditoria mais antigos e mais recentes mantidos no Azure Monitor, use a seguinte consulta:

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

Para obter mais informações sobre as colunas que são armazenadas para eventos de auditoria no Azure Monitor, consulte [Interpretar o esquema de logs de auditoria do Azure AD no Azure Monitor](../reports-monitoring/reference-azure-monitor-audit-log-schema.md).

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>Criar consultas personalizadas do Azure Monitor usando o Azure PowerShell

Você pode acessar os logs por meio do PowerShell depois de configurar o Azure AD para enviar logs para o Azure Monitor. Em seguida, envie consultas de scripts ou da linha de comando do PowerShell, sem a necessidade de ser um Administrador Global no locatário. 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>Verifique se o usuário ou a entidade de serviço tem a atribuição de função correta

Verifique se você, o usuário ou a entidade de serviço que se autenticará no Azure AD, está na função apropriada do Azure no workspace do Log Analytics. As opções de função são o Leitor do Log Analytics ou o Colaborador do Log Analytics. Se você já estiver em uma dessas funções, pule para [Recuperar a ID do Log Analytics com uma assinatura do Azure](#retrieve-log-analytics-id-with-one-azure-subscription).

Para definir a atribuição de função e criar uma consulta, execute as seguintes etapas:

1. No portal do Azure, localize o [workspace do Log Analytics](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
).

1. Selecione **Controle de Acesso (IAM)** .

1. Clique em **Adicionar** para adicionar uma atribuição de função.

    ![Adicionar uma atribuição de função](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>Instalar o módulo do Azure PowerShell

Depois de ter a atribuição de função apropriada, inicie o PowerShell e [instale o módulo do Azure PowerShell](/powershell/azure/install-az-ps) (se ainda não tiver feito isso), digitando:

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
Agora você está pronto para se autenticar no Azure AD e recuperar a ID do workspace do Log Analytics que você está consultando.

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>Recuperar a ID do Log Analytics com uma assinatura do Azure
Se você tiver apenas uma única assinatura do Azure e um único workspace do Log Analytics, digite o seguinte para autenticar no Azure AD, conecte-se a essa assinatura e recupere esse workspace:
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>Recuperar a ID do Log Analytics com várias assinaturas do Azure

 O [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) opera em uma assinatura por vez. Portanto, se você tiver várias assinaturas do Azure, convém se conectar a uma que tenha o workspace do Log Analytics com os logs do Azure AD. 
 
 Os cmdlets a seguir exibem uma lista de assinaturas e localizam a ID da assinatura que tem o workspace do Log Analytics:
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
Você pode autenticar e associar sua sessão do PowerShell a essa assinatura usando um comando como `Connect-AzAccount –Subscription $subs[0].id`. Para saber mais sobre como autenticar o Azure do PowerShell, inclusive não interativamente, consulte [Entrar com Azure PowerShell](/powershell/azure/authenticate-azureps).

Se você tiver vários workspaces do Log Analytics nessa assinatura, o cmdlet [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) retornará a lista de workspaces. Em seguida, você poderá encontrar aquele que tem os logs do Azure AD. O campo `CustomerId` retornado por esse cmdlet é o mesmo que o valor da “ID do workspace” exibido no portal do Azure na visão geral do workspace do Log Analytics.
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>Enviar a consulta para o workspace do Log Analytics
Por fim, depois que um workspace for identificado, você poderá usar [Invoke-AzOperationalInsightsQuery](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) para enviar uma consulta Kusto para esse workspace. Essas consultas são gravadas na [linguagem de consulta Kusto](/azure/kusto/query/).
 
Por exemplo, você pode recuperar o intervalo de datas dos registros de eventos de auditoria do workspace do Log Analytics, com cmdlets do PowerShell para enviar uma consulta como:
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

Você também pode recuperar eventos de gerenciamento de direitos usando uma consulta como:

```azurepowershell
$bQuery = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>Próximas etapas:
- [Criar relatórios interativos com as pastas de trabalho do Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md)
