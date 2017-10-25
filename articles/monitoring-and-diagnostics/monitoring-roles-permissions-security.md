---
title: "Introdução às funções, permissões e segurança com o Azure Monitor | Microsoft Docs"
description: "Saiba como usar funções e permissões internas do Azure Monitor para restringir o acesso aos recursos de monitoramento."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 2686e53b-72f0-4312-bcd3-3dc1b4a9b912
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: johnkem
ms.openlocfilehash: 31c4fc5b606bf96cec8c508f4a0ff7ecbaeae38a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-roles-permissions-and-security-with-azure-monitor"></a>Introdução às funções, permissões e segurança com o Azure Monitor
Muitas equipes precisam regular estritamente o acesso aos dados e configurações de monitoramento. Por exemplo, se você tiver os membros da equipe que trabalham exclusivamente no monitoramento (engenheiros de suporte, engenheiros de devops) ou se você usar um provedor de serviços gerenciados, você talvez queira conceder-lhes acesso aos dados de monitoramento apenas enquanto restringe a capacidade de criar, modificar ou excluir recursos. Este artigo mostra como aplicar uma função interna de RBAC de monitoramento a um usuário no Azure rapidamente ou criar sua própria função personalizada para um usuário que precise de permissões limitadas de monitoramento. Em seguida, ele aborda considerações de segurança para os recursos relacionados ao Azure Monitor e como você pode limitar o acesso aos dados contidos nos mesmos.

## <a name="built-in-monitoring-roles"></a>Funções internas de monitoramento
As funções internas do Azure Monitor são projetadas para ajudar a limitar o acesso aos recursos em uma assinatura, permitindo que as pessoas responsáveis pelo monitoramento da infra-estrutura obtenham e configurem os dados que precisam. O Azure Monitor fornece duas funções integradas: um Leitor de monitoramento e um Colaborador de monitoramento.

### <a name="monitoring-reader"></a>Leitor de monitoramento
Pessoas atribuídas à função de Leitor de monitoramento podem exibir todos os dados de monitoramento em uma assinatura, mas não podem modificar nenhum recurso nem editar nenhuma configuração relacionados ao monitoramento de recursos. Essa função é apropriada para os usuários em uma organização, como engenheiros de suporte ou de operações, que precisam ser capazes de:

* Exibir painéis de monitoramentos no portal e criar seus próprios painéis de monitoramentos privados.
* Consultar métricas usando a [API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931930.aspx), [os cmdlets do PowerShell](insights-powershell-samples.md) ou a [CLI de plataforma cruzada](insights-cli-samples.md).
* Consultar o Log de atividades usando o portal, a API REST do Azure Monitor, os cmdlets do PowerShell ou a CLI de plataforma cruzada.
* Exibir as [configurações de diagnóstico](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) para um recurso.
* Exibir o [perfil de log](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile) para uma assinatura.
* Exibir as configurações de autoescala.
* Exibir as configurações e a atividade do alerta.
* Acessar os dados do Application Insights e exiba os dados na Análise de AI.
* Pesquisar dados do espaço de trabalho do Log Analytics (OMS), incluindo os dados de uso do espaço de trabalho.
* Exibir grupos de gerenciamento do Log Analytics (OMS).
* Recuperar o esquema de pesquisa do Log Analytics (OMS).
* Listar os pacotes de inteligência do Log Analytics (OMS).
* Recuperar e executar as pesquisas salvas do Log Analytics (OMS).
* Recuperar a configuração de armazenamento do Log Analytics (OMS).

> [!NOTE]
> Essa função não concede acesso de leitura aos dados de log que foram transmitidos para um hub de eventos ou armazenados em uma conta de armazenamento. [Veja abaixo](#security-considerations-for-monitoring-data) para obter informações sobre como configurar o acesso a esses recursos.
> 
> 

### <a name="monitoring-contributor"></a>Colaborador de monitoramento
Pessoas atribuídas à função de Colaborador de monitoramento podem exibir todos os dados de monitoramento em uma assinatura e criar ou modificar as configurações de monitoramento, mas não podem modificar outros recursos. Essa função é um superconjunto da função Leitor de monitoramento e é apropriada para membros de uma equipe de monitoramento ou provedores de serviços gerenciados de uma organização que, além das permissões acima, também precisam ser capazes de:

* Publicra os painéis de monitoramentos como um painel compartilhado.
* Definir as [configurações de diagnóstico](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) para um recurso.*
* Definir o [perfil de log](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile) para uma assinatura.*
* Definir as configurações e a atividade do alerta.
* Criar testes Web e componentes do Application Insights.
* Listar chaves de espaço de trabalho compartilhadas do Log Analytics (OMS).
* Ativar ou desativar os pacotes de inteligência do Log Analytics (OMS).
* Criar e excluir as pesquisas salvas do Log Analytics (OMS).
* Criar e excluir a configuração de armazenamento do Log Analytics (OMS).

*o usuário também deve receber, separadamente, uma permissão de ListKeys no recurso de destino (namespace do hub de eventos ou conta de armazenamento) para definir um perfil de registro ou configuração de diagnóstico.

> [!NOTE]
> Essa função não concede acesso de leitura aos dados de log que foram transmitidos para um hub de eventos ou armazenados em uma conta de armazenamento. [Veja abaixo](#security-considerations-for-monitoring-data) para obter informações sobre como configurar o acesso a esses recursos.
> 
> 

## <a name="monitoring-permissions-and-custom-rbac-roles"></a>Monitoramento de permissões e funções RBAC personalizadas
Se as funções internas acima não atenderem às necessidades exatas de sua equipe, você poderá [criar uma função personalizada de RBAC](../active-directory/role-based-access-control-custom-roles.md) com permissões mais granulares. A seguir estão as operações RBAC do Azure Monitor e suas descrições.

| Operação | Descrição |
| --- | --- |
| Microsoft.Insights/ActionGroups/[Read, Write, Delete] |Grupos de ações de leitura/gravação/exclusão. |
| Microsoft.Insights/ActivityLogAlerts/[Read, Write, Delete] |Alertas de log de atividades de leitura/gravação/exclusão. |
| Microsoft.Insights/AlertRules/[Read, Write, Delete] |Regras de alerta de leitura/gravação/exclusão (alertas de métrica). |
| Microsoft.Insights/AlertRules/Incidents/Read |Listar incidentes (histórico da regra de alerta disparado) para regras de alerta. Isso se aplica somente ao portal. |
| Microsoft.Insights/AutoscaleSettings/[Read, Write, Delete] |Leitura/gravação/exclusão de configurações de dimensionamento automático. |
| Microsoft.Insights/DiagnosticSettings/[Read, Write, Delete] |Leitura/gravação/exclusão de configurações de diagnóstico. |
| Microsoft.Insights/EventCategories/Read |Enumere todas as categorias possíveis no Log de Atividades. Usado pelo Portal do Azure. |
| Microsoft.Insights/eventtypes/digestevents/Read |Essa permissão é necessária para usuários que precisam de acesso aos Logs de atividade por meio do portal. |
| Microsoft.Insights/eventtypes/values/Read |Listar eventos do Log de atividades (eventos de gerenciamento) em um assinatura. Essa permissão é aplicável ao acesso programático e ao portal para o Log de atividades. |
| Microsoft.Insights/ExtendedDiagnosticSettings/[Read, Write, Delete] | Ler/gravar/excluir configurações de diagnóstico para logs de fluxo de rede. |
| Microsoft.Insights/LogDefinitions/Read |Essa permissão é necessária para usuários que precisam de acesso aos Logs de atividade por meio do portal. |
| Microsoft.Insights/LogProfiles/[Read, Write, Delete] |Perfis de log de leitura/gravação/exclusão (Log de Atividades de streaming para hub de eventos ou conta de armazenamento). |
| Microsoft.Insights/MetricAlerts/[Read, Write, Delete] |Ler/gravar/excluir alertas de métrica quase em tempo real (visualização pública). |
| Microsoft.Insights/MetricDefinitions/Read |Ler definições de métricas (lista de tipos de métrica disponíveis para um recurso). |
| Microsoft.Insights/Metrics/Read |Ler as métricas para um recurso. |
| Microsoft.Insights/Register/Action |Registrar o provedor de recursos do Azure Monitor. |


> [!NOTE]
> O acesso a alertas, configurações de diagnóstico e métricas para um recurso requer que o usuário tenha acesso de Leitura ao tipo de recurso e ao escopo do recurso. Criar (“gravar”) uma configuração de diagnóstico ou perfil de log que faz o arquivamento para uma conta de armazenamento ou transmissão para hubs de eventos requer que o usuário também tenha uma permissão ListKeys no recurso de destino.
> 
> 

Por exemplo, usando a tabela acima, você pode criar uma função RBAC personalizada para um "Leitor de Log de atividades" como esta:

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzureRmRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>Considerações de segurança para dados de monitoramento
Dados de monitoramento — especialmente os arquivos de log — podem conter informações confidenciais, como endereços IP ou nomes de usuário. Os dados de monitoramento do Azure vêm em três formas básicas:

1. O Log de atividades, que descreve todas as ações do plano de controle na sua assinatura do Azure.
2. Logs de diagnóstico, que são logs emitidos por um recurso.
3. Métricas, que são emitidas pelos recursos.

Todos esses três tipos de dados podem ser armazenados em uma conta de armazenamento ou transmitidos ao Hub de eventos, que são recursos do Azure para fins gerais. Como esses são recursos de finalidade geral, criar, excluir e acessá-los são operações privilegiadas, normalmente reservadas para o administrador. Sugerimos que você use as seguintes práticas para recursos relacionados ao monitoramento para evitar o uso indevido:

* Use uma conta de armazenamento única e dedicada para os dados de monitoramento. Se você precisar separar dados de monitoramento em várias contas de armazenamento, nunca compartilhe o uso de uma conta de armazenamento entre dados de monitoramento e não de monitoramento, pois isso pode, inadvertidamente, dar a quem precisa apenas de acesso aos dados de monitoramento (por exemplo, uma solução SIEM de terceiros) acesso aos dados que não são de monitoramento.
* Use um namespace único e dedicado de Barramento de Serviço ou Hub de Eventos em todas as configurações de diagnóstico pelo mesmo motivo que o citado acima.
* Limite o acesso às contas de armazenamento ou hubs de eventos relacionados ao monitoramento mantendo os mesmos em um grupo de recursos separado e [use o escopo](../active-directory/role-based-access-control-what-is.md#basics-of-access-management-in-azure) nas suas funções de monitoramento para limitar o acesso somente ao grupo de recursos.
* Não conceda a permissão de ListKeys para contas de armazenamento ou hubs de eventos no escopo de assinatura quando um usuário precisa apenas de acesso aos dados de monitoramento. Em vez disso, conceda essas permissões ao usuário a um escopo de recurso ou grupo de recursos (se você tiver um grupo de recursos de monitoramento dedicado).

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Limitando o acesso a contas de armazenamento relacionados ao monitoramento
Quando um usuário ou aplicativo precisa de acesso aos dados de monitoramento em uma conta de armazenamento, você deve [gerar uma SAS da conta](https://msdn.microsoft.com/library/azure/mt584140.aspx) na conta de armazenamento que contém os dados de monitoramento com acesso somente leitura no nível de serviço para armazenamento de blobs. No PowerShell, isso pode ser semelhante a:

```powershell
$context = New-AzureStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzureStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

Você pode fornecer o token para a entidade que precisa ler da conta de armazenamento e ele pode listar e ler todos os blobs na conta de armazenamento.

Como alternativa, se você precisar controlar essa permissão com o RBAC, você pode conceder a essa entidade a permissão Microsoft.Storage/storageAccounts/listkeys/action nessa conta de armazenamento específica. Isso é necessário para os usuários que precisam ser capazes de definir uma configuração de diagnóstico ou de perfil de log para arquivar em uma conta de armazenamento. Por exemplo, você pode criar a seguinte função RBAC personalizada para um usuário ou aplicativo que precisa apenas da leitura de uma conta de armazenamento:

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzureRmRoleDefinition -Role $role 
```

> [!WARNING]
> A permissão de ListKeys permite que o usuário liste as chaves primárias e secundárias da conta de armazenamento. Essas chaves concedem ao usuário todas as permissões assinadas (ler, gravar, criar blobs, excluir blobs, etc.) em todos os serviços assinados (blob, fila, tabela, arquivo) na conta de armazenamento. Recomendamos usar uma SAS da conta descrita acima, quando possível.
> 
> 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Limitando o acesso aos hubs de eventos relacionados ao monitoramento
Um padrão semelhante pode ser seguido com hubs de eventos, mas primeiro você precisa criar uma regra de autorização de escuta dedicada. Se você deseja conceder acesso a um aplicativo que precisa apenas escutar os hubs de eventos relacionados ao monitoramento, faça o seguinte:

1. Crie uma política de acesso compartilhado no(s) hubs de eventos que foram criados para transmitir dados de monitoramento com somente declarações de escuta. Isso pode ser feito no portal. Por exemplo, você pode chamá-la de "monitoringReadOnly." Se possível, dê essa chave diretamente ao consumidor e ignore a próxima etapa.
2. Se o consumidor precisa ser capaz de obter o chave ad hoc, conceda ao usuário a ação ListKeys para o hub de eventos. Isso é necessário também para os usuários que precisam ser capazes de definir uma configuração de diagnóstico ou de perfil de log para transmissão para hubs de eventos. Por exemplo, você pode criar uma regra RBAC:
   
   ```powershell
   $role = Get-AzureRmRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzureRmRoleDefinition -Role $role 
   ```

## <a name="next-steps"></a>Próximas etapas
* [Leia sobre RBAC e permissões no Gerenciador de Recursos](../active-directory/role-based-access-control-what-is.md)
* [Leia a visão geral do monitoramento no Azure](monitoring-overview.md)

