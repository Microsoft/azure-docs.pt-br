---
title: Monitorando o ABS (serviço Blockchain do Azure)
description: Monitorando o serviço Blockchain do Azure por meio do Azure Monitor
ms.date: 01/08/2020
ms.topic: how-to
ms.reviewer: v-umha
ms.openlocfilehash: 05147f48c4cde4cc97bf6cc9cae5c8220a389ebd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594919"
---
# <a name="monitor-azure-blockchain-service-through-azure-monitor"></a>Monitorar o serviço Blockchain do Azure por meio do Azure Monitor  

À medida que os clientes executam cenários de blockchain de nível de produção no serviço de Blockchain do Azure (ABS), torna-se essencial monitorar os recursos de disponibilidade, desempenho e operações. Este artigo descreve os dados de monitoramento gerados pelo serviço Blockchain do Azure e como um pode usar os vários recursos e integrações de Azure Monitor para analisar e alertar, para gerenciar ambientes de nível de produção.  

## <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?

O serviço Blockchain do Azure cria dados de monitoramento usando o Azure Monitor, que é um serviço de monitoramento de pilha completo no Azure que fornece um conjunto completo de recursos para monitorar os recursos do Azure. Para obter mais informações sobre Azure Monitor, consulte [monitorando recursos do Azure com Azure monitor](../../azure-monitor/essentials/monitor-azure-resource.md).
 

As seções a seguir se baseiam neste artigo descrevendo os dados específicos coletados do serviço Blockchain do Azure e fornecendo exemplos para configurar a coleta de dados e analisar esses dados com as ferramentas do Azure.

## <a name="monitor-data-collected-from-azure-blockchain-service"></a>Monitorar dados coletados do serviço Blockchain do Azure  

O serviço Blockchain do Azure coleta o mesmo tipo de dados de monitoramento que outros recursos do Azure, que são descritos em [monitoramento de dados](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data) de recursos do Azure. Consulte [monitorar a referência de dados do serviço Blockchain do Azure](#monitor-azure-blockchain-service-data-reference) para obter uma referência detalhada dos logs e métricas criados pelo serviço Blockchain do Azure.

A página de visão geral no portal do Azure para cada recurso de membro do serviço Blockchain do Azure inclui uma breve exibição das transações, incluindo as solicitações manipuladas e processadas blocos. Alguns desses dados são coletados automaticamente e disponíveis para análise depois que você cria o recurso de membro do serviço Blockchain do Azure, enquanto você pode habilitar a coleta de dados adicional com configuração adicional.

## <a name="diagnostic-settings"></a>Configurações de Diagnóstico  

As métricas de plataforma e o log de atividades são coletados automaticamente, mas você precisa criar uma configuração de diagnóstico para coletar logs de recursos ou encaminhá-los para fora do Azure Monitor. Confira [Criar uma configuração de diagnóstico para coletar logs e métricas de plataforma no Azure](../../azure-monitor/essentials/diagnostic-settings.md) para obter o processo detalhado de criação de uma configuração de diagnóstico usando o portal do Azure, a CLI ou o PowerShell.

Ao criar uma configuração de diagnóstico, você especifica quais categorias de logs coletar. As categorias do serviço Blockchain do Azure estão listadas abaixo.

**Logs de proxy do Blockchain** – selecione a categoria se desejar monitorar os logs de proxy do NGNIX. Todos os detalhes da transação do cliente estão disponíveis para fins de auditoria e de depuração.  

**Logs de aplicativo do Blockchain** – selecione a categoria para obter os logs do aplicativo Blockchain hospedado pelo serviço gerenciado. Por exemplo, para um membro ABS-Quorum, esses logs seriam os logs do quorum em si.  

**Solicitações de métrica**: selecione a opção para coletar dados de métrica de Azure Cosmos DB para os destinos na configuração de diagnóstico, que é coletada automaticamente nas métricas do Azure. Coletar dados de métrica com logs de recursos para analisar os dois tipos de dados juntos e enviar dados de métricas fora do Azure Monitor.

## <a name="analyze-metric-data"></a>Analisar dados de métrica  

Você pode analisar as métricas do serviço Blockchain do Azure com o Metrics Explorer, navegar até a guia métricas na seção monitoramento na folha de recursos do ABS. Consulte [introdução ao Azure Metrics Explorer](../../azure-monitor/essentials/metrics-getting-started.md) para obter detalhes sobre como usar a ferramenta. As métricas completas para o serviço Blockchain do Azure estão no namespace métricas padrão do serviço Blockchain do Azure.

Você pode usar a dimensão de **nó** ao adicionar um filtro ou dividir as métricas, que basicamente fornece valores de métrica por nós de transação e nós de validador do membro ABS.

## <a name="analyze-log-data"></a>Analisar dados de log

Aqui estão algumas consultas que você pode inserir na barra de pesquisa de log para ajudá-lo a monitorar os membros do serviço Blockchain do Azure. Essas consultas funcionam com a [nova linguagem](../../azure-monitor/logs/log-query-overview.md).

Para consultar as condições de erro nos logs do aplicativo Blockchain, use a consulta abaixo:

```
BlockchainApplicationLog | where BlockchainMessage contains "ERROR" or BlockchainMessage contains "fatal"

```

Para consultar as condições de erro nos logs de proxy do Blockchain, use a consulta abaixo  


```
BlockchainProxyLog
| filter Code != 200
| limit 500

```
Você pode usar os filtros de tempo disponíveis nos logs do Azure para filtrar a consulta para um intervalo de tempo específico.

## <a name="monitor-azure-blockchain-service-data-reference"></a>Monitorar a referência de dados do serviço Blockchain do Azure  

Este artigo fornece uma referência de log e dados de métrica coletados para analisar o desempenho e a disponibilidade do serviço Blockchain do Azure.  

### <a name="resource-logs"></a>Logs de recursos

Todos os logs de recursos compartilham um esquema comum de nível superior com algumas propriedades exclusivas específicas ao serviço blockchain. Você pode consultar o artigo [esquema de logs de recursos de nível superior](../../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema). os detalhes das propriedades específicas do serviço Blockchain do Azure são abordados abaixo  

A tabela a seguir lista as propriedades dos logs do proxy Blockchain do Azure quando eles são coletados nos logs de Azure Monitor ou no armazenamento do Azure.  


| Nome da propriedade  | Descrição |
|:---|:---|
| time | A data e hora (UTC) em que a operação ocorreu. |
| Identificação  | O recurso do serviço Blockchain do Azure para o qual os logs estão habilitados.  |
| category  |Para o serviço Blockchain do Azure, os valores possíveis são **Proxylogs** e **Applicationlogs**. |
| operationName  | O nome da operação representada por esse evento.   |
| Nível de log  | Por padrão, o serviço Blockchain do Azure permite o nível de log **informativo** .   |
| NodeLocation  | Região do Azure onde o membro blockchain é implantado.  |
| BlockchainNodeName  | O nome do nó do membro do serviço Blockchain do Azure no qual a operação é executada.   |
| EthMethod  | O método, que é chamado pelo protocolo blockchain subjacente, no quorum, pode ser eth_sendTransactions, eth_getBlockByNumber etc.  |
| Agente  | O agente do usuário que está agindo em nome de um usuário, como o navegador da Web Mozilla, Edge etc. Exemplos dos valores são: "Mozilla/5.0 (Linux x64) node.js/8.16.0 V8/6.2.414.77"  |
| Código   | Códigos de erro HTTP. Geralmente, 4XX e 5XX são condições de erro.  |
| NodeHost  | O nome DNS do nó.   |
| RequestMethodName | O método HTTP chamado, os valores possíveis aqui são colocados para criar membro, obter para obter detalhes do membro existente, excluir para membro de exclusão, PATCH para atualizar o membro.   |
| BlockchainMemberName  | Nome do membro do serviço Blockchain do Azure fornecido pelo usuário.  |
| Consórcio | Nome do consórcio conforme fornecido pelo usuário.   |
| Remoto  | O IP do cliente em que a solicitação está chegando.  |
| RequestSize  | Tamanho da solicitação feita em bytes.  |
| Requesttime  | A duração da solicitação em milissegundos.|




A tabela a seguir lista as propriedades dos logs do aplicativo Blockchain do Azure.


| Nome da propriedade  | Descrição |
|:---|:---|
| time | A data e hora (UTC) em que a operação ocorreu. |
| Identificação  | O recurso do serviço Blockchain do Azure para o qual os logs estão habilitados.|
| category  |Para o serviço Blockchain do Azure, o valor possível são **Proxylogs** e **Applicationlogs**.  |
| operationName  | O nome da operação representada por esse evento.   |
| Nível de log  | Por padrão, o serviço Blockchain do Azure permite o nível de log **informativo** .   |
| NodeLocation  | Região do Azure onde o membro blockchain é implantado.  |
| BlockchainNodeName  | O nome do nó do membro do serviço Blockchain do Azure no qual a operação é executada.   |
| BlockchainMessage    | Esse campo conterá o log do aplicativo Blockchain que são os logs simples de dados. Para o quorum de ABS, isso teria logs de quorum. Ele tem informações sobre o tipo de entrada de log que é informativo, erro, aviso e uma cadeia de caracteres que fornece mais informações sobre a ação executada.   |
| TenantID    | O locatário específico da região do serviço Blockchain do Azure. O formato deste campo é https://westlake-rp-prod . <region> . cloudapp.azure.com em que Region especifica a região do Azure do membro implantado.       |
| SourceSystem   | O sistema popula os logs, neste caso é o **Azure**.    |



### <a name="metrics"></a>Métricas

As tabelas a seguir listam as métricas de plataforma coletadas para o serviço Blockchain do Azure. Todas as métricas são armazenadas nas métricas padrão do serviço de namespace **Blockchain do Azure** .

Para obter uma lista de todas as Azure Monitor métricas com suporte (incluindo o serviço Blockchain do Azure), consulte [Azure monitor métricas com suporte](../../azure-monitor/essentials/metrics-supported.md).

### <a name="blockchain-metrics"></a>Métricas de Blockchain

A tabela a seguir especifica a lista de métricas Blockchain que são coletadas para o recurso de membro do serviço Blockchain do Azure.


| Nome da métrica | Unidade  |  Tipo de agregação| Descrição   |
|---|---|---|---|
| Transações Pendentes   | Contagem  |  Média | O número de transações que estão aguardando para serem extraídas.   |
| Blocos Processados   | Contagem  | Soma  |  O número de blocos processados em cada intervalo de tempo. Atualmente, o tamanho do bloco é de 5 segundos, portanto, em um minuto, cada nó processará 12 blocos e 60 blocos em 5 minutos.   |
|Transações Processadas    | Contagem  | Soma  | O número de transações processadas em um bloco.    |
|Transações em Fila    |  Contagem | Média  | O número de transações que não podem ser imediatamente extraídas. Pode ser porque eles chegaram fora de ordem e o futuro está aguardando a chegada da transação anterior. Ou, pode ser que duas transações tenham o mesmo número usado apenas uma vez (nonce) e o mesmo valor de gás, portanto, o segundo não pode ser minado.   |

### <a name="connection-metrics"></a>Métricas de conexão  

A tabela a seguir lista as diferentes métricas de conexão que são coletadas para o recurso de membro do serviço Blockchain do Azure. Essas são as métricas de proxy NGINX.


| Nome da métrica | Unidade  |  Tipo de agregação| Descrição |
|---|---|---|---|
| Conexões Aceitas   | Contagem  |  Soma | O número total de conexões de cliente aceitas.   |
| Conexões ativas  | Contagem  | Média  |  O número atual de conexões de cliente ativas, incluindo a espera de conexões.    |
|Conexões Manipuladas    | Contagem  | Soma  | O número total de conexões manipuladas. Em geral, o valor do parâmetro é o mesmo que as conexões aceitas, a menos que alguns limites de recursos tenham sido atingidos.     |
|Solicitações Manipuladas     |  Contagem | Soma  | O número total de solicitações do cliente.  |


### <a name="performance-metrics"></a>Métricas de desempenho

A tabela a seguir lista as métricas de desempenho que são coletadas para cada um dos nós do recurso de membro do Azure Blockchain.  


| Nome da métrica | Unidade  |  Tipo de agregação| Descrição   |
|---|---|---|---|
| Porcentagem de uso da CPU   | Percentual  |  Máx | A porcentagem do uso da CPU.     |
| Bytes de Leitura de E/S   | Quilobytes   | Soma  |  A soma de bytes de leitura de e/s em todos os nós do recurso de membro blockchain.      |
|Bytes de Gravação de E/S     | Quilobytes   | Soma  | A soma da e/s grava bytes em todos os nós do recurso de membro blockchain.     |
|Limite de Memória       |  Gigabytes   | Média    | Memória máxima disponível para o processo blockchain por nó. |
|Uso de Memória     | Gigabytes  |  Média | A quantidade de memória usada na média em todos os nós.  |
| Percentual de Uso de Memória     | Percentual   | Média  |  A porcentagem da memória usada na média em todos os nós.       |
|Uso de Armazenamento      | Gigabytes   | Média  | Os GB de armazenamento usados são medidos em média em todos os nós.       |


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [Blockchain Gerenciador de dados](./data-manager.md) para capturar e transformar dados do Blockchain na grade de eventos do Azure.
