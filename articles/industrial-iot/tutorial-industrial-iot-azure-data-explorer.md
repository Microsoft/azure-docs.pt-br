---
title: Efetuar pull de dados de IoT Industrial do Azure no ADX
description: Neste tutorial, você aprenderá a efetuar pull de dados de IIoT no ADX.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 4c344dc09ad6c8aa4b2aa431952fc271d946b60d
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787171"
---
# <a name="tutorial-pull-azure-industrial-iot-data-into-adx"></a>Tutorial: Efetuar pull de dados de IoT Industrial do Azure no ADX

A plataforma IIoT (IoT Industrial) do Azure combina módulos de borda e microsserviços de nuvem com vários serviços de PaaS do Azure para fornecer funcionalidades para a descoberta de ativos industriais e coletar dados desses ativos usando o OPC UA. O [ADX (Azure Data Explorer)](https://docs.microsoft.com/azure/data-explorer) é um destino natural para dados de IIoT com recursos de análise de dados que permite a execução de consultas flexíveis nos dados ingeridos dos servidores OPC UA conectados ao Hub IoT por meio do OPC Publisher. Embora um cluster do ADX possa ingerir dados diretamente do Hub IoT, a plataforma IIoT faz o processamento adicional dos dados para torná-los mais úteis antes de colocá-los no Hub de Eventos fornecido quando uma implantação completa dos microsserviços é usada (veja a arquitetura da plataforma IIoT).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma tabela no ADX
> * Conectar o Hub de Eventos ao cluster do ADX
> * Analisar os dados no ADX

## <a name="how-to-make-the-data-available-in-the-adx-cluster-to-query-it-effectively"></a>Como disponibilizar os dados no cluster do ADX para consultá-los com eficiência 

Se examinarmos o formato da mensagem do Hub de Eventos (conforme definido pela classe Microsoft.Azure.IIoT.OpcUa.Subscriber.Models.MonitoredItemMessageModel), poderemos ver uma dica para a estrutura de que precisamos para o esquema de tabela do ADX.

![Estrutura](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-1.png)

Veja abaixo as etapas necessárias para disponibilizar os dados no cluster do ADX e consultar os dados com eficiência.  
1. Crie um cluster do ADX. Se você ainda não tem um cluster do ADX provisionado com a plataforma IIoT ou se deseja usar outro cluster, siga as etapas descritas [aqui](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal#create-a-cluster). 
2. Habilite a ingestão de streaming no cluster do ADX, conforme explicado [aqui](https://docs.microsoft.com/azure/data-explorer/ingest-data-streaming#enable-streaming-ingestion-on-your-cluster). 
3. Crie um banco de dados do ADX seguindo as etapas descritas [aqui](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal#create-a-database).

Para a etapa a seguir, usaremos a [interface da Web do ADX](https://docs.microsoft.com/azure/data-explorer/web-query-data) para executar as consultas necessárias. Lembre-se de adicionar o cluster à interface da Web, conforme explicado no link.  
 
4. Crie uma tabela no ADX para colocar os dados ingeridos.  Embora a classe MonitoredItemMessageModel possa ser usada para definir o esquema da tabela do ADX, recomendamos ingerir os dados primeiro em uma tabela de preparo com uma coluna do tipo [Dinâmico](https://docs.microsoft.com/azure/data-explorer/kusto/query/scalar-data-types/dynamic). Isso nos dá mais flexibilidade para lidar com os dados e o processamento em outras tabelas (potencialmente, combinando-as com outras fontes de dados) que atendem às necessidades de vários casos de uso. A seguinte consulta do ADX cria a tabela de preparo ‘iiot_stage’ com uma coluna ‘payload’:

```
.create table ['iiot_stage']  (['payload']:dynamic)
```

Também precisamos adicionar um mapeamento de ingestão de JSON para instruir o cluster a colocar toda a mensagem JSON do Hub de Eventos na tabela de preparo:

```
.create table ['iiot_stage'] ingestion json mapping 'iiot_stage_mapping' '[{"column":"payload","path":"$","datatype":"dynamic"}]'
```

5. Nossa tabela agora está pronta para receber dados do Hub de Eventos. 
6. Use as instruções descritas [aqui](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub#connect-to-the-event-hub) para conectar o Hub de Eventos ao cluster do ADX e começar a ingerir os dados na tabela de preparo. Só precisamos criar a conexão, pois já temos um Hub de Eventos provisionado pela plataforma IIoT.  
7. Depois que a conexão for verificada, os dados começarão a fluir para a tabela e, após um pequeno atraso, poderemos começar a examinar os dados na tabela. Use a consulta a seguir na interface da Web do ADX para examinar uma amostra de dados de 10 linhas. Podemos ver aqui como os dados no conteúdo se assemelham à classe MonitoredItemMessageModel mencionada anteriormente.

![Consulta](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-2.png)

8. Agora, vamos executar uma análise desses dados analisando os Dados Dinâmicos na coluna ‘payload’ diretamente. Neste exemplo, calcularemos a média da telemetria identificada por “DisplayName”: “PositiveTrendData”, em janelas de tempo de 10 minutos, em todos os registros ingeridos desde um ponto de tempo específico (definido pela variável min_t) let min_t = datetime(2020-10-23); iiot_stage | where todatetime(payload.Timestamp) > min_t | where tostring(payload.DisplayName)== 'PositiveTrendData' | summarize event_avg = avg(todouble(payload.Value)) by bin(todatetime(payload.Timestamp), 10 m)
 
Como nossa coluna ‘payload’ contém um tipo de Dados Dinâmicos, precisamos executar a conversão de dados no momento da consulta para que nossos cálculos sejam executados nos tipos de dados corretos.

![Carimbo de data/hora de conteúdo](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-3.png)

Como mencionamos anteriormente, a ingestão dos dados do OPC UA em uma tabela de preparo com uma coluna ‘Dinâmica’ nos dá flexibilidade. No entanto, a necessidade de executar conversões de tipo de dados no tempo de consulta pode resultar em atrasos na execução das consultas, especialmente se o volume de dados é grande e se há muitas consultas simultâneas. Neste estágio, podemos criar outra tabela com os tipos de dados já determinados, para evitarmos conversões de tipo de dados no tempo de consulta.
 
9. Crie uma tabela para os dados analisados que consistam em uma seleção limitada do conteúdo do ‘payload’ dinâmico na tabela de preparo. Observe que criamos uma coluna de valor para cada um dos tipos de dados esperados na telemetria.

```
.create table ['iiot_parsed']  
    (['Tag_Timestamp']: datetime ,  
    ['Tag_PublisherId']:string ,  
    ['Tag']:string ,
    ['Tag_Datatype']:string ,  
    ['Tag_NodeId']:string,  
    ['Tag_value_long']:long ,  
    ['Tag_value_double']:double,  
    ['Tag_value_boolean']:bool)
```

10. Crie uma função (no nível de banco de dados) para projetar os dados necessários da tabela de preparo. Aqui, selecionamos os itens ‘Timestamp’, ‘PublisherId’, ‘DisplayName’, ‘Datatype’ e ‘NodeId’ da coluna ‘payload’ e os projetamos como ‘Tag_Timestamp’, ‘Tag_PublisherId’, ‘Tag’, ‘Tag_Datatype’ e ‘Tag_NodeId’. O item ‘Value’ é projetado como três partes diferentes com base no ‘DataType’.

```
.create-or-alter function fn_InflightParseIIoTEvent()
{
iiot_stage
| extend Tag_Timestamp =  todatetime(payload.Timestamp)
| extend Tag_PublisherId = tostring(payload.PublisherId)
| extend Tag = tostring(payload.DisplayName)
| extend Tag_Datatype = tostring(payload.DataType)
| extend Tag_NodeId = tostring(payload.NodeId)
| extend Tag_value_long = case(Tag_Datatype == "Int64", tolong(payload.Value), long(null))
| extend Tag_value_double = case(Tag_Datatype == "Double", todouble(payload.Value), double(null))
| extend Tag_value_boolean = case(Tag_Datatype == "Boolean", tobool(payload.Value), bool(null))
| project Tag_Timestamp, Tag_PublisherId, Tag, Tag_Datatype, Tag_NodeId, Tag_value_long, Tag_value_double, Tag_value_boolean
}
```

Para obter mais informações sobre como mapear tipos de dados no ADX, acesse [aqui](https://docs.microsoft.com/azure/data-explorer/kusto/query/scalar-data-types/dynamic), e para obter as funções do ADX, comece [aqui](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/stored-functions).
 
11. Aplique a função da etapa anterior à tabela analisada usando uma política de atualização. A [política](https://docs.microsoft.com/azure/data-explorer/kusto/management/updatepolicy) de atualização instrui o ADX a acrescentar automaticamente dados a uma tabela de destino sempre que novos dados são inseridos na tabela de origem, com base em uma consulta de transformação executada nos dados inseridos na tabela de origem. Podemos usar a consulta a seguir para atribuir a tabela analisada como o destino e a tabela de preparo como a origem da política de atualização definida pela função que criamos na etapa anterior.

```
.alter table iiot_parsed policy update
@'[{"IsEnabled": true, "Source": "iiot_stage", "Query": "fn_InflightParseIIoTEvent()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
```

Assim que a consulta acima for executada, os dados começarão a fluir e preencher a tabela de destino ‘iiot_parsed’. Podemos examinar os dados em ‘iiot_parsed’, conforme mostrado a seguir.

![Tabela analisada](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-4.png)

12. Vamos ver agora como podemos repetir a análise que fizemos em uma etapa anterior. Calcule a média da telemetria identificada por “DisplayName”: “PositiveTrendData”, em janelas de tempo de 10 minutos, em todos os registros ingeridos desde um ponto de tempo específico (definido pela variável min_t). Como agora temos os valores da marca ‘PositveTrendData’ armazenados em uma coluna de tipo de dados double, esperamos um aprimoramento no desempenho da consulta.

![Repetir a análise](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-5.png)

13. Finalmente, vamos comparar o desempenho da consulta em ambos os casos. Podemos encontrar o tempo necessário para executar a consulta usando a opção ‘Estatísticas’ na interface do usuário do ADX (que pode estar localizada acima dos resultados da consulta).  

![Desempenho da consulta 1](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-6.png)

![Desempenho da consulta 2](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-7.png)

Podemos ver que a consulta que usa a tabela analisada é aproximadamente duas vezes mais rápida que a tabela de preparo. Neste exemplo, temos um pequeno conjunto de dados e não há consultas simultâneas em execução. Portanto, o efeito no tempo de execução da consulta não é grande; no entanto, para uma carga de trabalho realista, há um grande impacto no desempenho. É por isso que é importante considerar a separação dos diferentes tipos de dados em colunas diferentes.

> [!NOTE] 
> A política de atualização só funciona nos dados ingeridos na tabela de preparo depois que a política foi configurada e não se aplica aos dados pré-existentes. Isso precisa ser levado em consideração quando, por exemplo, precisamos alterar a política de atualização. Encontre os detalhes completos na documentação do ADX.

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu a alterar os valores padrão da configuração, poderá 

> [!div class="nextstepaction"]
> [Configurar componentes da IoT Industrial](tutorial-configure-industrial-iot-components.md)

> [!div class="nextstepaction"]
> [Visualizar e analisar os dados usando o Time Series Insights](tutorial-visualize-data-time-series-insights.md)