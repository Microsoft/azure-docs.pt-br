---
title: Coletar fontes de dados de desempenho do Windows e do Linux com Log Analytics Agent no Azure Monitor
description: Os contadores de desempenho são coletados pelo Azure Monitor para analisar o desempenho em agentes do Windows e do Linux.  Este artigo descreve como configurar a coleta de contadores de desempenho para agentes do Linux e do Windows, cujos detalhes são armazenados no workspace, e como analisá-los no portal do Azure.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/26/2021
ms.openlocfilehash: f4bddc1666d1165d6a1e4c749fdbc96ede37747a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102036752"
---
# <a name="collect-windows-and-linux-performance-data-sources-with-log-analytics-agent"></a>Coletar fontes de dados de desempenho do Windows e do Linux com o agente de Log Analytics
Os contadores de desempenho no Windows e Linux fornecem informações sobre o desempenho de componentes de hardware, sistemas operacionais e aplicativos.  Azure Monitor pode coletar contadores de desempenho de Log Analytics agentes em intervalos frequentes para análise NRT (quase em tempo real), além de agregar dados de desempenho para análise e relatórios de longo prazo.

> [!IMPORTANT]
> Este artigo aborda a coleta de dados de desempenho com o [agente de log Analytics](./log-analytics-agent.md) , que é um dos agentes usados pelo Azure monitor. Outros agentes coletam dados diferentes e são configurados de forma diferente. Consulte [visão geral dos agentes de Azure monitor](../agents/agents-overview.md) para obter uma lista dos agentes disponíveis e os dados que eles podem coletar.

![Contadores de desempenho](media/data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Configurando os contadores de desempenho
Configure contadores de desempenho no [menu de configuração de agentes](../agents/agent-data-sources.md#configuring-data-sources) para o espaço de trabalho log Analytics.

Ao configurar os contadores de desempenho do Windows ou do Linux para um novo workspace pela primeira vez, você terá a opção de criar rapidamente vários contadores comuns.  Eles são listados com uma caixa de seleção ao lado de cada um.  Garanta que todos os contadores que deseja criar inicialmente estejam marcados e clique em **Add the selected performance counters**(Adicionar os contadores de desempenho selecionados).

Para os contadores de desempenho do Windows, você pode escolher uma instância específica para cada contador de desempenho. Para os contadores de desempenho do Linux, a instância de cada contador escolhido se aplicará a todos os contadores filhos do contador pai. A tabela a seguir mostra as instâncias comuns disponíveis para os contadores de desempenho do Linux e do Windows.

| Nome da instância | Descrição |
| --- | --- |
| \_Total |Total de todas as instâncias |
| \* |Todas as instâncias |
| (/&#124;/var) |Corresponde às instâncias chamadas: / ou /var |

### <a name="windows-performance-counters"></a>Contadores de desempenho do Windows

[![Configurar contadores de desempenho do Windows](media/data-sources-performance-counters/configure-windows.png)](media/data-sources-performance-counters/configure-windows.png#lightbox)

Siga este procedimento para adicionar um novo contador de desempenho do Windows para coletar. Observe que não há suporte para os contadores de desempenho do Windows v2.

1. Clique em **Adicionar contador de desempenho**.
2. Digite o nome do contador na caixa de texto no formato *objeto(instâncias)\contador*.  Quando você começar a digitar, verá uma lista de correspondência dos contadores comuns.  Você pode selecionar um contador na lista ou digitar um dos seus.  Você também pode retornar todas as instâncias de um determinado contador especificando *objeto\contador*.  

    Durante a coleta de contadores de desempenho do SQL Server de instâncias nomeadas, todos os contadores de instância nomeados começam com *MSSQL$* seguidos do nome da instância.  Por exemplo, para coletar o contador de Proporção de Ocorrência no Cache de Log para todos os bancos de dados do objeto de desempenho de Banco de Dados para a instância nomeada do SQL INST2, especificar `MSSQL$INST2:Databases(*)\Log Cache Hit Ratio`.

4. Quando você adicionar um contador, ele usa o padrão de 10 segundos para seu **Intervalo de Amostragem**.  Você poderá alterar isso para um valor mais alto de até 1800 segundos (30 minutos) se desejar reduzir os requisitos de armazenamento dos dados de desempenho coletados.
5. Quando você terminar de adicionar contadores, clique no botão **aplicar** na parte superior da tela para salvar a configuração.

### <a name="linux-performance-counters"></a>Contadores de desempenho do Linux

[![Configurar contadores de desempenho do Linux](media/data-sources-performance-counters/configure-linux.png)](media/data-sources-performance-counters/configure-linux.png#lightbox)

Siga este procedimento para adicionar um novo contador de desempenho do Linux para coletar.

1. Clique em **Adicionar contador de desempenho**.
1. Digite o nome do contador na caixa de texto no formato *objeto(instâncias)\contador*.  Quando você começar a digitar, verá uma lista de correspondência dos contadores comuns.  Você pode selecionar um contador na lista ou digitar um dos seus.  
1. Todos os contadores para um objeto usam o mesmo **Intervalo de Amostragem**.  O padrão é 10 segundos.  Você altera para um valor mais alto de até 1800 segundos (30 minutos) se desejar reduzir os requisitos de armazenamento dos dados de desempenho coletados.
1. Quando você terminar de adicionar contadores, clique no botão **aplicar** na parte superior da tela para salvar a configuração.

#### <a name="configure-linux-performance-counters-in-configuration-file"></a>Configurar contadores de desempenho do Linux no arquivo de configuração
Em vez de configurar contadores de desempenho do Linux usando o portal do Azure, existe a opção de editar arquivos de configuração no agente do Linux.  As métricas de desempenho a serem coletadas são controladas pela configuração no **/etc/opt/Microsoft/omsagent/ \<workspace id\> /conf/omsagent.conf**.

Cada objeto, ou categoria, de métricas de desempenho a ser coletado deve ser definido no arquivo de configuração como um único elemento `<source>` . A sintaxe segue o padrão abaixo.

```xml
<source>
    type oms_omi  
    object_name "Processor"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 30s
</source>
```


Os parâmetros usados com este comando são descritos na tabela a seguir.

| Parâmetros | Descrição |
|:--|:--|
| object\_name | O nome do objeto da coleção. |
| instance\_regex |  Uma *expressão regular* que define quais instâncias serão coletadas. O valor: `.*` especifica todas as instâncias. Para coletar métricas de processador somente para a instância \_Total, você poderia especificar `_Total`. Para coletar métricas de processador somente para a instância _Total, você poderia especificar: `(crond\|sshd)`. |
| counter\_name\_regex | Uma *expressão regular* que define os contadores (para o objeto) a serem coletados. Para coletar todos os contadores para o objeto, especifique: `.*`. Para coletar somente os contadores de espaço de permuta para o objeto de memória, por exemplo, você poderia especificar: `.+Swap.+` |
| intervalo | A frequência na qual os contadores do objeto são coletados. |


A tabela a seguir lista os objetos e contadores que você pode especificar no arquivo de configuração.  Há contadores adicionais disponíveis para alguns aplicativos, conforme descrito em [Coletar contadores de desempenho para aplicativos do Linux no Azure Monitor](data-sources-linux-applications.md).

| Nome do Objeto | Nome do contador |
|:--|:--|
| Disco Lógico | % de Inodes livres |
| Disco Lógico | % de Espaço Livre |
| Disco Lógico | % de Inodes Usados |
| Disco Lógico | % de Espaço Usado |
| Disco Lógico | Bytes Lidos no Disco/s |
| Disco Lógico | Leituras de Disco/s |
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

```xml
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
    instance_regex ".*"
    counter_name_regex ".*"
    interval 5m
</source>

<source>
    type oms_omi
    object_name "Processor"
    instance_regex ".*"
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
```

## <a name="data-collection"></a>Coleta de dados
O Azure Monitor coleta todos os contadores de desempenho especificados em seu intervalo de amostragem especificado em todos os agentes que têm o contador instalado.  Os dados não são agregados e os dados brutos estão disponíveis em todas as exibições de consulta de log durante a duração especificada pelo seu espaço de trabalho do log Analytics.

## <a name="performance-record-properties"></a>Propriedades do registro de desempenho
Os registros de desempenho têm um tipo de **Perf** e têm as propriedades na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| Computador |Computador do qual o evento foi coletado. |
| CounterName |Nome do contador de desempenho |
| CounterPath |Caminho completo do contador no \\ \\ \<Computer> \\ contador objeto (instância) do formulário \\ . |
| CounterValue |Valor numérico do contador. |
| InstanceName |Nome da instância do evento.  Vazio se não houver nenhuma instância. |
| ObjectName |Nome do objeto de desempenho |
| SourceSystem |Tipo de agente do qual os dados foram coletados. <br><br>OpsManager - agente do Windows, conexão direta ou SCOM <br>  Linux: todos os agentes do Linux  <br>  AzureStorage: Diagnóstico do Azure |
| TimeGenerated |Data e hora em que os dados foram amostrados. |

## <a name="sizing-estimates"></a>Estimativas de dimensionamento
 Uma estimativa aproximada de coleção de um determinado contador em intervalos de 10 segundos é cerca de 1 MB por dia por instância.  Você pode estimar os requisitos de armazenamento de um contador específico com a fórmula a seguir.

> 1 MB x (número de contadores) x (número de agentes) x (número de instâncias)

## <a name="log-queries-with-performance-records"></a>Consultas de log com registros de Desempenho
A tabela a seguir fornece diferentes exemplos de consultas de log que recuperam registros de Desempenho.

| Consulta | Descrição |
|:--- |:--- |
| Perf |Todos os dados de desempenho |
| Perf &#124; where Computer == "MyComputer" |Todos os dados de desempenho de um computador específico |
| Perf &#124; where CounterName == "Current Disk Queue Length" |Todos os dados de desempenho de um contador específico |
| Perf &#124; em que ObjectName = = "Processor" e CounterName = = "% Processor Time" e InstanceName = = "_Total" &#124; resume AVGCPU = AVG (comvalue) por computador |Utilização média da CPU em todos os computadores |
| Perf &#124; em que CounterName = = "% Processor Time" &#124; resume AggregatedValue = Max (comvalue) por computador |Utilização máxima da CPU em todos os computadores |
| Perf &#124; em que ObjectName = = "LogicalDisk" e CounterName = = "comprimento atual da fila do disco" e computador = = "mycomputername" &#124; resumir AggregatedValue = AVG (comvalue) por InstanceName |Comprimento médio da fila de disco atual em todas as instâncias de um determinado computador |
| Perf &#124; em que CounterName = = "transferências de disco/s" &#124; resumir AggregatedValue = percentil (comvalue, 95) por computador |95º percentil de transferências de disco/s em todos os computadores |
| Perf &#124; where CounterName == "% Processor Time" and InstanceName == "_Total" &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), Computer |Por hora média de utilização da CPU em todos os computadores |
| Perf &#124; where Computer == "MyComputer" and CounterName startswith_cs "%" and InstanceName == "_Total" &#124; summarize AggregatedValue = percentile(CounterValue, 70) by bin(TimeGenerated, 1h), CounterName | Percentil de 70 por hora de cada contador de porcentagem % para um computador específico |
| Perf &#124; where CounterName == "% Processor Time" and InstanceName == "_Total" and Computer == "MyComputer" &#124; summarize ["min(CounterValue)"] = min(CounterValue), ["avg(CounterValue)"] = avg(CounterValue), ["percentile75(CounterValue)"] = percentile(CounterValue, 75), ["max(CounterValue)"] = max(CounterValue) by bin(TimeGenerated, 1h), Computer |Por hora média, mínima, máximo e percentil de 75 da CPU para um computador específico |
| Perf &#124; where ObjectName == "MSSQL$INST2:Databases" and InstanceName == "master" | Todos os dados de desempenho do objeto de desempenho de Banco de Dados para o banco de dados mestre da instância nomeada do SQL Server INST2.  




## <a name="next-steps"></a>Próximas etapas
* [Colete contadores de desempenho de aplicativos Linux](data-sources-linux-applications.md), incluindo Apache HTTP Server e MySQL.
* Saiba mais sobre [registrar consultas](../logs/log-query-overview.md) para analisar os dados coletados de fontes de dados e soluções.  
* Exporte os dados coletados para o [Power BI](../visualize/powerbi.md) para análise e visualizações adicionais.