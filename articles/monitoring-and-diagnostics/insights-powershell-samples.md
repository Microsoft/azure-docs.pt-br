---
title: "Exemplos de início rápido do PowerShell do Azure Monitor. | Microsoft Docs"
description: "Use o PowerShell para acessar os recursos do Azure Monitor, como o dimensionamento automático, alertas, webhooks e pesquisa de logs de atividade."
author: kamathashwin
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: c0761814-7148-4ab5-8c27-a2c9fa4cfef5
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: ashwink
ms.openlocfilehash: 48f064884c2a6d0a55cc58a44169ed03c62de46d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-monitor-powershell-quick-start-samples"></a>Exemplos de início rápido do PowerShell do Azure Monitor
Este artigo mostra exemplos de comandos do PowerShell que ajudarão você a acessar os recursos do Azure Monitor. O Azure Monitor permite que você dimensione automaticamente Serviços de Nuvem, Máquinas Virtuais e Aplicativos Web e envie notificações de alerta ou chame URLs da Web com base em valores de dados de telemetria configurados.

> [!NOTE]
> O Azure Monitor é o novo nome do que era chamado "Azure Insights" até 25 de setembro de 2016. No entanto, os namespaces e, portanto, os comandos a seguir, ainda contêm os “insights”.
> 
> 

## <a name="set-up-powershell"></a>Configurar o PowerShell
Se ainda não tiver feito isso, configure o PowerShell para ser executado no seu computador. Para saber mais, consulte [Como instalar e configurar o PowerShell](/powershell/azure/overview).

## <a name="examples-in-this-article"></a>Exemplos neste artigo
Os exemplos neste artigo ilustram como você pode usar os cmdlets do Azure Monitor. Você também pode ver a lista completa de cmdlets do PowerShell do Azure Monitor em [Cmdlets do Azure Monitor (Insights)](https://msdn.microsoft.com/library/azure/mt282452#40v=azure.200#41.aspx).

## <a name="sign-in-and-use-subscriptions"></a>Conectar-se e usar assinaturas
Primeiro, entre em sua assinatura do Azure.

```PowerShell
Login-AzureRmAccount
```

Isso exige que você se conecte. Quando fizer isso, você verá sua Conta, sua TenantID e ID da Assinatura padrão. Todos os cmdlets do Azure funcionam no contexto de sua assinatura padrão. Para ver a lista de assinaturas a que você tem acesso, use o seguinte comando.

```PowerShell
Get-AzureRmSubscription
```

Para alterar o contexto de trabalho para uma assinatura diferente, use o comando a seguir.

```PowerShell
Set-AzureRmContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-activity-log-for-a-subscription"></a>Recuperar o log de atividade para uma assinatura
Use o cmdlet `Get-AzureRmLog` .  A seguir, temos alguns exemplos comuns.

Obter entradas de log dessa data/hora até o momento presente:

```PowerShell
Get-AzureRmLog -StartTime 2016-03-01T10:30
```

Obter entradas de log em um intervalo de data/hora:

```PowerShell
Get-AzureRmLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obter entradas de log de um grupo de recursos específico:

```PowerShell
Get-AzureRmLog -ResourceGroup 'myrg1'
```

Obter entradas de log de um provedor de recursos específico um intervalo de data/hora:

```PowerShell
Get-AzureRmLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obter todas as entradas de log com um autor de chamadas específico:

```PowerShell
Get-AzureRmLog -Caller 'myname@company.com'
```

O comando a seguir recupera os últimos 1000 eventos do log de atividade:

```PowerShell
Get-AzureRmLog -MaxEvents 1000
```

`Get-AzureRmLog` dá suporte a muitos outros parâmetros. Para saber mais, consulte a referência `Get-AzureRmLog` .

> [!NOTE]
> `Get-AzureRmLog` fornece apenas 15 dias de histórico. Usar o parâmetro **-MaxEvents** permite consultar os últimos N eventos, além de 15 dias. Para eventos de acesso que ocorreram há mais 15 dias, use a API REST ou o SDK (exemplo em C# usando o SDK). Se você não incluir **StartTime**, o valor padrão será **EndTime** menos uma hora. Se você não incluir **EndTime**, o valor padrão será a hora atual. Todas as horas estão no padrão UTC.
> 
> 

## <a name="retrieve-alerts-history"></a>Recuperar o histórico de alertas
Para ver todos os eventos de alerta, você pode consultar os logs do Azure Resource Manager usando os exemplos a seguir.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Para ver o histórico de uma regra de alerta específica, você pode usar o cmdlet `Get-AzureRmAlertHistory` , passando a ID de recurso da regra de alerta.

```PowerShell
Get-AzureRmAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

O cmdlet `Get-AzureRmAlertHistory` dá suporte a vários parâmetros. Para saber mais, consulte [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).

## <a name="retrieve-information-on-alert-rules"></a>Recuperar informações sobre regras de alerta
Todos os comandos a seguir atuam em um grupo de recursos denominado "montest".

Exibir todas as propriedades da regra de alerta:

```PowerShell
Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Recuperar todos os alertas de um grupo de recursos:

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest
```

Recuperar todas as regras de alerta definidas para um recurso de destino. Por exemplo, todas as regras de alerta definidas em uma VM.

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzureRmAlertRule` dá suporte a outros parâmetros. Consulte [Get-AlertRule](https://msdn.microsoft.com/library/mt282459.aspx) para obter mais informações.

## <a name="create-metric-alerts"></a>Criar alertas de métricas
Você pode usar o cmdlet `Add-AlertRule` para criar, atualizar ou desabilitar uma regra de alerta.

Você pode criar propriedades de email e webhook usando `New-AzureRmAlertRuleEmail` e `New-AzureRmAlertRuleWebhook`, respectivamente. No cmdlet da regra de alerta, atribua como ações para a propriedade **Actions** da regra de alerta.

A tabela a seguir descreve os parâmetros e valores usados para criar um alerta usando uma métrica.

| parâmetro | value |
| --- | --- |
| Nome |simpletestdiskwrite |
| Local desta regra de alerta |Leste dos EUA |
| ResourceGroup |montest |
| TargetResourceId |/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
| MetricName do alerta que é criado |\PhysicalDisk(_Total)\Disk Writes/sec. Consulte o cmdlet `Get-MetricDefinitions` sobre como recuperar os nomes de métrica exatos |
| operator |GreaterThan |
| Valor de limite (contagem/s para esta métrica) |1 |
| WindowSize (formato hh:mm:ss) |00:05:00 |
| agregador (estatística da métrica, que usa a contagem Média, nesse caso) |Média |
| emails personalizados (matriz de cadeia de caracteres) |'foo@example.com','bar@example.com' |
| enviar email a proprietários, colaboradores e leitores |-SendToServiceOwners |

Ação Criar um email

```PowerShell
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Ação Criar um webhook

```PowerShell
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Criar a regra de alerta na métrica de % de CPU em uma VM clássica

```PowerShell
Add-AzureRmMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Recuperar a regra de alerta

```PowerShell
Get-AzureRmAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

O cmdlet de alerta Add também atualizará a regra se já existir uma regra de alerta para as propriedades determinadas. Para desabilitar uma regra de alerta, inclua o parâmetro **- DisableRule**.

## <a name="get-a-list-of-available-metrics-for-alerts"></a>Obter uma lista das métricas disponíveis para alertas
Você pode usar o cmdlet `Get-AzureRmMetricDefinition` para exibir a lista de todas as métricas para um recurso específico.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id>
```

O exemplo a seguir gera uma tabela com o nome de métrica e sua unidade.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Uma lista completa das opções disponíveis para `Get-AzureRmMetricDefinition` está disponível em [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx).

## <a name="create-and-manage-autoscale-settings"></a>Criar e gerenciar configurações de Autoescala
Um recurso, assim como um aplicativo Web, VM, serviço de nuvem ou conjunto de dimensionamento de máquinas virtuais, pode ter apenas uma configuração de autoescala definida para ele.
No entanto, cada configuração de autoescala pode ter vários perfis. Por exemplo, um para um perfil de escala baseada em desempenho e outro para um perfil baseado em agendamento. Cada perfil pode ter várias regras configuradas nele. Para obter mais informações sobre Dimensionamento Automático, confira [Como fazer o dimensionamento automático de um aplicativo](../cloud-services/cloud-services-how-to-scale.md).

Estas são as etapas que usaremos:

1. Criar regra(s).
2. Crie perfis mapeando as regras que você criou anteriormente para os perfis.
3. Opcional: criar notificações de autoescala configurando propriedades de webhook e email.
4. Crie uma configuração de autoescala com um nome do recurso de destino mapeando os perfis e notificações que você criou nas etapas anteriores.

Os exemplos a seguir mostram como você pode criar uma configuração de autoescala para um conjunto de dimensionamento de máquinas virtuais definido para um sistema operacional Windows usando a métrica de uso da CPU.

Primeiro, crie uma regra para expansão, com um aumento de contagem de instâncias.

```PowerShell
$rule1 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionValue 1
```        

Em seguida, crie uma regra para expansão, com uma redução da contagem de instâncias.

```PowerShell
$rule2 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionValue 1
```

Depois, crie um perfil para as regras.

```PowerShell
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Crie uma propriedade de webhook.

```PowerShell
$webhook_scale = New-AzureRmAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Crie a propriedade de notificação para a configuração de autoescala, incluindo o email e o webhook que você criou anteriormente.

```PowerShell
$notification1= New-AzureRmAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Por fim, crie a configuração de autoescala para adicionar o perfil que você criou acima.

```PowerShell
Add-AzureRmAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Para obter mais informações sobre como gerenciar configurações de Dimensionamento Automático, confira [Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx).

## <a name="autoscale-history"></a>Histórico de Autoescala
O exemplo a seguir mostra como você pode exibir eventos recentes de autoescala e alertas. Use a pesquisa do log de atividades para exibir o histórico de autoescala.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

Você pode usar o cmdlet `Get-AzureRmAutoScaleHistory` para recuperar o histórico de Dimensionamento Automático.

```PowerShell
Get-AzureRmAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Para obter mais informações, confira [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx).

### <a name="view-details-for-an-autoscale-setting"></a>Exibir os detalhes de uma configuração de autoescala
Você pode usar o cmdlet `Get-Autoscalesetting` para recuperar mais informações sobre a configuração de dimensionamento automático.

O exemplo a seguir mostra detalhes de todas as configurações de autoescala no grupo de recursos "myrg1".

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

O exemplo a seguir mostra detalhes sobre todas as configurações de autoescala do grupo de recursos "myrg1" e, especificamente, a configuração de autoescala chamada "MyScaleVMSSSetting".

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Remover uma configuração de autoescala
Você pode usar o cmdlet `Remove-Autoscalesetting` para excluir uma configuração de dimensionamento automático.

```PowerShell
Remove-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>Gerenciar perfis de log para logs de atividade
Você pode criar um *perfil de log* e exportar dados de logs de atividade para uma conta de armazenamento e pode configurar retenção de dados para ele. Você também pode transmitir os dados para seu Hub de eventos. Observe que no momento esse recurso está em Preview e você só pode criar um perfil de log por assinatura. Você pode usar os cmdlets a seguir com sua assinatura atual para criar e gerenciar perfis de log. Você também pode escolher uma assinatura específica. Embora o PowerShell selecione a assinatura atual por padrão, você pode alterar isso usando `Set-AzureRmContext`. Você pode configurar logs de atividade para encaminhar dados para qualquer conta de armazenamento ou Hub de eventos dentro dessa assinatura. Os dados são gravados como arquivos de blob no formato JSON.

### <a name="get-a-log-profile"></a>Obter um perfil de log
Para buscar os perfis de log existentes, use o cmdlet `Get-AzureRmLogProfile` .

### <a name="add-a-log-profile-without-data-retention"></a>Adicionar um perfil de log sem retenção de dados
```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Remover um perfil de log
```PowerShell
Remove-AzureRmLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Adicionar um perfil de log com retenção de dados
Você pode especificar a propriedade **-RetentionInDays** com o número de dias, como um inteiro positivo, em que os dados são mantidos.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Adicionar perfil de log com retenção e Hub de eventos
Além de encaminhar seus dados para a conta de armazenamento, você também pode transmiti-los para um Hub de eventos. Observe que nessa versão de teste a configuração da conta de armazenamento é obrigatória, mas a configuração do Hub de eventos é opcional.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Configurar logs de diagnóstico
Muitos serviços do Azure fornecem logs e telemetria adicionais que podem ser configurados para salvar dados em sua conta de Armazenamento do Azure, enviar para Hubs de Eventos e/ou enviar para um espaço de trabalho de Log Analytics do OMS. Essa operação só pode ser executada no nível dos recursos e a conta de armazenamento ou hub de eventos deve estar na mesma região que o recurso de destino em que a configuração de diagnóstico está definida.

### <a name="get-diagnostic-setting"></a>Obter configuração de diagnóstico
```PowerShell
Get-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Desabilitar configuração de diagnóstico

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Habilitar configuração de diagnóstico sem retenção

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Habilitar configuração de diagnóstico com retenção

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Habilitar configuração de diagnóstico com retenção para uma categoria de log específica

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Habilitar configuração de diagnóstico para Hubs de Eventos

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Enable $true
```

Habilitar configuração de diagnóstico para OMS

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -WorkspaceId 76d785fd-d1ce-4f50-8ca3-858fc819ca0f -Enabled $true

```
