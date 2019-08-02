---
title: Gerenciar o uso e custos para Logs do Azure Monitor | Microsoft Docs
description: Saiba como alterar o plano de preços e gerenciar a política de retenção e volume de dados do espaço de trabalho do Log Analytics no Azure Monitor.
services: azure-monitor
documentationcenter: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: magoedte
ms.subservice: ''
ms.openlocfilehash: bcfefc9698f7f251e99531750e19e7c06395e064
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655700"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Gerenciar o uso e os custos com Logs do Azure Monitor

> [!NOTE]
> Este artigo descreve como controlar os custos no Azure Monitor, definindo o período de retenção de dados para seu espaço de trabalho do Log Analytics.  Consulte o seguinte artigo para obter informações relacionadas.
> - [Monitorando o uso e os custos estimados](usage-estimated-costs.md) descreve como visualizar o uso e os custos estimados em vários recursos de monitoramento do Azure para diferentes modelos de preços. Também descreve como alterar seu modelo de preços.

Os Logs do Azure Monitor é projetado para escala e fornecer suporte à coleta, indexação e armazenamento de grandes quantidades de dados por dia, de qualquer fonte em sua empresa ou implantado no Azure.  Embora isso possa ser um driver primário para a organização, a eficiência de custo é, em última instância, o driver subjacente. Para esse fim, é importante entender que o custo de um espaço de trabalho do Log Analytics não é baseado apenas no volume de dados coletados, também é depende do plano selecionado, e por quanto tempo você escolheu para armazenar os dados gerados a partir de suas fontes conectadas.  

Neste artigo, analisamos como você pode monitorar proativamente o volume de dados e o aumento do armazenamento e definir limites para controlar esses custos associados. 

O custo dos dados pode ser considerável dependendo dos fatores a seguir: 

- Volume dos dados gerados e ingeridos no workspace 
    - Número de soluções de gerenciamento ativadas
    - Número de sistemas monitorados
    - Tipo de dados coletados de cada recurso monitorado 
- O período de tempo que decidir reter seus dados 

## <a name="understand-your-workspaces-usage-and-estimated-cost"></a>Reconhecer o uso do espaço de trabalho e o custo estimado

Azure possibilita monitorar Logs fácil entender o que são os custos se basear em padrões de uso recentes. Para fazer isso, use **uso do Log Analytics e custos estimados** para revisar e analisar o uso de dados. A mostra quantos dados são coletados por cada solução, quantos dados estão sendo retidos e uma estimativa de seus custos com base na quantidade de dados ingeridos e em qualquer retenção adicional além do valor incluído.

![Uso e custos estimados](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Para explorar seus dados com mais detalhes, clique no ícone no canto superior direito de qualquer um dos gráficos na página **Uso e custos estimados**. Agora você pode trabalhar com essa consulta para explorar mais detalhes do seu uso.  

![Exibir logs](media/manage-cost-storage/logs.png)

Da página**uso e custos estimados**, pode-se examinar o seu volume de dados para o mês. Isso inclui todos os dados recebidos e retidos no espaço de trabalho do Log Analytics.  Clique em **detalhes de uso** da parte superior da página para exibir o painel de uso com informações sobre as tendências de volume de dados por fonte, computadores e oferta. Para exibir e definir um limite diário ou modificar o período de retenção, clique em **Gerenciamento de volume de dados**.
 
Os encargos do Log Analytics são adicionadas à sua fatura do Azure. É possível ver os detalhes da fatura do Azure na seção Cobrança do Portal do Azure ou no [Portal de Cobrança do Azure](https://account.windowsazure.com/Subscriptions).  

## <a name="daily-cap"></a>Limite diário

É possível configurar um limite diário e limitar a ingestão diária para o workspace, mas seja cuidadoso, pois sua meta não deve ser atingir o limite diário.  Caso contrário, você perderá os dados no restante do dia, o que pode afetar outros serviços e soluções do Azure cuja funcionalidade pode depender de dados atualizados no workspace.  Como resultado, sua capacidade de observar e receber alertas quando as condições de integridade dos recursos que dão suporte a serviços de TI forem afetadas.  O limite diário destina-se a ser usado como uma maneira de gerenciar o aumento inesperado no volume de dados de seus recursos gerenciados e permanecer dentro do limite, ou quando você quiser limitar cobranças não planejadas no seu espaço de trabalho.  

Quando o limite diário é alcançado, a coleta de tipos de dados faturáveis é interrompida pelo restante do dia. Uma faixa de aviso aparece na parte superior da página do espaço de trabalho do Log Analytics selecionado e um evento de operação é enviado para a tabela *Operação* na categoria **LogManagement**. A coleta de dados é retomada após o tempo de redefinição definido em *O limite diário será definido em*. É recomendável definir uma regra de alerta com base nesse evento de operação, configurada para notificar quando o limite de dados diários for alcançado. 

> [!NOTE]
> O limite diário não interrompe a coleta de dados da Central de segurança do Azure.

### <a name="identify-what-daily-data-limit-to-define"></a>Identificar o limite diário de dados a definir

Analise [Uso do Log Analytics e custos estimados](usage-estimated-costs.md) para reconhecer a tendência de ingestão de dados e qual é o limite diário de volume a ser definido. Considere-o com cuidado, pois não será possível monitorar os recursos depois que o limite for alcançado. 

### <a name="manage-the-maximum-daily-data-volume"></a>Gerenciar o volume de dados diário máximo

As etapas a seguir descrevem como configurar um limite para gerenciar o volume de dados que o espaço de trabalho do Log Analytics ingerirá por dia.  

1. No workspace, selecione **Uso e custos estimados** no painel esquerdo.
2. Na página **Uso e custos estimados** para o workspace selecionado, clique em **Gerenciamento de volume de dados** na parte superior da página. 
3. O limite diário é **OFF** por padrão – clique em **ON** para habilitá-lo e defina o limite de volume de dados em GB dia.

    ![Configurar o limite de dados do log Analytics](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Alerta quando o limite diário for atingido

Embora uma indicação visual seja apresentada no Portal do Azure quando o limite de dados é alcançado, esse comportamento não alinha-se necessariamente à maneira como você gerencia problemas operacionais que exigem atenção imediata.  Para receber uma notificação de alerta, é possível criar uma nova regra de alerta no Azure Monitor.  Para obter mais informações, consulte [como criar, exibir e gerenciar alertas](alerts-metric.md).

Para começar, aqui estão as configurações recomendadas para o alerta:

- Destino: Selecionar o recurso do Log Analytics
- Critérios: 
   - Nome do sinal: Pesquisa de logs personalizada
   - Consulta de pesquisa: Operação | em que Detalhe tem 'OverQuota'
   - Baseado em: Número de resultados
   - Condição: Maior que
   - Limite: 0
   - Período: 5 (minutos)
   - Frequência: 5 (minutos)
- Nome da regra de alerta: Limite diário de dados atingido
- Gravidade: Aviso (Sev 1)

Quando o alerta é definido e o limite é alcançado, um alerta é disparado e executa a resposta definida no Grupo de Ações. Ele pode notificar a equipe por email e mensagens de texto, ou automatizar ações usando webhooks, runbooks de Automação ou [integrando com uma solução ITSM externa](itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="change-the-data-retention-period"></a>Alterar o período de retenção de dados

As etapas a seguir descrevem como configurar por quanto tempo os dados de log são mantidos no workspace.
 
1. No workspace, selecione **Uso e custos estimados** no painel esquerdo.
2. Na página **Uso e estimativa de custos**, clique em **Gerenciamento de volume de dados** na parte superior da página.
3. No painel, mova o controle deslizante para aumentar ou diminuir o número de dias e, em seguida, clique em **Salvar**.  Se você usar a camada *Gratuita*, não será possível modificar o período de retenção de dados, sendo necessário atualizar para a camada paga para controlar essa configuração.

    ![Alterar a configuração de retenção de dados de espaço de trabalho](media/manage-cost-storage/manage-cost-change-retention-01.png)
    
A retenção também pode ser [definidas por meio do ARM](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) usando o `dataRetention` parâmetro. Além disso, se você definir a retenção de dados como 30 dias, você pode disparar uma limpeza imediata de dados mais antigos usando o `immediatePurgeDataOn30Days` parâmetro, que pode ser útil para cenários relacionados à conformidade. Essa funcionalidade é exposta somente por meio do ARM. 

## <a name="legacy-pricing-tiers"></a>Tipos de preço legados

Assinaturas que tinham um espaço de trabalho do Log Analytics ou o recurso do Application Insights nele antes de 2 de abril de 2018 ou vinculadas a um contrato Enterprise que foi iniciado antes do dia 1 de fevereiro de 2019 continuarão tendo acesso aos tipos de preços herdado: **Livre**, **autônomo (por GB)** e **por nó (OMS)** .  Espaços de trabalho no tipo de preço gratuito terá diária ingestão de dados é limitado a 500 MB (exceto para tipos de dados de segurança coletados pela Central de segurança do Azure) e a retenção de dados é limitada a sete dias. O tipo de preço gratuito é destinado apenas a fins de avaliação. Espaços de trabalho no autônomo ou tipos de preço por nó tem retenção configurável pelo usuário de até 2 anos. Espaços de trabalho criados antes de abril de 2016 também têm acesso original **Standard** e **Premium** tipos de preço. Mais detalhes sobre as limitações de camada de preços está disponíveis [aqui](https://docs.microsoft.com/azure/azure-subscription-service-limits#log-analytics-workspaces).

> [!NOTE]
> Para usar os direitos provenientes da aquisição de OMS E1 Suite, OMS E2 Suite OMS ou Complemento do OMS para System Center, escolha o tipo de preço *Por Nó* do Log Analytics.

Os usuários pioneiros mais antigos do Log Analytics também tem acesso para os tipos de preço originais **Standard** e **Premium**, que corrigiram retenção de dados de 30 e 365 dias, respectivamente. 

## <a name="changing-pricing-tier"></a>Alterando o tipo de preço

Se o espaço de trabalho do Log Analytics tem acesso aos tipos de preço herdados, para alternar entre os tipos de preço herdados:

1. No portal do Azure, no painel de inscrições do Log Analytics, selecione um workspace.

2. No painel de workspace, sob **gerais**, selecione **tipo de preço**.  

3. Sob **tipo de preço**, selecione um tipo de preço e, em seguida, clique em **selecione**.  
    ![Selecionado o plano de preços](media/manage-cost-storage/workspace-pricing-tier-info.png)

Você também pode [definir o tipo de preço por meio do ARM](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) usando o `ServiceTier` parâmetro. 

## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Solucionar problemas se o Log Analytics não está mais coletando dados

Se você estiver usando o tipo de preço gratuito herdado e tiver enviado mais de 500 MB de dados em um dia, a coleta de dados será interrompida pelo restante do dia. Alcançar o limite diário é um motivo comum para o Log Analytics parar de coletar dados ou para dados parecerem estar ausentes.  O Log Analytics cria um evento de tipo Operação quando a coleta de dados inicia e para. Execute a seguinte consulta na pesquisa para verificar se você está atingindo o limite diário e perdendo dados: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Quando a coleta de dados é interrompida, o OperationStatus é **aviso**. Quando a coleta de dados é iniciado, o OperationStatus é **bem-sucedido**. A tabela a seguir descreve os motivos pelos quais a coleta de dados é interrompida e uma ação é sugerida para retomar a coleta de dados:  

|Motivo para a interrupção da coleta| Solução| 
|-----------------------|---------|
|Limite diário de tipo de preço gratuito herdado atingido |Aguarde até o dia seguinte para que a coleta seja reiniciada automaticamente ou altere para um tipo de preço pago.|
|O limite diário do seu espaço de trabalho foi atingido|Aguarde para a coleta ser reiniciada automaticamente ou aumente o limite diário de volume de dados descrito em Gerenciar o volume máximo de dados diário. O tempo de redefinição de limite diário é exibido na página **Gerenciamento de volume de dados**. |
|Assinatura do Azure está em um estado suspenso devido a:<br> A avaliação gratuita terminou<br> O Azure Pass expirou<br> Limite de gastos mensal atingido (por exemplo, em uma assinatura do MSDN ou do Visual Studio)|Converter para uma assinatura paga<br> Remova o limite ou espere o limite ser redefinido|

Para ser notificado quando a coleta de dados será interrompida, use as etapas descritas em *limite diário de dados criar* alerta para ser notificado quando a coleta de dados será interrompida. Use as etapas descritas em [criar um grupo de ação](action-groups.md) para configurar uma ação de runbook, webhook ou email para a regra de alerta. 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Solução de problemas por uso acima do esperado

O uso aumenta devido a uma ou mais das seguintes causas:
- Nós mais do que o esperado enviando dados para o espaço de trabalho do Log Analytics
- Mais dados que o esperado sendo enviados para o espaço de trabalho do Log Analytics

## <a name="understanding-nodes-sending-data"></a>Noções básicas sobre nós enviando dados

Para entender o número de computadores que relatam as pulsações a cada dia no último mês, use

```kusto
Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```

Para obter uma lista de computadores que será cobrada como nós se o espaço de trabalho estiver no herdado por nó de tipo de preço, procure por nós que estão transmitindo **cobrado tipos de dados** (alguns tipos de dados são gratuitos). Para fazer isso, use o `_IsBillable` [propriedade](log-standard-properties.md#_isbillable) e use o campo mais à esquerda do nome de domínio totalmente qualificado. Isso retorna a lista de computadores com dados cobrados:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

A contagem de nós faturáveis visto pode ser prevista como: 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| billableNodes=dcount(computerName)
```

> [!NOTE]
> Use estas consultas `union withsource = tt *` com moderação como verificações em tipos de dados que são caros para executar. Esta consulta substitui o método antigo de consulta de informações por computador com o tipo de dados de uso.  

É um cálculo mais preciso do que realmente será cobrado para obter a contagem de computadores que estão enviando os tipos de dados cobrados por hora. (Para espaços de trabalho no tipo de preço por nó herdado, o Log Analytics calcula o número de nós que precisam ser cobrado por hora.) 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="understanding-ingested-data-volume"></a>Noções básicas sobre ingerido volume de dados

Na página **Uso e custos estimados**, o gráfico *Ingestão de dados por solução* mostra o volume total de dados enviados e quanto está sendo enviado por cada solução. Isso permite determinar tendências, como se o uso geral de dados (ou uso por uma solução específica) está crescendo, permanecendo estável ou diminuindo. É a consulta usada para gerar isso

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart
```

Observe que a cláusula "onde IsBillable = verdadeiro" filtra os tipos de dados de determinadas soluções para o qual não há nenhuma taxa de ingestão. 

Você pode detalhar mais para ver tendências de dados para tipos de dados específicos, por exemplo, se você quiser estudar os dados devido aos logs do IIS:

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart
```

### <a name="data-volume-by-computer"></a>Volume de dados por computador

Para ver os **tamanho** de eventos cobráveis ingeridos por computador, use o `_BilledSize` [propriedade](log-standard-properties.md#_billedsize), que fornece o tamanho em bytes:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize Bytes=sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

O `_IsBillable` [propriedade](log-standard-properties.md#_isbillable) Especifica se os dados ingeridos incorrerão em encargos.

Para ver a contagem de **faturável** eventos ingeridos por computador, use 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount=count() by computerName  | sort by count_ nulls last
```

Se você quiser ver as contagens dos tipos de dados faturáveis que estão enviando dados para um computador específico, use:

```kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Volume de dados por recursos do Azure, grupo de recursos ou assinatura

Para dados de nós hospedados no Azure, você pode obter o **tamanho** de eventos cobráveis ingeridos __por computador__, use o _ResourceId [propriedade](log-standard-properties.md#_resourceid), que fornece o caminho completo para o recurso:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

Para dados de nós hospedados no Azure, você pode obter o **tamanho** de eventos cobráveis ingeridos __por assinatura do Azure__, analisar o `_ResourceId` a propriedade como:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

Alterando `subscriptionId` para `resourceGroup` mostrará o volume de dados ingeridos faturáveis por grupo de recursos do Azure. 


> [!NOTE]
> Alguns dos campos do tipo de dados Uso, ainda no esquema, foram reprovados e seus valores não serão mais preenchidos. Estes são **Computador**, bem como campos relacionados à ingestão (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** e **AverageProcessingTimeMs**.

### <a name="querying-for-common-data-types"></a>Consultando tipos de dados comuns

Para aprofundar a fonte de dados de um determinado tipo de dados, aqui estão algumas consultas de exemplo úteis:

+ Solução de **Segurança**
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ Solução do **Gerenciamento de log**
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ Tipo de dados de **Desempenho**
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ Tipo de dados de **Evento**
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ Tipo de dados **Syslog**
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ Tipo de dados **AzureDiagnostics**
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

### <a name="tips-for-reducing-data-volume"></a>Dicas para reduzir o volume de dados

Algumas sugestões para reduzir o volume de logs coletados incluem:

| Origem do alto volume de dados | Como reduzir o volume de dados |
| -------------------------- | ------------------------- |
| Eventos de segurança            | Selecione [eventos de segurança mínima ou comuns](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) <br> Alterar a política de auditoria de segurança para coletar somente eventos necessários. Em particular, examine a necessidade para coletar eventos para <br> - [auditoria de plataforma de filtragem](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [auditoria de registro](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [auditoria de sistema de arquivos](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [auditoria de objeto kernel](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [auditoria de manipulação de identificador](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - auditoria de armazenamento removível |
| contadores de desempenho       | Altere a [configuração do contador de desempenho](data-sources-performance-counters.md) para: <br> - Reduzir a frequência de coleta <br> - Reduzir o número de contadores de desempenho |
| Logs de eventos                 | Altere a [configuração de log de eventos](data-sources-windows-events.md) para: <br> - Reduzir o número de logs de eventos coletados <br> - Coletar somente níveis de eventos necessários. Por exemplo, não colete eventos de nível *informações* |
| syslog                     | Altere a [configuração do syslog](data-sources-syslog.md) para: <br> - Reduzir o número de instalações coletadas <br> - Coletar somente níveis de eventos necessários. Por exemplo, não coletar eventos de nível *Informações* e *Depurar* |
| AzureDiagnostics           | Altere a coleção de logs do recurso para: <br> - Reduzir o número de logs de envio de recursos para o Log Analytics <br> - Coletar somente os logs necessários |
| Dados da solução de computadores que não precisam da solução | Use [direcionamento de solução](../insights/solution-targeting.md) para coletar dados somente dos grupos de computadores necessários. |

### <a name="getting-security-and-automation-node-counts"></a>Ao obter contagens de nó de segurança e automação

Se você estiver no nível de preço "Por nó (OMS)", será cobrado com base no número de nós e soluções usados, o número de nós do Insights e do Analytics para os quais você está sendo faturado será mostrado na tabela do **Uso e custo estimado**.  

Para ver o número de nós de segurança distintos, você pode usar a consulta:

```kusto
union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count
```

Para ver o número de nós de automação distintos, use a consulta:

```kusto
 ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc
```

## <a name="create-an-alert-when-data-collection-is-high"></a>Criar um alerta quando a coleta de dados é alta

Este artigo descreve como criar um alerta quando:
- O volume de dados excede uma quantidade definida.
- A previsão do volume de dados excede uma quantidade definida.

Os alertas do Azure oferecem suporte a [alertas de log](alerts-unified-log.md) que usam consultas de pesquisa. 

A consulta abaixo tem um resultado quando há mais de 100 GB de dados coletados nas últimas 24 horas:

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type 
| where DataGB > 100
```

A consulta abaixo usa uma fórmula simples para prever quando mais de 100 GB de dados serão enviados em um mesmo dia: 

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table 
| summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type 
| where EstimatedGB > 100
```

Para receber um alerta em caso de volume de dados diferente, altere o número 100 nas consultas para o número de GB que deve disparar um alerta.

Use as etapas descritas em [criar um novo alerta de log](alerts-metric.md) para ser notificado quando a coleta de dados for maior que a esperada.

Ao criar o alerta para a primeira consulta, quando há mais de 100 GB de dados em 24 horas, defina:  

- **Definir condição de alerta** especifica seu espaço de trabalho do Log Analytics como o destino do recurso.
- **Critérios de alerta** especificam o seguinte:
   - **Nome do sinal** seleciona **Pesquisa de registro personalizada**
   - **Consulta de pesquisa** como `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`
   - **Lógica de alerta** é **Baseada em** *número de resultados* e **Condição** é *maior* que um **Limite**  de *0*
   - **Período de tempo** de *1440* minutos e **Frequência de alerta** a cada *60* minutos uma vez que os dados de uso são atualizados somente uma vez por hora.
- **Definir os detalhes do alerta** especifica o seguinte:
   - O **Nome** como *Volume de dados maior que 100 GB em 24 horas*
   - A **Gravidade** como *Aviso*

Especifique um já existente ou crie um novo [Grupo de ação](action-groups.md) para que, quando o alerta de log corresponder aos critérios, você seja notificado.

Ao criar o alerta para a segunda consulta, quando existe a previsão de que haverá mais de 100 GB de dados em 24 horas, defina:

- **Definir condição de alerta** especifica seu espaço de trabalho do Log Analytics como o destino do recurso.
- **Critérios de alerta** especificam o seguinte:
   - **Nome do sinal** seleciona **Pesquisa de registro personalizada**
   - **Consulta de pesquisa** como `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`
   - **Lógica de alerta** é **Baseada em** *número de resultados* e **Condição** é *maior* que um **Limite**  de *0*
   - **Período de tempo** de *180* minutos e **Frequência de alerta** a cada *60* minutos uma vez que os dados de uso são atualizados somente uma vez por hora.
- **Definir os detalhes do alerta** especifica o seguinte:
   - O **Nome** como *Previsão de volume de dados maior que 100 GB em 24 horas*
   - A **Gravidade** como *Aviso*

Especifique um já existente ou crie um novo [Grupo de ação](action-groups.md) para que, quando o alerta de log corresponder aos critérios, você seja notificado.

Ao receber um alerta, use as etapas na seção a seguir para solucionar problemas relativos ao uso acima do esperado.

## <a name="limits-summary"></a>Resumo de limites

Há alguns limites adicionais do Log Analytics, algumas das quais dependem do tipo de preço do Log Analytics. Essas situações são documentadas [aqui](https://docs.microsoft.com/azure/azure-subscription-service-limits#log-analytics-workspaces).


## <a name="next-steps"></a>Próximas etapas

- Ver [pesquisas de Log em Logs do Azure Monitor](../log-query/log-query-overview.md) para aprender a usar a linguagem de pesquisa. Você pode usar consultas de pesquisa para executar outras análises nos dados de uso.
- Use as etapas descritas em [criar um alerta de log](alerts-metric.md) para ser notificado quando um critério de pesquisa for atendido.
- Use [direcionamento de solução](../insights/solution-targeting.md) para coletar dados somente dos grupos de computadores necessários.
- Para configurar uma política de coleta de eventos eficaz, confira a [Política de filtragem da Central de Segurança do Azure](../../security-center/security-center-enable-data-collection.md).
- Altere a [configuração do contador de desempenho](data-sources-performance-counters.md).
- Para modificar as configurações de coleta de eventos, analise [configuração de log de eventos](data-sources-windows-events.md).
- Para modificar as configurações de coleta de syslog, analise [configuração de syslog](data-sources-syslog.md).