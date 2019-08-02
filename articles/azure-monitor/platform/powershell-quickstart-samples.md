---
title: Exemplos de início rápido do PowerShell do Azure Monitor
description: Use o PowerShell para acessar os recursos do Azure Monitor, como o dimensionamento automático, alertas, webhooks e pesquisa de logs de atividade.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 2/14/2018
ms.author: robb
ms.subservice: ''
ms.openlocfilehash: ada62fbfa51604a6b3188c27d5c14da40c8ac116
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66400199"
---
# <a name="azure-monitor-powershell-quick-start-samples"></a>Exemplos de início rápido do PowerShell do Azure Monitor
Este artigo mostra exemplos de comandos do PowerShell que ajudarão você a acessar os recursos do Azure Monitor.

> [!NOTE]
> O Azure Monitor é o novo nome do que era chamado "Azure Insights" até 25 de setembro de 2016. No entanto, os namespaces e, portanto, os comandos, a seguir, ainda contêm a palavra "insights".

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="set-up-powershell"></a>Configurar o PowerShell
Se ainda não tiver feito isso, configure o PowerShell para ser executado no seu computador. Para saber mais, consulte [Como instalar e configurar o PowerShell](/powershell/azure/overview).

## <a name="examples-in-this-article"></a>Exemplos neste artigo
Os exemplos neste artigo ilustram como você pode usar os cmdlets do Azure Monitor. Você também pode ver a lista completa de cmdlets do PowerShell do Azure Monitor em [Cmdlets do Azure Monitor (Insights)](https://docs.microsoft.com/powershell/module/az.applicationinsights).

## <a name="sign-in-and-use-subscriptions"></a>Conectar-se e usar assinaturas
Primeiro, entre em sua assinatura do Azure.

```powershell
Connect-AzAccount
```

Você verá uma tela de entrada. Quando entrar, sua Conta, sua TenantID e a ID da Assinatura padrão serão exibidas. Todos os cmdlets do Azure funcionam no contexto de sua assinatura padrão. Para exibir a lista de assinaturas a que você tem acesso, use o seguinte comando:

```powershell
Get-AzSubscription
```

Para ver o contexto de trabalho (qual assinatura sua comandos são executados em), use o seguinte comando:

```powershell
Get-AzContext
```
Para alterar o contexto de trabalho para uma assinatura diferente, use o comando a seguir:

```powershell
Set-AzContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-activity-log-for-a-subscription"></a>Recuperar o log de atividade para uma assinatura
Use o [Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) cmdlet.  A seguir, temos alguns exemplos comuns. O Log de atividades contém os últimos 90 dias de operações. Usando datas antes neste momento resulta em uma mensagem de erro.  

Veja quais são os de data/hora atual para verificar o que o tempo para usar os comandos a seguir:
```powershell
Get-Date
```

Obter entradas de log dessa data/hora até o momento presente:

```powershell
Get-AzLog -StartTime 2019-03-01T10:30
```

Obter entradas de log em um intervalo de data/hora:

```powershell
Get-AzLog -StartTime 2019-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obter entradas de log de um grupo de recursos específico:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Obter entradas de log de um provedor de recursos específico um intervalo de data/hora:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obter todas as entradas de log com um autor de chamadas específico:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

O comando a seguir recupera os últimos 1000 eventos do log de atividade:

```powershell
Get-AzLog -MaxRecord 10
```

`Get-AzLog` dá suporte a muitos outros parâmetros. Para saber mais, consulte a referência `Get-AzLog` .

> [!NOTE]
> `Get-AzLog` fornece apenas 15 dias de histórico. Usando o **- MaxRecords** parâmetro permite que você consulte os últimos N eventos, além de 15 dias. Para eventos de acesso que ocorreram há mais 15 dias, use a API REST ou o SDK (exemplo em C# usando o SDK). Se você não incluir **StartTime**, o valor padrão será **EndTime** menos uma hora. Se você não incluir **EndTime**, o valor padrão será a hora atual. Todas as horas estão no padrão UTC.
> 
> 

## <a name="retrieve-alerts-history"></a>Recuperar o histórico de alertas
Para ver todos os eventos de alerta, você pode consultar os logs do Azure Resource Manager usando os exemplos a seguir.

```powershell
Get-AzLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Para ver o histórico de uma regra de alerta específica, você pode usar o cmdlet `Get-AzAlertHistory` , passando a ID de recurso da regra de alerta.

```powershell
Get-AzAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

O cmdlet `Get-AzAlertHistory` dá suporte a vários parâmetros. Para saber mais, consulte [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).

## <a name="retrieve-information-on-alert-rules"></a>Recuperar informações sobre regras de alerta
Todos os comandos a seguir atuam em um grupo de recursos denominado "montest".

Exibir todas as propriedades da regra de alerta:

```powershell
Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Recuperar todos os alertas de um grupo de recursos:

```powershell
Get-AzAlertRule -ResourceGroup montest
```

Recuperar todas as regras de alerta definidas para um recurso de destino. Por exemplo, todas as regras de alerta definidas em uma VM.

```powershell
Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzAlertRule` dá suporte a outros parâmetros. Consulte [Get-AlertRule](https://msdn.microsoft.com/library/mt282459.aspx) para obter mais informações.

## <a name="create-metric-alerts"></a>Criar alertas de métricas
Você pode usar o cmdlet `Add-AlertRule` para criar, atualizar ou desabilitar uma regra de alerta.

Você pode criar propriedades de email e webhook usando `New-AzAlertRuleEmail` e `New-AzAlertRuleWebhook`, respectivamente. No cmdlet da Regra de alerta, atribua essas propriedades como ações para a propriedade **Actions** da Regra de alerta.

A tabela a seguir descreve os parâmetros e valores usados para criar um alerta usando uma métrica.

| parâmetro | value |
| --- | --- |
| NOME |simpletestdiskwrite |
| Local desta regra de alerta |East US |
| ResourceGroup |montest |
| TargetResourceId |/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
| MetricName do alerta que é criado |\PhysicalDisk(_Total)\Disk Writes/sec. See the `Get-MetricDefinitions` cmdlet about how to retrieve the exact metric names |
| operator |GreaterThan |
| Valor de limite (contagem/s para esta métrica) |1 |
| WindowSize (formato hh:mm:ss) |00:05:00 |
| agregador (estatística da métrica, que usa a contagem Média, nesse caso) |Average |
| emails personalizados (matriz de cadeia de caracteres) |'foo@example.com','bar@example.com' |
| enviar email a proprietários, colaboradores e leitores |-SendToServiceOwners |

Ação Criar um email

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail myname@company.com
```

Ação Criar um webhook

```powershell
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Criar a regra de alerta na métrica de % de CPU em uma VM clássica

```powershell
Add-AzMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Action $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Recuperar a regra de alerta

```powershell
Get-AzAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

O cmdlet de alerta Add também atualizará a regra se já existir uma regra de alerta para as propriedades determinadas. Para desabilitar uma regra de alerta, inclua o parâmetro **- DisableRule**.

## <a name="get-a-list-of-available-metrics-for-alerts"></a>Obter uma lista das métricas disponíveis para alertas
Você pode usar o cmdlet `Get-AzMetricDefinition` para exibir a lista de todas as métricas para um recurso específico.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id>
```

O exemplo a seguir gera uma tabela com o nome de métrica e sua unidade.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Uma lista completa das opções disponíveis para `Get-AzMetricDefinition` está disponível em [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx).

## <a name="create-and-manage-activity-log-alerts"></a>Criar e gerenciar as alerta do Log de Atividades
Você pode usar o cmdlet `Set-AzActivityLogAlert` para definir um alerta do Log de Atividades. Um alerta de Log de Atividades requer que você primeiro defina as condições como um dicionário de condições e, em seguida, crie um alerta que use essas condições.

```powershell

$condition1 = New-AzActivityLogAlertCondition -Field 'category' -Equal 'Administrative'
$condition2 = New-AzActivityLogAlertCondition -Field 'operationName' -Equal 'Microsoft.Compute/virtualMachines/write'
$additionalWebhookProperties = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
$additionalWebhookProperties.Add('customProperty', 'someValue')
$actionGrp1 = New-AzActionGroup -ActionGroupId '/subscriptions/<subid>/providers/Microsoft.Insights/actiongr1' -WebhookProperty $additionalWebhookProperties
Set-AzActivityLogAlert -Location 'Global' -Name 'alert on VM create' -ResourceGroupName 'myResourceGroup' -Scope '/subscriptions/<subid>' -Action $actionGrp1 -Condition $condition1, $condition2

```

As propriedades de webhook adicionais são opcionais. Você pode obter novamente os conteúdos de um alerta do Log de Atividade usando `Get-AzActivityLogAlert`.

## <a name="create-and-manage-autoscale-settings"></a>Criar e gerenciar configurações de Autoescala
Um recurso (um aplicativo Web, VM, serviço de nuvem ou conjunto de dimensionamento de máquinas virtuais) pode ter apenas uma configuração de autoescala definida para ele.
No entanto, cada configuração de autoescala pode ter vários perfis. Por exemplo, um para um perfil de escala baseada em desempenho e outro para um perfil baseado em agendamento. Cada perfil pode ter várias regras configuradas nele. Para obter mais informações sobre Dimensionamento Automático, confira [Como fazer o dimensionamento automático de um aplicativo](../../cloud-services/cloud-services-how-to-scale-portal.md).

Aqui estão as etapas a serem usadas:

1. Criar regra(s).
2. Crie perfis mapeando as regras que você criou anteriormente para os perfis.
3. Opcional: Crie notificações para dimensionamento automático, configurando propriedades de webhook e email.
4. Crie uma configuração de autoescala com um nome do recurso de destino mapeando os perfis e notificações que você criou nas etapas anteriores.

Os exemplos a seguir mostram como você pode criar uma configuração de autoescala para um conjunto de dimensionamento de máquinas virtuais definido para um sistema operacional Windows usando a métrica de uso da CPU.

Primeiro, crie uma regra para escala horizontal, com um aumento de contagem de instâncias.

```powershell
$rule1 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionValue 1
```        

Em seguida, crie uma regra para a redução horizontal, com uma diminuição da contagem de instâncias.

```powershell
$rule2 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionValue 1
```

Depois, crie um perfil para as regras.

```powershell
$profile1 = New-AzAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Crie uma propriedade de webhook.

```powershell
$webhook_scale = New-AzAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Crie a propriedade de notificação para a configuração de autoescala, incluindo o email e o webhook que você criou anteriormente.

```powershell
$notification1= New-AzAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Por fim, crie a configuração de autoescala para adicionar o perfil que você criou anteriormente. 

```powershell
Add-AzAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Para obter mais informações sobre como gerenciar configurações de Dimensionamento Automático, confira [Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx).

## <a name="autoscale-history"></a>Histórico de Autoescala
O exemplo a seguir mostra como você pode exibir eventos recentes de autoescala e alertas. Use a pesquisa do log de atividades para exibir o histórico de autoescala.

```powershell
Get-AzLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

Você pode usar o cmdlet `Get-AzAutoScaleHistory` para recuperar o histórico de Dimensionamento Automático.

```powershell
Get-AzAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Para obter mais informações, confira [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx).

### <a name="view-details-for-an-autoscale-setting"></a>Exibir os detalhes de uma configuração de autoescala
Você pode usar o cmdlet `Get-Autoscalesetting` para recuperar mais informações sobre a configuração de dimensionamento automático.

O exemplo a seguir mostra detalhes de todas as configurações de autoescala no grupo de recursos "myrg1".

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

O exemplo a seguir mostra detalhes sobre todas as configurações de autoescala do grupo de recursos "myrg1" e, especificamente, a configuração de autoescala chamada "MyScaleVMSSSetting".

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Remover uma configuração de autoescala
Você pode usar o cmdlet `Remove-Autoscalesetting` para excluir uma configuração de dimensionamento automático.

```powershell
Remove-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>Gerenciar perfis de log para logs de atividade
Você pode criar um *perfil de log* e exportar dados de logs de atividade para uma conta de armazenamento e pode configurar retenção de dados para ele. Você também pode transmitir os dados para seu Hub de eventos. No momento esse recurso está em Versão prévia e você só pode criar um perfil de log por assinatura. Você pode usar os cmdlets a seguir com sua assinatura atual para criar e gerenciar perfis de log. Você também pode escolher uma assinatura específica. Embora o PowerShell selecione a assinatura atual por padrão, você pode alterar isso usando `Set-AzContext`. Você pode configurar logs de atividade para encaminhar dados para qualquer conta de armazenamento ou Hub de eventos dentro dessa assinatura. Os dados são gravados como arquivos de blob no formato JSON.

### <a name="get-a-log-profile"></a>Obter um perfil de log
Para buscar os perfis de log existentes, use o cmdlet `Get-AzLogProfile` .

### <a name="add-a-log-profile-without-data-retention"></a>Adicionar um perfil de log sem retenção de dados
```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Remover um perfil de log
```powershell
Remove-AzLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Adicionar um perfil de log com retenção de dados
Você pode especificar a propriedade **-RetentionInDays** com o número de dias, como um inteiro positivo, em que os dados são mantidos.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Adicionar perfil de log com retenção e Hub de eventos
Além de encaminhar seus dados para a conta de armazenamento, você também pode transmiti-los para um Hub de eventos. Nessa versão prévia a configuração da conta de armazenamento é obrigatória, mas a configuração do Hub de eventos é opcional.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Configurar logs de diagnóstico
Muitos serviços do Azure fornecem logs adicionais e telemetria que podem fazer uma ou mais das seguintes opções: 
 - ser configurado para salvar dados em sua conta de Armazenamento do Azure
 - enviar para Hubs de Eventos
 - enviar um espaço de trabalho do Log Analytics. 

A operação só pode ser executada em um nível de recurso. A conta de armazenamento ou hub de eventos deve estar na mesma região que o recurso de destino em que a configuração de diagnóstico está definida.

### <a name="get-diagnostic-setting"></a>Obter configuração de diagnóstico
```powershell
Get-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Desabilitar configuração de diagnóstico

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Habilitar configuração de diagnóstico sem retenção

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Habilitar configuração de diagnóstico com retenção

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Habilitar configuração de diagnóstico com retenção para uma categoria de log específica

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Habilitar configuração de diagnóstico para Hubs de Eventos

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Enable $true
```

Habilitar configuração de diagnóstico para Log Analytics

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -WorkspaceId /subscriptions/s1/resourceGroups/insights-integration/providers/providers/microsoft.operationalinsights/workspaces/myWorkspace -Enabled $true

```

Observe que a propriedade WorkspaceId tem a *ID de recurso* do workspace. É possível obter a ID de recurso do seu espaço de trabalho do Log Analytics usando o seguinte comando:

```powershell
(Get-AzOperationalInsightsWorkspace).ResourceId

```

Esses comandos podem ser combinados para enviar dados a vários destinos.

