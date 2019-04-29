---
title: Gerenciar o uso e os custos do Log Analytics do Azure| Microsoft Docs
description: Saiba como alterar o plano de preços e gerenciar o volume de dados e a política de retenção para o espaço de trabalho do Log Analytics no Azure.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/29/2018
ms.author: magoedte
ms.subservice: ''
ms.openlocfilehash: a2f90c52823664df5fdc71c55220cc660c2f68e3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60782818"
---
# <a name="manage-usage-and-costs-for-log-analytics-in-azure-monitor"></a>Gerenciar o uso e custos para o Log Analytics no Azure Monitor

> [!NOTE]
> Este artigo descreve como controlar os custos no Log Analytics configurando o período de retenção de dados.  Consulte os seguintes artigos para informações relacionadas.
> - [Analisar o uso de dados no Log Analytics](manage-cost-storage.md) descreve como analisar e alertar sobre o uso de dados.
> - [Monitoramento de uso e custos estimados](usage-estimated-costs.md) descreve como exibir o uso e os custos estimados nos vários recursos de monitoramento do Azure para diferentes modelos de preços. Também descreve como alterar seu modelo de preços.

Log Analytics no Azure Monitor é projetado para escala e fornecer suporte à coleta, indexação e armazenamento de grandes quantidades de dados por dia, de qualquer fonte em sua empresa ou implantado no Azure.  Embora isso possa ser um driver primário para a organização, a eficiência de custo é, em última instância, o driver subjacente. Para esse fim, é importante entender que o custo de um espaço de trabalho do Log Analytics não se baseia apenas no volume de dados coletados, mas também depende do plano selecionado e de quanto tempo você escolheu armazenar os dados gerados por suas fontes conectadas.  

Neste artigo, analisamos como você pode monitorar proativamente o volume de dados e o aumento do armazenamento e definir limites para controlar esses custos associados. 

O custo dos dados pode ser considerável dependendo dos fatores a seguir: 

- Volume dos dados gerados e ingeridos no workspace 
    - Número de soluções de gerenciamento ativadas
    - Número de sistemas monitorados
    - Tipo de dados coletados de cada recurso monitorado 
- O período de tempo que decidir reter seus dados 

## <a name="understand-your-workspaces-usage-and-estimated-cost"></a>Reconhecer o uso do espaço de trabalho e o custo estimado
O Log Analytics facilita reconhecer quais são os custos com base nos padrões de uso recentes.  Para isso, use **o Log Analytics e Custos Estimados** para revisar e analisar o uso de dados. A mostra quantos dados são coletados por cada solução, quantos dados estão sendo retidos e uma estimativa de seus custos com base na quantidade de dados ingeridos e em qualquer retenção adicional além do valor incluído.

![Uso e custos estimados](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Para explorar seus dados com mais detalhes, clique no ícone no canto superior direito de qualquer um dos gráficos na página **Uso e custos estimados**. Agora você pode trabalhar com essa consulta para explorar mais detalhes do seu uso.  

![Exibir logs](media/manage-cost-storage/logs.png)

Da página**uso e custos estimados**, pode-se examinar o seu volume de dados para o mês. Isso inclui todos os dados recebidos e retidos no espaço de trabalho do Log Analytics.  Clique em **Detalhes de uso** na parte superior da página para exibir o painel de uso com informações sobre tendências de volume de dados por fonte, computadores e oferta. Para exibir e definir um limite diário ou modificar o período de retenção, clique em **Gerenciamento de volume de dados**.
 
Os encargos do Log Analytics são adicionadas à sua fatura do Azure. É possível ver os detalhes da fatura do Azure na seção Cobrança do Portal do Azure ou no [Portal de Cobrança do Azure](https://account.windowsazure.com/Subscriptions).  

## <a name="daily-cap"></a>Limite diário
É possível configurar um limite diário e limitar a ingestão diária para o workspace, mas seja cuidadoso, pois sua meta não deve ser atingir o limite diário.  Caso contrário, você perderá os dados no restante do dia, o que pode afetar outros serviços e soluções do Azure cuja funcionalidade pode depender de dados atualizados no workspace.  Como resultado, sua capacidade de observar e receber alertas quando as condições de integridade dos recursos que dão suporte a serviços de TI forem afetadas.  O limite diário destina-se a ser usado como uma maneira de gerenciar o aumento inesperado no volume de dados dos recursos gerenciados e permanecer dentro do limite, ou quando você quiser simplesmente limitar encargos não planejados para o workspace.  

Quando o limite diário é alcançado, a coleta de tipos de dados faturáveis é interrompida pelo restante do dia. Uma faixa de aviso aparece na parte superior da página do espaço de trabalho do Log Analytics selecionado e um evento de operação é enviado para a tabela *Operação* na categoria **LogManagement**. A coleta de dados é retomada após o tempo de redefinição definido em *O limite diário será definido em*. É recomendável definir uma regra de alerta com base nesse evento de operação, configurada para notificar quando o limite de dados diários for alcançado. 

### <a name="identify-what-daily-data-limit-to-define"></a>Identificar o limite diário de dados a definir 
Analise [Uso do Log Analytics e custos estimados](usage-estimated-costs.md) para reconhecer a tendência de ingestão de dados e qual é o limite diário de volume a ser definido. Considere-o com cuidado, pois não será possível monitorar os recursos depois que o limite for alcançado. 

### <a name="manage-the-maximum-daily-data-volume"></a>Gerenciar o volume de dados diário máximo 
As etapas a seguir descrevem como configurar um limite para gerenciar o volume de dados que o Log Analytics ingerirá por dia.  

1. No workspace, selecione **Uso e custos estimados** no painel esquerdo.
2. Na página **Uso e custos estimados** para o workspace selecionado, clique em **Gerenciamento de volume de dados** na parte superior da página. 
3. O limite diário é **OFF** por padrão – clique em **ON** para habilitá-lo e defina o limite de volume de dados em GB dia.<br><br> ![Configuração do limite de dados do Log Analytics](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Alerta quando o limite diário for atingido
Embora uma indicação visual seja apresentada no Portal do Azure quando o limite de dados é alcançado, esse comportamento não alinha-se necessariamente à maneira como você gerencia problemas operacionais que exigem atenção imediata.  Para receber uma notificação de alerta, é possível criar uma nova regra de alerta no Azure Monitor.  Para saber mais, consulte [como criar, exibir e gerenciar alertas](alerts-metric.md).      

Para começar, aqui estão as configurações recomendadas para o alerta:

* Destino: Selecionar o recurso do Log Analytics
* Critérios: 
   * Nome do sinal: Pesquisa de logs personalizada
   * Consulta de pesquisa: Operação | em que Detalhe tem 'OverQuota'
   * Baseado em: Número de resultados
   * Condição: Maior que
   * Limite: 0
   * Período: 5 (minutos)
   * Frequência: 5 (minutos)
* Nome da regra de alerta: Limite diário de dados atingido
* Gravidade: Aviso (Sev 1)

Quando o alerta é definido e o limite é alcançado, um alerta é disparado e executa a resposta definida no Grupo de Ações. Ele pode notificar a equipe por email e mensagens de texto, ou automatizar ações usando webhooks, runbooks de Automação ou [integrando com uma solução ITSM externa](itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="change-the-data-retention-period"></a>Alterar o período de retenção de dados 
As etapas a seguir descrevem como configurar por quanto tempo os dados de log são mantidos no workspace.
 
1. No workspace, selecione **Uso e custos estimados** no painel esquerdo.
2. Na página **Uso e estimativa de custos**, clique em **Gerenciamento de volume de dados** na parte superior da página.
5. No painel, mova o controle deslizante para aumentar ou diminuir o número de dias e, em seguida, clique em **Salvar**.  Se você usar a camada *Gratuita*, não será possível modificar o período de retenção de dados, sendo necessário atualizar para a camada paga para controlar essa configuração.<br><br> ![Alterar a configuração de retenção de dados do workspace](media/manage-cost-storage/manage-cost-change-retention-01.png)

## <a name="legacy-pricing-tiers"></a>Tipos de preço legados

Clientes com um Enterprise Agreement assinados antes de 1º de julho de 2018 ou que já criaram um espaço de trabalho do Log Analytics em uma assinatura, você ainda terá acesso ao plano *Gratuito*. Se sua assinatura não está vinculada a um registro de EA existente, a camada *Gratuita* não estará disponível quando você criar um workspace em uma nova assinatura após 2 de abril de 2018.  Os dados estarão limitados a 7 dias de retenção para a camada *Gratuita*.  Para o camadas herdadas *autônomo* ou *por nó* , bem como o tipo único de preço atual de 2018, os dados coletados estão disponível para os últimos 31 dias. A camada *Gratuita* tem um limite diário de ingestão de 500 MB e, se perceber que excede consistentemente o volume permitido, poderá alterar o workspace para um outro plano para coletar dados além desse limite. 

> [!NOTE]
> Para usar os direitos provenientes da aquisição de OMS E1 Suite, OMS E2 Suite OMS ou Complemento do OMS para System Center, escolha o tipo de preço *Por Nó* do Log Analytics.

## <a name="changing-pricing-tier"></a>Alterando o tipo de preço

Se o espaço de trabalho do Log Analytics tem acesso aos tipos de preço herdados, para alternar entre os tipos de preço herdados:

1. No portal do Azure, no painel de inscrições do Log Analytics, selecione um workspace.

2. No painel de workspace, sob **gerais**, selecione **tipo de preço**.  

3. Sob **tipo de preço**, selecione um tipo de preço e, em seguida, clique em **selecione**.  
    ![Selecionado o plano de preços](media/manage-cost-storage/workspace-pricing-tier-info.png)

Se quiser mover seu workspace para o tipo de preço atual, você precisará [alterar o modelo de preços de monitoramento de sua assinatura no Azure Monitor](usage-estimated-costs.md#moving-to-the-new-pricing-model), o que alterará o tipo de preço de todos os workspaces nessa assinatura.


> [!NOTE]
> Você pode saber mais sobre [definir o tipo de preço por meio do ARM](template-workspace-configuration.md#create-a-log-analytics-workspace) e como garantir que sua implantação do ARM terá êxito, independentemente se a assinatura está no herdado ou no novo modelo de preços. 


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Solucionar problemas se o Log Analytics não está mais coletando dados
Se você estiver usando o tipo de preço gratuito herdado e tiver enviado mais de 500 MB de dados em um dia, a coleta de dados será interrompida pelo restante do dia. Alcançar o limite diário é um motivo comum para o Log Analytics parar de coletar dados ou para dados parecerem estar ausentes.  O Log Analytics cria um evento de tipo Operação quando a coleta de dados inicia e para. Execute a seguinte consulta na pesquisa para verificar se você está atingindo o limite diário e perdendo dados: 

`Operation | where OperationCategory == 'Data Collection Status'`

Quando a coleta de dados é interrompida, o OperationStatus torna-se Aviso. Quando a coleta de dados inicia, o OperationStatus é Com êxito. A tabela a seguir descreve os motivos pelos quais a coleta de dados é interrompida e uma ação é sugerida para retomar a coleta de dados:  

|Motivo para a interrupção da coleta| Solução| 
|-----------------------|---------|
|Limite diário de tipo de preço gratuito herdado atingido |Aguarde até o dia seguinte para que a coleta seja reiniciada automaticamente ou altere para um tipo de preço pago.|
|O limite diário do seu espaço de trabalho foi atingido|Aguarde para a coleta ser reiniciada automaticamente ou aumente o limite diário de volume de dados descrito em Gerenciar o volume máximo de dados diário. O tempo de redefinição de limite diário é exibido na página **Gerenciamento de volume de dados**. |
|Assinatura do Azure está em um estado suspenso devido a:<br> A avaliação gratuita terminou<br> O Azure Pass expirou<br> Limite de gastos mensal atingido (por exemplo, em uma assinatura do MSDN ou do Visual Studio)|Converter para uma assinatura paga<br> Remova o limite ou espere o limite ser redefinido|

Para ser notificado quando a coleta de dados terminar, use as etapas descritas em *Criar um limite de dados diário* para ser notificado quando a coleta de dados parar e siga as etapas em Usar as etapas descritas em adicionar ações para regras de alerta configurar uma ação de runbook, webhook ou email para a regra de alerta. 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Solução de problemas por uso acima do esperado
O uso aumenta devido a uma ou mais das seguintes causas:
- A quantidade de nós enviando dados para o Log Analytics está acima da esperada
- Os dados enviados para o Log Analytics estão acima do esperado

A próxima explor seções

## <a name="understanding-nodes-sending-data"></a>Noções básicas sobre nós enviando dados

Para reconhecer o número de computadores (nós) que relatam dados a cada dia no último mês, use

`Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart`

Para obter uma lista de computadores que estão enviando **tipos de dados cobrados** (alguns tipos de dados são gratuitos), aproveite a propriedade [_IsBillable](log-standard-properties.md#_isbillable):

`union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName`

Use estas consultas `union withsource = tt *` com moderação como verificações em tipos de dados que são caros para executar. Esta consulta substitui o método antigo de consulta de informações por computador com o tipo de dados de uso.  

Isso pode ser estendido para retornar a contagem de computadores por hora que estão enviando cobrado tipos de dados (que é como o Log Analytics calcula nós faturáveis herdado por nó de tipo de preço):

`union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc`

## <a name="understanding-ingested-data-volume"></a>Noções básicas sobre ingerido volume de dados 

Na página **Uso e custos estimados**, o gráfico *Ingestão de dados por solução* mostra o volume total de dados enviados e quanto está sendo enviado por cada solução. Isso permite determinar tendências, como se o uso geral de dados (ou uso por uma solução específica) está crescendo, permanecendo estável ou diminuindo. É a consulta usada para gerar isso

`Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart`

Observe que a cláusula "onde IsBillable = verdadeiro" filtra os tipos de dados de determinadas soluções para o qual não há nenhuma taxa de ingestão. 

Você pode detalhar mais para ver tendências de dados para tipos de dados específicos, por exemplo, se você quiser estudar os dados devido aos logs do IIS:

`Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart`

### <a name="data-volume-by-computer"></a>Volume de dados por computador

Para ver os **tamanho** de eventos cobráveis ingeridos por computador, use o `_BilledSize` propriedade ([log-padrão-properties #_billedsize.md](learn more)) que fornece o tamanho em bytes:

```
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last
```

O `_IsBillable` propriedade especifica se os dados ingeridos incorrerão em encargos ([log-padrão-properties.md #_isbillable](Learn more).)

Para ver a **contagem** de eventos ingeridos por computador, use

`union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last`

Para ver a contagem de eventos faturáveis ingeridos por computador, use 

`union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last`

Se você quiser ver as contagens dos tipos de dados faturáveis que estão enviando dados para um computador específico, use:

```
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Volume de dados por recursos do Azure, grupo de recursos ou assinatura

Para dados de nós hospedados no Azure, você pode obter o **tamanho** de eventos cobráveis ingeridos __por computador__, use o `_ResourceId` propriedade que fornece o caminho completo para o recurso ([ log-padrão-properties.md #_resourceid](learn more)):

```
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

Para dados de nós hospedados no Azure, você pode obter o **tamanho** de eventos cobráveis ingeridos __por assinatura do Azure__, analisar o `_ResourceId` a propriedade como:

```
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

Alterando `subscriptionId` para `resourceGroup` mostrará o volume de dados ingeridos faturáveis por grupo resouurce do Azure. 


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

`union
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
| count`

Para ver o número de nós de automação distintos, use a consulta:

```
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

## <a name="create-an-alert-when-data-collection-is-higher-than-expected"></a>Criar um alerta quando a coleta de dados for maior que a esperada

Este artigo descreve como criar um alerta quando:
- O volume de dados excede uma quantidade definida.
- A previsão do volume de dados excede uma quantidade definida.

Os alertas do Azure oferecem suporte a [alertas de log](alerts-unified-log.md) que usam consultas de pesquisa. 

A consulta abaixo tem um resultado quando há mais de 100 GB de dados coletados nas últimas 24 horas:

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`

A consulta abaixo usa uma fórmula simples para prever quando mais de 100 GB de dados serão enviados em um mesmo dia: 

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`

Para receber um alerta em caso de volume de dados diferente, altere o número 100 nas consultas para o número de GB que deve disparar um alerta.

Use as etapas descritas em [criar um novo alerta de log](alerts-metric.md) para ser notificado quando a coleta de dados for maior que a esperada.

Ao criar o alerta para a primeira consulta, quando há mais de 100 GB de dados em 24 horas, defina:  

- **Definir condição de alerta** especifica seu workspace do Log Analytics como o destino do recurso.
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

- **Definir condição de alerta** especifica seu workspace do Log Analytics como o destino do recurso.
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

## <a name="next-steps"></a>Próximas etapas
* Confira [Pesquisas de log no Log Analytics](../log-query/log-query-overview.md) para aprender a usar a linguagem de pesquisa. Você pode usar consultas de pesquisa para executar outras análises nos dados de uso.
* Use as etapas descritas em [criar um alerta de log](alerts-metric.md) para ser notificado quando um critério de pesquisa for atendido.
* Use [direcionamento de solução](../insights/solution-targeting.md) para coletar dados somente dos grupos de computadores necessários.
* Para configurar uma política de coleta de eventos eficaz, confira a [Política de filtragem da Central de Segurança do Azure](../../security-center/security-center-enable-data-collection.md).
* Altere a [configuração do contador de desempenho](data-sources-performance-counters.md).
* Para modificar as configurações de coleta de eventos, analise [configuração de log de eventos](data-sources-windows-events.md).
* Para modificar as configurações de coleta de syslog, analise [configuração de syslog](data-sources-syslog.md).


