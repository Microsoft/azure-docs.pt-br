---
title: Consultar logs de Gerenciamento de Atualizações da Automação do Azure
description: Este artigo informa como consultar os logs para Gerenciamento de Atualizações em seu workspace do Log Analytics.
services: automation
ms.subservice: update-management
ms.date: 09/24/2020
ms.topic: conceptual
ms.openlocfilehash: 833e2f7808b4b8efa210bc6a903ed30fe9ac53e0
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92221925"
---
# <a name="query-update-management-logs"></a>Consultar os logs do Gerenciamento de Atualizações

Além dos detalhes fornecidos durante a implantação do Gerenciamento de Atualizações, você pode pesquisar os logs armazenados em seu workspace do Log Analytics. Para pesquisar os logs da conta de Automação do Azure, selecione **Gerenciamento de Atualizações** e abra o workspace do Log Analytics associado à implantação.

Você também pode personalizar as consultas do log ou usá-las de clientes diferentes. Confira [Documentação da API de pesquisa do Log Analytics](https://dev.loganalytics.io/).

## <a name="query-update-records"></a>Consultar registros de atualização

O Gerenciamento de Atualizações coleta registros para VMs do Windows e do Linux e os tipos de dados que aparecem nos resultados da pesquisa do log. As seções a seguir descrevem esses registros.

### <a name="query-required-updates"></a>Consultar atualizações necessárias

É criado um registro com um tipo `RequiredUpdate` que representa as atualizações exigidas por um computador. Esses registros têm as propriedades descritas na tabela a seguir:

| Propriedade | Descrição |
|----------|-------------|
| Computador | Nome de domínio totalmente qualificado do computador de relatórios. |
| KBID | ID do artigo da base de dados de conhecimento para o Windows Update. |
| ManagementGroupName | Nome do grupo de gerenciamento do Operations Manager ou do workspace do Log Analytics. |
| Produto | Produtos para aos quais se aplica a atualização. |
| PublishDate | A data em que a atualização está pronta para ser baixada e instalada pelo Windows Update. |
| Servidor | | 
| SourceHealthServiceId | Identificador exclusivo que representa a ID de agente do Windows do Log Analytics. |
| SourceSystem | *OperationsManager* |
| TenantId | Identificador exclusivo que representa a instância da organização do Azure Active Directory. |
| TimeGenerated | Data e hora de criação do registro. |
| Type | *Atualização* |
| UpdateClassification | Indica o tipo de atualizações que podem ser aplicadas. Para Windows:<br> *Atualizações críticas*<br> *Atualizações de segurança*<br> *Pacotes cumulativos de atualização*<br> *Feature packs*<br> *Service packs*<br> *Atualizações de definição*<br> *Ferramentas*<br> *Atualizações*. Para Linux:<br> *Atualizações críticas e de segurança*<br> *Outros* |
| UpdateSeverity | Classificação de severidade da vulnerabilidade. Os valores são:<br> *Crítico*<br> *Importante*<br> *Moderado*<br> *Baixo* |
| UpdateTitle | O título da atualização.|

### <a name="query-update-record"></a>Consultar registros de atualização

É criado um registro do tipo `Update` que representa as atualizações disponíveis e o status da instalação em um computador. Esses registros têm as propriedades descritas na tabela a seguir:

| Propriedade | Descrição |
|----------|-------------|
| ApprovalSource | Aplica-se exclusivamente ao sistema operacional Windows. Origem da aprovação do registro. O valor é Microsoft Update. |
| Aprovado | Se o registro for aprovado, True. Caso contrário, False. |
| classificação | Classificação da aprovação. O valor é Updates. |
| Computador | Nome de domínio totalmente qualificado do computador de relatórios. |
| ComputerEnvironment | Ambiente. Os valores possíveis são Azure ou Non-Azure. |
| MSRCBulletinID | Número de ID do boletim de segurança. |
| MSRCSeverity | Classificação de severidade da vulnerabilidade. Os valores são:<br> Crítico<br> Importante<br> Moderado<br> Baixo |  
| KBID | ID do artigo da base de dados de conhecimento para o Windows Update. |
| ManagementGroupName | Nome do grupo de gerenciamento do Operations Manager ou do workspace do Log Analytics. |
| UpdateID | Identificador exclusivo da atualização de software. |
| RevisionNumber | O número de uma revisão específica de uma atualização. |
| Opcional | Se o registro for opcional, True. Caso contrário, False. |
| RebootBehavior | O comportamento de reinicialização após instalar/desinstalar uma atualização. |
| _ResourceId | Identificador exclusivo do recurso associado ao registro. |
| Type | Tipo de registro. O valor é Update. |
| VMUUID | Identificador exclusivo da máquina virtual. |
| MG | Identificador exclusivo do grupo de gerenciamento ou do workspace do Log Analytics. |
| TenantId | Identificador exclusivo que representa a instância da organização do Azure Active Directory. |
| SourceSystem | O sistema de origem do registro. O valor é `OperationsManager`. |
| TimeGenerated | Data e hora da criação do registro. |
| SourceComputerId | Identificador exclusivo que representa o computador de origem. |
| Title | O título da atualização. |
| PublishedDate (UTC) | A data em que a atualização está pronta para ser baixada e instalada pelo Windows Update.  |
| UpdateState | O estado atual da atualização. |
| Produto | Produtos aos quais a atualização se aplica. |
| SubscriptionId | Identificador exclusivo da assinatura do Azure. |
| ResourceGroup | Nome do grupo de recursos ao qual pertence o recurso. |
| ResourceProvider | O provedor de recursos. |
| Recurso | Nome do recurso. |
| ResourceType | O tipo de recurso. |

### <a name="query-update-agent-record"></a>Consultar registro do agente de atualização

É criado um registro do tipo `UpdateAgent` que fornece detalhes do agente de atualização no computador. Esses registros têm as propriedades descritas na tabela a seguir:

| Propriedade | Descrição |
|----------|-------------|
| AgeofOldestMissingRequiredUpdate | |
| AutomaticUpdateEnabled | |
| Computador | Nome de domínio totalmente qualificado do computador de relatórios. |
| DaySinceLastUpdateBucket | |
| ManagementGroupName | Nome do grupo de gerenciamento do Operations Manager ou do workspace do Log Analytics. |
| OSVersion | A versão do sistema operacional. |
| Servidor | |
| SourceHealthServiceId | Identificador exclusivo que representa a ID de agente do Windows do Log Analytics. |
| SourceSystem | O sistema de origem do registro. O valor é `OperationsManager`. |
| TenantId | Identificador exclusivo que representa a instância da organização do Azure Active Directory. |
| TimeGenerated | Data e hora da criação do registro. |
| Type | Tipo de registro. O valor é Update. |
| WindowsUpdateAgentVersion | Versão do agente do Windows Update. |
| WSUSServer | Erros se o agente de Windows Update tiver um problema, para auxiliar na solução de problemas. |

### <a name="query-update-deployment-status-record"></a>Consultar registro de status da implantação da atualização

É criado um registro do tipo `UpdateRunProgress` que fornece o status da implantação da atualização agendada pelo computador. Esses registros têm as propriedades descritas na tabela a seguir:

| Propriedade | Descrição |
|----------|-------------|
| Computador | Nome de domínio totalmente qualificado do computador de relatórios. |
| ComputerEnvironment | Ambiente. Os valores são Azure ou Non-Azure. |
| CorrelationId | Identificador exclusivo da execução do trabalho de runbook para a atualização. |
| EndTime | A hora em que o processo de sincronização terminou. *Esta propriedade não é usada no momento. Confira TimeGenerated.* |
| ErrorResult | Código de erro do Windows Update gerado quando uma atualização não é instalada. |
| InstallationStatus | Os estados de instalação possíveis de uma atualização no computador cliente:<br> `NotStarted` - o trabalho ainda não foi disparado.<br> `FailedToStart` - não é possível iniciar o trabalho no computador.<br> `Failed` - trabalho iniciado, mas falhou com uma exceção.<br> `InProgress` - trabalho em andamento.<br> `MaintenanceWindowExceeded` - se a execução não tinha terminado, mas o intervalo da janela de manutenção foi alcançado.<br> `Succeeded` - trabalho realizado.<br> `InstallFailed` - atualização não realizada.<br> `NotIncluded`<br> `Excluded` |
| KBID | ID do artigo da base de dados de conhecimento para o Windows Update. |
| ManagementGroupName | Nome do grupo de gerenciamento do Operations Manager ou do workspace do Log Analytics. |
| OSType | Tipo do sistema operacional. Os valores são Windows ou Linux. |
| Produto | Produtos aos quais a atualização se aplica. |
| Recurso | Nome do recurso. |
| ResourceId | Identificador exclusivo do recurso associado ao registro. |
| ResourceProvider | O provedor de recursos. |
| ResourceType | Tipo de recurso. |
| SourceComputerId | Identificador exclusivo que representa o computador de origem. |
| SourceSystem | Sistema de origem do registro. O valor é `OperationsManager`. |
| StartTime | Hora em que a atualização está agendada para ser instalada. *Esta propriedade não é usada no momento. Confira TimeGenerated.* |
| SubscriptionId | Identificador exclusivo da assinatura do Azure. |
| SucceededOnRetry | Valor que indica se a execução da atualização não ocorreu na primeira tentativa e se a operação atual é uma nova tentativa. |
| TimeGenerated | Data e hora da criação do registro. |
| Title | O título da atualização. |
| Type | O tipo de atualização. O valor é `UpdateRunProgress`. |
| UpdateID | Identificador exclusivo da atualização de software. |
| VMUUID | Identificador exclusivo da máquina virtual. |
| ResourceId | Identificador exclusivo do recurso associado ao registro. |

### <a name="query-update-summary-record"></a>Consultar registro de resumo de atualização

É criado um registro do tipo `UpdateSummary` que fornece o resumo da atualização por computador. Esses registros têm as propriedades descritas na tabela a seguir:

| Propriedade | Descrição |
|----------|-------------|
| Computador | Nome de domínio totalmente qualificado do computador de relatórios. |
| ComputerEnvironment | Ambiente. Os valores são Azure ou Non-Azure. |
| CriticalUpdatesMissing | Número de atualizações críticas aplicáveis que estão faltando. |
| ManagementGroupName | Nome do grupo de gerenciamento do Operations Manager ou do workspace do Log Analytics. |
| NETRuntimeVersion | Versão do .NET Framework instalada no computador Windows. |
| OldestMissingSecurityUpdateBucket | Especificador do bucket de segurança ausente mais antigo. Os valores são:<br> Recente, se o valor for inferior a 30 dias<br> Há 30 dias<br> Há 60 dias<br> Há 90 dias<br> Há 120 dias<br> Há 150 dias<br> Há 180 dias<br> Mais antigo quando o valor é superior a 180 dias. |
| OldestMissingSecurityUpdateInDays | Número total de dias para a atualização mais antiga detectada, conforme aplicável, que não foi instalada. |
| OsVersion | A versão do sistema operacional. |
| OtherUpdatesMissing | Contagem de atualizações detectadas que estão faltando. |
| Recurso | Nome do recurso do registro. |
| ResourceGroup | Nome do grupo de recursos que contém o recurso. |
| ResourceId | Identificador exclusivo do recurso associado ao registro. |
| ResourceProvider | O provedor de recursos. |
| ResourceType | Tipo de recurso. |
| RestartPending | True se uma reinicialização estiver pendente. Caso contrário, False. |
| SecurityUpdatesMissing | Contagem de atualizações de segurança ausentes aplicáveis.|
| SourceComputerId | Identificador exclusivo da máquina virtual. |
| SourceSystem | Sistema de origem do registro. O valor é `OpsManager`. |
| SubscriptionId | Identificador exclusivo da assinatura do Azure. |
| TimeGenerated | Data e hora da criação do registro. |
| TotalUpdatesMissing | Número total de atualizações ausentes que se aplicam. |
| Type | Tipo de registro. O valor é `UpdateSummary`. |
| VMUUID | Identificador exclusivo da máquina virtual. |
| WindowsUpdateAgentVersion | Versão do agente do Windows Update. |
| WindowsUpdateSetting | Status do agente do Windows Update. Os valores possíveis são:<br> `Scheduled installation`<br> `Notify before installation`<br> `Error returned from unhealthy WUA agent` |
| WSUSServer | Erros se o agente de Windows Update tiver um problema, para auxiliar na solução de problemas. |
| _ResourceId | Identificador exclusivo do recurso associado ao registro. |

## <a name="sample-queries"></a>Consultas de exemplo

As seções a seguir fornecem consultas de log de exemplo dos registros de atualizações coletados pelo Gerenciamento de Atualizações.

### <a name="confirm-that-non-azure-machines-are-enabled-for-update-management"></a>Confirme se os computadores não Azure estão habilitados para Gerenciamento de Atualizações.

Para confirmar se os computadores conectados diretamente estão se comunicando com o Azure Monitor, execute uma das pesquisas de logs a seguir.

#### <a name="linux"></a>Linux

```loganalytics
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```loganalytics
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

Em um computador Windows, você pode examinar a seguinte informação para verificar a conectividade do agente com os logs do Azure Monitor:

1. No painel de controle, abra o **Microsoft Monitoring Agent**. Na guia **Azure Log Analytics**, o agente exibirá a seguinte mensagem: **O Microsoft Monitoring Agent conectou-se com êxito ao Log Analytics**.

1. Abra o Log de Eventos do Windows. Navegue até **Logs de Aplicativos e Serviços\Operations Manager** e procure as IDs de Evento 3000 e 5002 do **Conector de Serviço** de origem. Esses eventos indicam que o computador foi registrado com o espaço de trabalho do Log Analytics e está recebendo a configuração.

Se o agente não puder se comunicar com os logs do Azure Monitor e o agente estiver configurado para se comunicar com a Internet por meio de um servidor proxy ou firewall, verifique se o servidor proxy ou firewall está configurado corretamente. Para saber como verificar se o firewall ou o servidor proxy está configurado corretamente, veja [Configuração de rede para agente do Windows](../../azure-monitor/platform/agent-windows.md) ou [Configuração de rede para agente do Linux](../../azure-monitor/learn/quick-collect-linux-computer.md).

> [!NOTE]
> Se os seus sistemas Linux estiverem configurados para se comunicar com um proxy ou com o Gateway do Log Analytics, e você estiver habilitando o Gerenciamento de Atualizações, atualize as permissões de `proxy.conf` para conceder ao grupo omiuser permissão de leitura no arquivo usando os seguintes comandos:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Agentes do Linux recém-adicionados mostram um status de **Atualizado** após ter sido realizada uma avaliação. Esse processo pode levar até seis horas.

Para confirmar se um grupo de gerenciamento do Operations Manager está se comunicando com os logs do Azure Monitor, confira [Validar a integração do Operations Manager com os logs do Azure Monitor](../../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor).

### <a name="single-azure-vm-assessment-queries-windows"></a>Consultas de Avaliação de VM única do Azure (Windows)

Substitua o valor VMUUID pelo GUID da VM da máquina virtual que você está consultando. Você pode encontrar o VMUUID que deveria ser usado executando a seguinte consulta nos logs do Azure Monitor: `Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>Resumo das atualizações ausentes

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>Lista das atualizações ausentes

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

### <a name="single-azure-vm-assessment-queries-linux"></a>Consultas de avaliação de VM única do Azure (Linux)

Para algumas distribuições de Linux, há uma incompatibilidade de [endianness](https://en.wikipedia.org/wiki/Endianness) com o valor VMUUID que vem do Azure Resource Manager e o que é armazenado nos logs do Azure Monitor. A consulta a seguir procura uma correspondência em qualquer endianness. Substitua os valores VMUUID pelo formato big-endian e little-endian do GUID para retornar corretamente os resultados. Você pode encontrar o VMUUID que deveria ser usado executando a seguinte consulta nos logs do Azure Monitor: `Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>Resumo das atualizações ausentes

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>Lista das atualizações ausentes

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

### <a name="multi-vm-assessment-queries"></a>Consultas de avaliação de várias VMs

#### <a name="computers-summary"></a>Resumo de computadores

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeedOtherUpdatesCount=sum(computersNeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount
```

#### <a name="missing-updates-summary"></a>Resumo das atualizações ausentes

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="computers-list"></a>Lista de computadores

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

#### <a name="missing-updates-list"></a>Lista das atualizações ausentes

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="next-steps"></a>Próximas etapas

* Para obter detalhes de logs do Azure Monitor, confira [Logs do Azure Monitor](../../azure-monitor/log-query/log-query-overview.md).
* Para obter ajuda com alertas, consulte [Configurar alertas](configure-alerts.md).
