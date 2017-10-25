---
title: Coletar e analisar contadores de desempenho no Azure Log Analytics | Microsoft Docs
description: "Os contadores de desempenho são coletados pelo Log Analytics para analisar o desempenho em agentes do Windows e Linux.  Este artigo descreve como configurar a coleta de contadores de desempenho para agentes do Windows e do Linux, os detalhes deles que são armazenados no repositório do OMS e como analisá-los no portal do OMS."
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 20e145e4-2ace-4cd9-b252-71fb4f94099e
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/12/2017
ms.author: magoedte
ms.openlocfilehash: 953bb453b0a9635627fbbb6c3913d0cd757101c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="windows-and-linux-performance-data-sources-in-log-analytics"></a>Fontes de dados de desempenho do Windows e Linux no Log Analytics
Os contadores de desempenho no Windows e Linux fornecem informações sobre o desempenho de componentes de hardware, sistemas operacionais e aplicativos.  O Log Analytics pode coletar contadores de desempenho em intervalos frequentes para análises NRT (Quase em Tempo Real) além de agregar dados de desempenho para análise e relatório de longo prazo.

![Contadores de desempenho](media/log-analytics-data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Configurando os contadores de desempenho
Configure os contadores de desempenho no portal do OMS do [menu Dados nas Configurações do Log Analytics](log-analytics-data-sources.md#configuring-data-sources).

Quando você configurar os contadores de desempenho do Linux ou Windows para um novo espaço de trabalho do OMS pela primeira vez, você tem a opção de criar rapidamente vários contadores comuns.  Eles são listados com uma caixa de seleção ao lado de cada um.  Garanta que todos os contadores que deseja criar inicialmente estejam marcados e clique em **Add the selected performance counters**(Adicionar os contadores de desempenho selecionados).

Para os contadores de desempenho do Windows, você pode escolher uma instância específica para cada contador de desempenho. Para os contadores de desempenho do Linux, a instância de cada contador escolhido se aplicará a todos os contadores filhos do contador pai. A tabela a seguir mostra as instâncias comuns disponíveis para os contadores de desempenho do Linux e do Windows.

| Nome da instância | Descrição |
| --- | --- |
| \_Total |Total de todas as instâncias |
| \* |Todas as instâncias |
| (/&#124;/var) |Corresponde às instâncias chamadas: / ou /var |

### <a name="windows-performance-counters"></a>Contadores de desempenho do Windows

![Configurar contadores de desempenho do Windows](media/log-analytics-data-sources-performance-counters/configure-windows.png)

Siga este procedimento para adicionar um novo contador de desempenho do Windows para coletar.

1. Digite o nome do contador na caixa de texto no formato *objeto(instâncias)\contador*.  Quando você começar a digitar, verá uma lista de correspondência dos contadores comuns.  Você pode selecionar um contador na lista ou digitar um dos seus.  Você também pode retornar todas as instâncias de um determinado contador especificando *objeto\contador*.  

    Durante a coleta de contadores de desempenho do SQL Server de instâncias nomeadas, todos os contadores de instância nomeados começam com *MSSQL$* seguidos do nome da instância.  Por exemplo, para coletar o contador de Proporção de Ocorrência no Cache de Log para todos os bancos de dados do objeto de desempenho de Banco de Dados para a instância nomeada do SQL INST2, especificar `MSSQL$INST2:Databases(*)\Log Cache Hit Ratio`.

2. Clique em **+** ou pressione **Enter** para adicionar o contador à lista.
3. Quando você adicionar um contador, ele usa o padrão de 10 segundos para seu **Intervalo de Amostragem**.  Você poderá alterar isso para um valor mais alto de até 1800 segundos (30 minutos) se desejar reduzir os requisitos de armazenamento dos dados de desempenho coletados.
4. Quando terminar de adicionar contadores, clique no botão **Salvar** na parte superior da tela para salvar a configuração.

### <a name="linux-performance-counters"></a>Contadores de desempenho do Linux

![Configurar contadores de desempenho do Linux](media/log-analytics-data-sources-performance-counters/configure-linux.png)

Siga este procedimento para adicionar um novo contador de desempenho do Linux para coletar.

1. Por padrão, todas as alterações de configuração são automaticamente envidas por push para todos os agentes.  Para agentes do Linux, um arquivo de configuração é enviado para o coletor de dados Fluentd.  Se você quiser modificar esse arquivo manualmente em cada agente do Linux, desmarque a caixa *Aplicar as configurações abaixo aos computadores Linux* e siga a diretriz abaixo.
2. Digite o nome do contador na caixa de texto no formato *objeto(instâncias)\contador*.  Quando você começar a digitar, verá uma lista de correspondência dos contadores comuns.  Você pode selecionar um contador na lista ou digitar um dos seus.  
3. Clique em **+** ou pressione **Enter** para adicionar o contador à lista de outros contadores para o objeto.
4. Todos os contadores para um objeto usam o mesmo **Intervalo de Amostragem**.  O padrão é 10 segundos.  Você altera para um valor mais alto de até 1800 segundos (30 minutos) se desejar reduzir os requisitos de armazenamento dos dados de desempenho coletados.
5. Quando terminar de adicionar contadores, clique no botão **Salvar** na parte superior da tela para salvar a configuração.

#### <a name="configure-linux-performance-counters-in-configuration-file"></a>Configurar contadores de desempenho do Linux no arquivo de configuração
Em vez de configurar contadores de desempenho do Linux usando o portal do OMS, você tem a opção de editar arquivos de configuração no agente do Linux.  As métricas de desempenho a serem coletadas são controladas pela configuração em **/etc/opt/microsoft/omsagent/\<workspace id\>/conf/omsagent.conf**.

Cada objeto, ou categoria, de métricas de desempenho a ser coletado deve ser definido no arquivo de configuração como um único elemento `<source>` . A sintaxe segue o padrão abaixo.

    <source>
      type oms_omi  
      object_name "Processor"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 30s
    </source>


Os parâmetros usados com este comando são descritos na tabela a seguir.

| Parâmetros | Descrição |
|:--|:--|
| object\_name | O nome do objeto da coleção. |
| instance\_regex |  Uma *expressão regular* que define quais instâncias serão coletadas. O valor: `.*` especifica todas as instâncias. Para coletar métricas de processador somente para a instância \_Total, você poderia especificar `_Total`. Para coletar métricas de processador somente para a instância _Total, você poderia especificar: `(crond\|sshd)`. |
| counter\_name\_regex | Uma *expressão regular* que define os contadores (para o objeto) a serem coletados. Para coletar todos os contadores para o objeto, especifique: `.*`. Para coletar somente os contadores de espaço de permuta para o objeto de memória, por exemplo, você poderia especificar: `.+Swap.+` |
| intervalo | A frequência na qual os contadores do objeto são coletados. |


A tabela a seguir lista os objetos e contadores que você pode especificar no arquivo de configuração.  Há contadores adicionais disponíveis para certos aplicativos, conforme descrito em [Coletar contadores de desempenho para aplicativos Linux no Log Analytics](log-analytics-data-sources-linux-applications.md).

| Nome do Objeto | Nome do contador |
|:--|:--|
| Disco Lógico | % de Inodes livres |
| Disco Lógico | % de Espaço Livre |
| Disco Lógico | % de Inodes Usados |
| Disco Lógico | % de Espaço Usado |
| Disco Lógico | Bytes Lidos no Disco/s  |
| Disco Lógico | Leituras de Disco/s  |
| Disco Lógico | Transferências de Disco/s |
| Disco Lógico | Bytes Gravados no Disco/s |
| Disco Lógico | Gravações de Disco/s |
| Disco Lógico | Megabytes Livres |
| Disco Lógico | Bytes de Disco Lógico/s |
| Memória | % de Memória Disponível |
| Memória | % de Espaço de Permuta Disponível |
| Memória | % de Memória Usada |
| Memória | % de Espaço de Permuta Usado |
| Memória | MBytes de Memória Disponíveis |
| Memória | MBytes de Espaço de Permuta Disponíveis |
| Memória | Leituras de Página/s |
| Memória | Gravações de Página/s |
| Memória | Páginas/s |
| Memória | MBytes de Espaço de Permuta Usado |
| Memória | MBytes de Memória Usada |
| Rede | Total de Bytes Transmitidos |
| Rede | Total de Bytes Recebidos |
| Rede | Total de Bytes |
| Rede | Total de Pacotes Transmitidos |
| Rede | Total de Pacotes Recebidos |
| Rede | Total de Erros de Rx |
| Rede | Total de Erros de Tx |
| Rede | Total de Colisões |
| Disco Físico | Média de segundos/Leitura do Disco |
| Disco Físico | Média de segundos/Transferência do Disco |
| Disco Físico | Média de segundos/Gravação do Disco |
| Disco Físico | Bytes/s do Disco Físico |
| Processo | % de Tempo Privilegiado |
| Processo | % de Tempo do Usuário |
| Processo | KBytes de Memória Usada |
| Processo | Memória Virtual Compartilhada |
| Processador | % de Tempo de DPC |
| Processador | % de Tempo Ocioso |
| Processador | % de Tempo de Interrupção |
| Processador | % de Tempo de Espera de E/S |
| Processador | % de Tempo Adequado |
| Processador | % de Tempo Privilegiado |
| Processador | % Tempo do Processador |
| Processador | % de Tempo do Usuário |
| Sistema | Memória Física Livre |
| Sistema | Espaço Livre em Arquivos de Paginação |
| Sistema | Memória Virtual Livre |
| Sistema | Processos |
| Sistema | Tamanho Armazenado em Arquivos de Paginação |
| Sistema | Tempo de atividade |
| Sistema | Usuários |


A seguir está a configuração padrão para as métricas de desempenho.

    <source>
      type oms_omi
      object_name "Physical Disk"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 5m
    </source>

    <source>
      type oms_omi
      object_name "Logical Disk"
      instance_regex ".*
      counter_name_regex ".*"
      interval 5m
    </source>

    <source>
      type oms_omi
      object_name "Processor"
      instance_regex ".*
      counter_name_regex ".*"
      interval 30s
    </source>

    <source>
      type oms_omi
      object_name "Memory"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 30s
    </source>

## <a name="data-collection"></a>Coleta de dados
O Log Analytics coleta todos os contadores de desempenho especificados no seu intervalo de amostragem especificado em todos os agentes que têm o contador instalado.  Os dados não estão agregados e os dados brutos estão disponíveis em todas as exibições de pesquisa de log pela duração especificada por sua assinatura do OMS.

## <a name="performance-record-properties"></a>Propriedades do registro de desempenho
Os registros de desempenho têm um tipo de **Perf** e têm as propriedades na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| Computador |Computador do qual o evento foi coletado. |
| CounterName |Nome do contador de desempenho |
| CounterPath |Caminho completo do contador no formato \\\\\<Computador>\\objeto(instância)\\contador. |
| CounterValue |Valor numérico do contador. |
| InstanceName |Nome da instância do evento.  Vazio se não houver nenhuma instância. |
| ObjectName |Nome do objeto de desempenho |
| SourceSystem |Tipo de agente do qual os dados foram coletados. <br><br>OpsManager - agente do Windows, conexão direta ou SCOM <br> Linux: todos os agentes do Linux  <br> AzureStorage: Diagnóstico do Azure |
| TimeGenerated |Data e hora em que os dados foram amostrados. |

## <a name="sizing-estimates"></a>Estimativas de dimensionamento
 Uma estimativa aproximada de coleção de um determinado contador em intervalos de 10 segundos é cerca de 1 MB por dia por instância.  Você pode estimar os requisitos de armazenamento de um contador específico com a fórmula a seguir.

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-searches-with-performance-records"></a>Pesquisas de log com registros de desempenho
A tabela a seguir fornece diferentes exemplos de pesquisas de log que recuperam registros de desempenho.

| Consultar | Descrição |
|:--- |:--- |
| Type=Perf |Todos os dados de desempenho |
| Type=Perf Computer="MyComputer" |Todos os dados de desempenho de um computador específico |
| Type=Perf CounterName="Current Disk Queue Length" |Todos os dados de desempenho de um contador específico |
| Type=Perf (ObjectName=Processor) CounterName="% Processor Time" InstanceName=_Total &#124; measure Avg(Average) as AVGCPU by Computer |Utilização média da CPU em todos os computadores |
| Type=Perf (CounterName="% Processor Time") &#124; measure max(Max) by Computer |Utilização máxima da CPU em todos os computadores |
| Type=Perf ObjectName=LogicalDisk CounterName="Current Disk Queue Length" Computer="MyComputerName" &#124; measure Avg(Average) by InstanceName |Comprimento médio da fila de disco atual em todas as instâncias de um determinado computador |
| Type=Perf CounterName="DiskTransfers/sec" &#124; measure percentile95(Average) by Computer |95º percentil de transferências de disco/s em todos os computadores |
| Type=Perf CounterName="% Processor Time" InstanceName="_Total"  &#124; measure avg(CounterValue) by Computer Interval 1HOUR |Por hora média de utilização da CPU em todos os computadores |
| Type=Perf Computer="MyComputer" CounterName=%* InstanceName=_Total &#124; measure percentile70(CounterValue) by CounterName Interval 1HOUR |Percentil de 70 por hora de cada contador de porcentagem % para um computador específico |
| Type=Perf CounterName="% Processor Time" InstanceName="_Total"  (Computer="MyComputer") &#124; measure min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) by Computer Interval 1HOUR |Por hora média, mínima, máximo e percentil de 75 da CPU para um computador específico |
| Type=Perf ObjectName="MSSQL$INST2:Databases" InstanceName=master | Todos os dados de desempenho do objeto de desempenho de Banco de Dados para o banco de dados mestre da instância nomeada do SQL Server INST2.  

>[!NOTE]
> Se o seu espaço de trabalho fosse atualizado para a [nova linguagem de consulta do Log Analytics](log-analytics-log-search-upgrade.md), as consultas acima seriam alteradas para o demonstrado a seguir.

> | Consultar | Descrição |
|:--- |:--- |
| Perf |Todos os dados de desempenho |
| Perf &#124; where Computer == "MyComputer" |Todos os dados de desempenho de um computador específico |
| Perf &#124; where CounterName == "Current Disk Queue Length" |Todos os dados de desempenho de um contador específico |
| Perf &#124; where ObjectName == "Processor" and CounterName == "% Processor Time" and InstanceName == "_Total" &#124; summarize AVGCPU = avg(Average) by Computer |Utilização média da CPU em todos os computadores |
| Perf &#124; where CounterName == "% Processor Time" &#124; summarize AggregatedValue = max(Max) by Computer |Utilização máxima da CPU em todos os computadores |
| Perf &#124; where ObjectName == "LogicalDisk" and CounterName == "Current Disk Queue Length" and Computer == "MyComputerName" &#124; summarize AggregatedValue = avg(Average) by InstanceName |Comprimento médio da fila de disco atual em todas as instâncias de um determinado computador |
| Perf &#124; where CounterName == "DiskTransfers/sec" &#124; summarize AggregatedValue = percentile(Average, 95) by Computer |95º percentil de transferências de disco/s em todos os computadores |
| Perf &#124; where CounterName == "% Processor Time" and InstanceName == "_Total" &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), Computer |Por hora média de utilização da CPU em todos os computadores |
| Perf &#124; where Computer == "MyComputer" and CounterName startswith_cs "%" and InstanceName == "_Total" &#124; summarize AggregatedValue = percentile(CounterValue, 70) by bin(TimeGenerated, 1h), CounterName | Percentil de 70 por hora de cada contador de porcentagem % para um computador específico |
| Perf &#124; where CounterName == "% Processor Time" and InstanceName == "_Total" and Computer == "MyComputer" &#124; summarize ["min(CounterValue)"] = min(CounterValue), ["avg(CounterValue)"] = avg(CounterValue), ["percentile75(CounterValue)"] = percentile(CounterValue, 75), ["max(CounterValue)"] = max(CounterValue) by bin(TimeGenerated, 1h), Computer |Por hora média, mínima, máximo e percentil de 75 da CPU para um computador específico |
| Perf &#124; where ObjectName == "MSSQL$INST2:Databases" and InstanceName == "master" | Todos os dados de desempenho do objeto de desempenho de Banco de Dados para o banco de dados mestre da instância nomeada do SQL Server INST2.  

## <a name="viewing-performance-data"></a>Exibindo dados de desempenho
Quando você executa uma pesquisa de logs de dados de desempenho, a exibição **List** é mostrada por padrão.  Para exibir os dados em formato gráfico, clique em **Métricas**.  Para obter uma exibição gráfica detalhada, clique em **+** ao lado de um contador.  

![Exibição de métricas recolhida](media/log-analytics-data-sources-performance-counters/metricscollapsed.png)

Para agregar dados de desempenho em uma pesquisa de log, confira [Agregação métrica sob demanda e visualização de OMS](http://blogs.technet.microsoft.com/msoms/2016/02/26/on-demand-metric-aggregation-and-visualization-in-oms/).


## <a name="next-steps"></a>Próximas etapas
* [Colete contadores de desempenho de aplicativos Linux](log-analytics-data-sources-linux-applications.md), incluindo Apache HTTP Server e MySQL.
* Saiba mais sobre [pesquisas de log](log-analytics-log-searches.md) para analisar os dados coletados de fontes de dados e soluções.  
* Exporte os dados coletados para o [Power BI](log-analytics-powerbi.md) para análise e visualizações adicionais.
