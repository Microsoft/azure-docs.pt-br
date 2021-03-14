---
title: Log de diagnósticos do Azure Analysis Services | Microsoft Docs
description: Descreve como configurar o registro em log para monitorar o servidor do Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2bee856adef1208aabbe65ecd5fd11235579bb82
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582696"
---
# <a name="setup-diagnostic-logging"></a>Configurar o log de diagnósticos

Uma parte importante de qualquer solução do Analysis Services é o monitoramento do desempenho de seus servidores. O Azure Analysis Services está integrado ao Azure Monitor. Com os [logs de registro do Azure Monitor](../azure-monitor/essentials/platform-logs-overview.md), é possível monitorar e enviar logs para o [Armazenamento do Microsoft Azure](https://azure.microsoft.com/services/storage/), transmiti-los para [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/) e exportá-los para os [logs do Azure Monitor](../azure-monitor/overview.md).

![Registrar em log os recursos do Armazenamento, do Hubs de Eventos ou do Azure Monitor](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>O que é registrado em log?

Você pode selecionar as categorias **Mecanismo**, **Serviço** e **Métricas**.

### <a name="engine"></a>Mecanismo

Selecionar **Mecanismo** registra todos [xEvents](/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). Não é possível selecionar eventos individuais. 

|Categorias de XEvent |Nome do evento  |
|---------|---------|
|Auditoria de Segurança    |   Auditar logon      |
|Auditoria de Segurança    |   Auditar logoff      |
|Auditoria de Segurança    |   Auditar início e parada de servidor      |
|Relatórios de andamento     |   Início de relatório de andamento      |
|Relatórios de andamento     |   Fim de relatório de andamento      |
|Relatórios de andamento     |   Relatório de andamento atual      |
|Consultas     |  Início de consulta       |
|Consultas     |   Término de consulta      |
|Comandos     |  Início de comando       |
|Comandos     |  Término de comando       |
|Erros e avisos     |   Erro      |
|Descobrir     |   Término de descoberta      |
|Notification     |    Notification     |
|Session     |  Inicialização de sessão       |
|Locks    |  Deadlock       |
|Query Processing     |   Início da consulta VertiPaq SE      |
|Query Processing     |   Final da consulta VertiPaq SE      |
|Query Processing     |   Correspondência de Cache de consulta VertiPaq SE      |
|Query Processing     |   Início de consulta direta      |
|Query Processing     |  Término de consulta direta       |

### <a name="service"></a>Serviço

|Nome da operação  |Ocorre quando  |
|---------|---------|
|ResumeServer     |    Retomar um servidor     |
|SuspendServer    |   Pausar um servidor      |
|DeleteServer     |    Excluir um servidor     |
|RestartServer    |     Usuário reinicia um servidor por meio do SSMS ou PowerShell    |
|GetServerLogFiles    |    Usuário exporta log de servidor por meio do PowerShell     |
|ExportModel     |   Usuário exporta um modelo no portal usando Abrir no Visual Studio     |

### <a name="all-metrics"></a>Todas as métricas

Os logs da categoria de métricas registra as [métricas de servidor](analysis-services-monitor.md#server-metrics) para a tabela AzureMetrics. Se estiver usando a [expansão](analysis-services-scale-out.md) de consulta e precisar separar métricas para cada réplica de leitura, use a tabela AzureDiagnostics, onde **OperationName** é igual a **LogMetric**.

## <a name="setup-diagnostics-logging"></a>Configurar o log de diagnósticos

### <a name="azure-portal"></a>Portal do Azure

1. Em [Portal do Azure](https://portal.azure.com) > Servidor, clique em **Configurações de diagnóstico** na navegação à esquerda e, em seguida, clique em **Ativar diagnóstico**.

    ![Ativar o log de recursos do Azure Cosmos DB no portal do Azure](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. Em **Configurações de diagnóstico**, especifique as opções a seguir: 

    * **Nome**. Insira um nome para os logs a serem criados.

    * **Arquivar em uma conta de armazenamento**. Para usar essa opção, você precisa de uma conta de armazenamento existente à qual se conectar. Consulte [Criar uma conta de armazenamento](../storage/common/storage-account-create.md). Siga as instruções para criar um Gerenciador de Recursos, uma conta de finalidade geral, em seguida, selecione sua conta de armazenamento retornando para esta página no portal. Pode levar alguns minutos para que as contas de armazenamento recém-criadas sejam exibidas no menu suspenso.
    * **Transmitir para um hub de eventos**. Para usar essa opção, é necessário ter um namespace existente do Hub de Eventos e um hub de evento ao qual se conectar. Para saber mais, consulte [Criar um namespace de Hubs de Eventos e um hub de eventos usando o Portal do Azure](../event-hubs/event-hubs-create.md). Em seguida, retorne a esta página no portal para selecionar o namespace e o nome da política do Hub de Eventos.
    * **Enviar para o Azure Monitor (espaço de trabalho do Log Analytics)** . Para usar essa opção, use um workspace existente ou [crie um novo recurso de workspace](../azure-monitor/logs/quick-create-workspace.md) no portal. Para obter mais informações sobre como exibir os logs, confira [Exibir logs no espaço de trabalho do Log Analytics](#view-logs-in-log-analytics-workspace) neste artigo.

    * **Mecanismo**. Selecione esta opção para registrar xEvents. Ao arquivar uma conta de armazenamento, você poderá selecionar o período de retenção para os logs de recurso. Os logs são excluídos automaticamente depois que o período de retenção expira.
    * **Serviço**. Selecione esta opção para registrar eventos de nível de serviço. Ao arquivar em uma conta de armazenamento, poderá selecionar o período de retenção para os logs de recurso. Os logs são excluídos automaticamente depois que o período de retenção expira.
    * **Métricas**. Selecione esta opção para armazenar dados detalhados em [Métricas](analysis-services-monitor.md#server-metrics). Ao arquivar em uma conta de armazenamento, poderá selecionar o período de retenção para os logs de recurso. Os logs são excluídos automaticamente depois que o período de retenção expira.

3. Clique em **Save** (Salvar).

    Se você receber um erro que diz "falha ao atualizar o diagnóstico para o \<workspace name> . A assinatura \<subscription id> não está registrada para usar o Microsoft. insights. " siga as instruções para [Solucionar Problemas de Diagnóstico do Azure](../azure-monitor/essentials/resource-logs.md) para registrar a conta, então repita este procedimento.

    Sempre que você quiser alterar como os seus logs de recursos são salvos, retorne a esta página para modificar as configurações.

### <a name="powershell"></a>PowerShell

Aqui estão os comandos básicos para você começar. Se você desejar obter ajuda passo a passo sobre como configurar o registro em log para uma conta de armazenamento usando o PowerShell, consulte o tutorial mais adiante neste artigo.

Para habilitar as métricas e o log de recursos usando o PowerShell, use os seguintes comandos:

- Para habilitar o armazenamento dos logs de recursos em uma conta de armazenamento, use este comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   A ID da conta de armazenamento é a ID de recurso da conta de armazenamento para a qual os logs serão enviados.

- Para habilitar o streaming dos logs de recursos para um hub de eventos, use este comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   A ID da regra do Barramento de Serviço do Azure é uma cadeia de caracteres com este formato:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Para habilitar o envio dos logs de recursos para um workspace do Log Analytics, use este comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- É possível obter a ID de recurso do espaço de trabalho do Log Analytics usando o seguinte comando:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

Você pode combinar esses parâmetros para permitir várias opções de saída.

### <a name="rest-api"></a>API REST

Saiba como [alterar as configurações de diagnóstico usando a API REST do Azure Monitor](/rest/api/monitor/). 

### <a name="resource-manager-template"></a>Modelo do Resource Manager

Saiba como [habilitar as configurações de diagnóstico na criação de recursos usando um modelo do Resource Manager](../azure-monitor/essentials/resource-manager-diagnostic-settings.md). 

## <a name="manage-your-logs"></a>Gerenciar seus logs

Os logs estão geralmente disponíveis em algumas horas após a configuração do registro em log. Cabe a você gerenciar os logs em sua conta de armazenamento:

* Use os métodos de controle de acesso padrão do Azure para proteger seus logs ao restringir quem pode acessá-los.
* Exclua os logs que você não deseja manter em sua conta de armazenamento.
* Certifique-se de definir um período de retenção para que logs antigos sejam excluídos da sua conta de armazenamento.

## <a name="view-logs-in-log-analytics-workspace"></a>Exibir logs no espaço de trabalho do Log Analytics

Os eventos de métricas e de servidor são integrados com o xEvents no recurso de espaço de trabalho do Log Analytics para uma análise lado a lado. O espaço de trabalho do Log Analytics também pode ser configurado para receber eventos de outros serviços do Azure, fornecendo uma exibição holística dos dados de log de diagnósticos em sua arquitetura.

Para exibir seus dados de diagnóstico, no espaço de trabalho do Log Analytics, abra **Logs** no menu à esquerda.

![Opções de pesquisa de logs no portal do Azure](./media/analysis-services-logging/aas-logging-open-log-search.png)

No construtor de consultas, expanda **LogManagement** > **AzureDiagnostics**. O AzureDiagnostics inclui eventos de Mecanismo e Serviço. Veja que uma consulta é imediatamente criada. O campo EventClass\_s contém nomes de xEvents, que podem parecer familiares se você já usou xEvents para registro em log local. Clique em **EventClass\_s** ou em um dos nomes de eventos e o espaço de trabalho do Log Analytics continuará a criar uma consulta. Certifique-se de salvar as consultas para reutilizar posteriormente.

### <a name="example-queries"></a>Consultas de exemplo

#### <a name="example-1"></a>Exemplo 1

A consulta a seguir retorna as durações de cada evento de fim de consulta/fim de atualização de consulta para um modelo de banco de dados e servidor. Se for escalado horizontalmente, os resultados serão divididos por réplica porque o número da réplica está incluído no ServerName_s. O agrupamento por RootActivityId_g reduz a contagem de linhas recuperadas da API REST do Diagnóstico do Azure e ajuda a ficar dentro dos limites conforme a descrição nos [limites de taxa do Log Analytics](https://dev.loganalytics.io/documentation/Using-the-API/Limits).

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName" and DatabaseName_s =~ "MyDatabaseName" ;
window
| where OperationName has "QueryEnd" or (OperationName has "CommandEnd" and EventSubclass_s == 38)
| where extract(@"([^,]*)", 1,Duration_s, typeof(long)) > 0
| extend DurationMs=extract(@"([^,]*)", 1,Duration_s, typeof(long))
| project  StartTime_t,EndTime_t,ServerName_s,OperationName,RootActivityId_g,TextData_s,DatabaseName_s,ApplicationName_s,Duration_s,EffectiveUsername_s,User_s,EventSubclass_s,DurationMs
| order by StartTime_t asc
```

#### <a name="example-2"></a>Exemplo 2

A consulta a seguir retorna a memória e o consumo de QPU de um servidor. Se for escalado horizontalmente, os resultados serão divididos por réplica porque o número da réplica está incluído no ServerName_s.

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where name_s == "memory_metric" or name_s == "qpu_metric"
| project ServerName_s, TimeGenerated, name_s, value_s
| summarize avg(todecimal(value_s)) by ServerName_s, name_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

#### <a name="example-3"></a>Exemplo 3

A consulta a seguir retorna os contadores de desempenho de leitura de linhas por segundo do Analysis Services de um servidor.

```Kusto
let window =  AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where parse_json(tostring(parse_json(perfobject_s).counters))[0].name == "Rows read/sec" 
| extend Value = tostring(parse_json(tostring(parse_json(perfobject_s).counters))[0].value) 
| project ServerName_s, TimeGenerated, Value
| summarize avg(todecimal(Value)) by ServerName_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

Há centenas de consultas que você pode usar. Para saber mais sobre as consultas, confira [Introdução às consultas de log do Azure Monitor](../azure-monitor/logs/get-started-queries.md).


## <a name="turn-on-logging-by-using-powershell"></a>Ativar o registro em log usando o PowerShell

Neste tutorial rápido, você cria uma conta de armazenamento na mesma assinatura e mesmo grupo de recursos que o servidor do Analysis Services. Então, você usa Set-AzDiagnosticSetting para ativar o log de diagnósticos, enviando a saída para a nova conta de armazenamento.

### <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, você deve ter os seguintes recursos:

* Um servidor existente do Azure Analysis Services. Para obter instruções de como criar um recurso de servidor, consulte [Criar um servidor no Portal do Azure](analysis-services-create-server.md) ou [Criar um servidor do Azure Analysis Services usando o PowerShell](analysis-services-create-powershell.md).

### <a name="aconnect-to-your-subscriptions"></a></a>Conectar-se às suas assinaturas

Inicie uma sessão do PowerShell do Azure e entre em sua conta do Azure com o seguinte comando:  

```powershell
Connect-AzAccount
```

Na janela pop-up do navegador, insira o nome de usuário e a senha da sua conta do Azure. O Azure PowerShell obtém todas as assinaturas que estão associadas a essa conta e, por padrão, usa a primeira.

Se você tiver várias assinaturas, talvez tenha que indicar uma assinatura específica que tenha sido usada para criar o Cofre de Chaves do Azure. Digite o seguinte para ver as assinaturas da sua conta:

```powershell
Get-AzSubscription
```

Em seguida, para especificar a assinatura associada à conta do Azure Analysis Services que está sendo registrada, digite:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Caso tenha várias assinaturas associadas à sua conta, é importante especificar a assinatura.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Criar uma nova conta de armazenamento para seus logs

Você pode usar uma conta de armazenamento existente para seus logs, desde que ela esteja na mesma assinatura que o servidor. Neste tutorial você cria uma nova conta de armazenamento dedicada aos logs do Analysis Services. Para facilitar, você armazenará os detalhes da conta de armazenamento em uma variável chamada **sa**.

Você também usa o mesmo grupo de recursos que aquele que contém o servidor do Analysis Services. Substitua os valores de `awsales_resgroup`, `awsaleslogs` e `West Central US` pelos seus próprios valores:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Identificar a conta de servidor para seus logs

Defina o nome da conta do como uma variável chamada **account**, em que ResourceName é o nome da conta.

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Habilitar o registro em log

Para habilitar o registro em log, use o cmdlet Set-AzDiagnosticSetting junto com as variáveis para a nova conta de armazenamento, a conta do servidor e a categoria. Execute o seguinte comando, definindo o sinalizador **-Enabled** como **$true**:

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

A saída deve ser semelhante ao exemplo a seguir:

```powershell
StorageAccountId            : 
/subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourceGroups/awsales_resgroup/providers/Microsoft.Storage/storageAccounts/awsaleslogs
ServiceBusRuleId            :
EventHubAuthorizationRuleId :
Metrics                    
    TimeGrain       : PT1M
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


Logs                       
    Category        : Engine
    Enabled         : True
    RetentionPolicy
    Enabled : False
    Days    : 0


    Category        : Service
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


WorkspaceId                 :
Id                          : /subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourcegroups/awsales_resgroup/providers/microsoft.analysisservic
es/servers/awsales/providers/microsoft.insights/diagnosticSettings/service
Name                        : service
Type                        :
Location                    :
Tags                        :
```

Essa saída confirma que o registro em log agora está habilitado para o servidor, salvando as informações na conta de armazenamento.

Você também pode definir a política de retenção para os logs, para que logs mais antigos sejam excluídos automaticamente. Por exemplo, defina a política de retenção usando o sinalizador **-RetentionEnabled** como **$true** e defina o parâmetro **-RetentionInDays** como **90**. Os logs com mais de 90 dias serão automaticamente excluídos.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [o log de recursos do Azure Monitor](../azure-monitor/essentials/platform-logs-overview.md).

Consulte [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) na ajuda do PowerShell.