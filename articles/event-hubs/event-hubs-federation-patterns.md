---
title: Padrões de tarefa de replicação de evento – hubs de eventos do Azure | Microsoft Docs
description: Este artigo fornece diretrizes detalhadas para a implementação de padrões de tarefa de replicação de eventos específicos
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: 438964c228f060dede93abf582c9504b698db8b0
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934604"
---
# <a name="event-replication-tasks-patterns"></a>Padrões de tarefas de replicação de eventos

As visão geral da [Federação](event-hubs-federation-overview.md) e as [funções do replicador](event-hubs-federation-replicator-functions.md) explicam a lógica para os elementos básicos das tarefas de replicação, e é recomendável que você se familiarize com eles antes de continuar com este artigo.

Neste artigo, detalharemos as diretrizes de implementação para vários dos padrões realçados na seção visão geral.

## <a name="replication"></a>Replicação

O padrão de replicação copia eventos de um hub de eventos para o próximo ou de um hub de eventos para outro destino, como uma fila do barramento de serviço. Os eventos são encaminhados sem fazer modificações no conteúdo do evento.

A implementação desse padrão é coberta pela replicação de [eventos entre os hubs de eventos](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy) e a replicação de eventos entre os exemplos de [hubs de eventos e do barramento de serviço](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus) e a [utilização Apache Kafka MirrorMaker com](event-hubs-kafka-mirror-maker-tutorial.md) o tutorial de hubs de eventos para o caso específico de replicar dados de um Apache Kafka Broker para os hubs de eventos.

### <a name="streams-and-order-preservation"></a>Fluxos e preservação de pedidos

A replicação, seja por meio de Azure Functions ou Azure Stream Analytics, não visa garantir a criação de clones exatos de 1:1 de um hub de eventos de origem em um hub de eventos de destino, mas concentra-se em preservar a ordem relativa de eventos em que o aplicativo exige isso. O aplicativo comunica isso agrupando eventos relacionados com a mesma chave de partição e os [hubs de eventos organizam as mensagens com a mesma chave de partição sequencialmente na mesma partição](event-hubs-features.md#partitions).

> [!IMPORTANT] 
> As informações de "deslocamento" são exclusivas para cada Hub de eventos e os deslocamentos para os mesmos eventos serão diferentes nas instâncias do hub de eventos. Para localizar uma posição em um fluxo de eventos copiado, use deslocamentos baseados em tempo e consulte os [metadados propagados atribuídos pelo serviço](#service-assigned-metadata).
>
> Os deslocamentos com base no tempo iniciam o receptor em um ponto específico no tempo:
> - *EventPosition. FromStart ()* -ler todos os dados retidos novamente.
> - *EventPosition. deextremidade ()* -ler todos os novos dados da hora da conexão.
> - *EventPosition. FromEnqueuedTime (DateTime)* -todos os dados que começam de uma determinada data e hora.
>
> No EventProcessor, você define a posição por meio de InitialOffsetProvider no EventProcessorOptions. Com as outras APIs do destinatário, a posição é passada pelo construtor. 


Os auxiliares da função de replicação pré-criados [fornecidos como exemplos](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) que são usados nas diretrizes baseadas em Azure Functions garantem que os fluxos de eventos com a mesma chave de partição recuperada de uma partição de origem sejam enviados para o Hub de eventos de destino como um lote no fluxo original e com a mesma chave de partição.

Se a contagem de partições do hub de eventos de origem e de destino for idêntica, todos os fluxos no destino serão mapeados para as mesmas partições que faziam na origem.
Se a contagem de partições for diferente, que é importante para alguns dos padrões descritos a seguir, o mapeamento será diferente, mas os fluxos serão sempre mantidos juntos e em ordem.

A ordem relativa de eventos que pertencem a fluxos diferentes ou de eventos independentes sem uma chave de partição em uma partição de destino pode ser sempre diferente da partição de origem.

### <a name="service-assigned-metadata"></a>Metadados atribuídos ao serviço

Os metadados atribuídos ao serviço de um evento obtido do hub de eventos de origem, a hora de enfileiramento original, o número de sequência e o deslocamento, são substituídos por novos valores atribuídos ao serviço no Hub de eventos de destino, mas com as [funções auxiliares](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication), tarefas de replicação fornecidas em nossos exemplos, os valores originais são preservados nas propriedades do usuário: `repl-enqueue-time` (ISO8601 String), `repl-sequence` `repl-offset`

Essas propriedades são do tipo cadeia de caracteres e contêm o valor em cadeias das respectivas propriedades originais. Se o evento for encaminhado várias vezes, os metadados atribuídos ao serviço da fonte imediata serão anexados às propriedades já existentes, com valores separados por ponto e vírgula.

### <a name="failover"></a>Failover

Se você estiver usando a replicação para fins de recuperação de desastre, para proteger os eventos de disponibilidade regionais no serviço de hubs de eventos ou contra interrupções de rede, qualquer cenário de falha desse tipo exigirá a execução de um failover de um hub de eventos para o próximo, informando produtores e/ou consumidores para usar o ponto de extremidade secundário.

Para todos os cenários de failover, supõe-se que os elementos necessários dos namespaces sejam estruturalmente idênticos, o que significa que os hubs de eventos e os grupos de consumidores são nomeados de forma idêntica e que as regras de assinatura de acesso compartilhado e/ou as regras de controle de acesso baseado em função são configuradas da mesma maneira. Você pode criar (e atualizar) um namespace secundário seguindo as [diretrizes para mover namespaces](move-across-regions.md) e omitir a etapa de limpeza.

Para forçar produtores e consumidores a mudarem, você precisará fazer as informações sobre qual namespace usar disponível para pesquisa em um local que seja fácil de acessar e atualizar. Se produtores ou consumidores encontrarem erros frequentes ou persistentes, eles deverão consultar esse local e ajustar sua configuração. Há várias maneiras de compartilhar essa configuração, mas indicamos dois nos seguintes itens: DNS e compartilhamentos de arquivos.

#### <a name="dns-based-failover-configuration"></a>Configuração de failover baseado em DNS

Uma abordagem candidata é manter as informações em Registros SRV do DNS em um DNS que você controla e apontar para os respectivos pontos de extremidade do hub de eventos. 

> [!IMPORTANT] 
> Lembre-se de que os hubs de eventos não permitem que seus pontos de extremidade tenham um alias direto com registros CNAME, o que significa que você usará o DNS como um mecanismo de pesquisa resiliente para endereços de ponto de extremidade e não para resolver diretamente as informações de endereço IP.

Suponha que você tenha o domínio `example.com` e, para seu aplicativo, uma zona `test.example.com` . Para dois hubs de eventos alternativos, agora você criará duas zonas aninhadas adicionais e um registro SRV em cada.

Os registros SRV são, seguindo a Convenção comum, prefixados com `_azure_eventhubs._amqp` e retêm dois registros de ponto de extremidade: um para AMQP-over-TLS na porta 5671 e outro para AMQP-over-WebSockets na porta 443, apontando para o ponto de extremidade dos hubs de eventos do namespace correspondente à zona.

| Zona                   | Registro SRV                                                                                                                                                            |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `eh1.test.example.com` | **`_azure_servicebus._amqp.eh1.test.example.com`**<br>`1 1 5671 eh1-test-example-com.servicebus.windows.net`<br>`2 2 443 eh1-test-example-com.servicebus.windows.net` |
| `eh2.test.example.com` | **`_azure_servicebus._amqp.eh2.test.example.com`**<br>`1 1 5671 eh2-test-example-com.servicebus.windows.net`<br>`2 2 443 eh2-test-example-com.servicebus.windows.net` |

Na zona do aplicativo, você criará uma entrada CNAME que aponta para a zona subordinada correspondente ao seu hub de eventos primário:

| Registro CNAME                | Alias                    |
| --------------------------- | ------------------------ |
| `eventhub.test.example.com` | `test1.test.example.com` |

Usando um cliente DNS que permite a consulta de registros CNAME e SRV explicitamente (os clientes internos do Java e do .NET só permitem a resolução simples de nomes para endereços IP), você pode então resolver o ponto de extremidade desejado. Com [DnsClient.net](https://dnsclient.michaco.net/), por exemplo, a função Lookup é:

```C#
static string GetEventHubName(string aliasName)
{
    const string SrvRecordPrefix = "_azure_eventhub._amqp.";
    LookupClient lookup = new LookupClient();

    return (from CNameRecord alias in (lookup.Query(aliasName, QueryType.CNAME).Answers)
            from SrvRecord srv in lookup.Query(SrvRecordPrefix + alias.CanonicalName, QueryType.SRV).Answers
            where srv.Port == 5671
            select srv.Target).FirstOrDefault()?.Value.TrimEnd('.');
}
```

A função retorna o nome do host de destino registrado para a porta 5671 da zona com alias no momento com o CNAME, conforme mostrado acima.

Executar um failover requer editar o registro CNAME e apontá-lo para a zona alternativa.

A vantagem de usar o DNS e, especificamente, o [DNS do Azure](../dns/dns-overview.md), é que as informações de DNS do Azure são replicadas globalmente e, portanto, resilientes contra interrupções de região única.

Esse procedimento é semelhante a como o [geo-Dr de hubs de eventos](event-hubs-geo-dr.md) funciona, mas totalmente sob seu próprio controle e também funciona com cenários ativos/ativos.

#### <a name="file-share-based-failover-configuration"></a>Configuração de failover com base em compartilhamento de arquivo

A alternativa mais simples de usar o DNS para compartilhar informações de ponto de extremidade é colocar o nome do ponto de extremidade primário em um arquivo de texto sem formatação e fornecer o arquivo de uma infraestrutura que seja robusta contra interrupções e que ainda permita atualizações.

Se você já tiver executado uma infraestrutura de site altamente disponível com disponibilidade global e replicação de conteúdo, adicionar esse arquivo e republicar o arquivo se uma opção for necessária.

> [!CAUTION]
> Você só deve publicar o nome do ponto de extremidade dessa forma, não uma cadeia de conexão completa, incluindo segredos.

#### <a name="extra-considerations-for-failing-over-consumers"></a>Considerações adicionais sobre o failover de consumidores

Para os consumidores do hub de eventos, outras considerações sobre a estratégia de failover dependem das necessidades do processador de eventos.

Se houver um desastre que exija a recriação de um sistema, a inclusão de bancos de dados, desde os de backup e dos bancos de dado são alimentados diretamente ou por meio de processamento intermediário dos eventos mantidos no Hub de eventos, você restaurará o backup e, em seguida, desejará começar a reproduzir eventos no sistema a partir do momento em que o backup do banco de dados foi criado e não do momento em que o sistema original foi destruído.

Se uma falha afeta apenas uma fatia de um sistema ou, de fato, apenas um único Hub de eventos, que se tornou inacessível, você provavelmente desejará continuar processando eventos de aproximadamente a mesma posição em que o processamento foi interrompido.

Para perceber qualquer cenário e usar o processador de eventos do seu respectivo SDK do Azure, [você criará um novo armazenamento de ponto de verificação](event-processor-balance-partition-load.md#checkpointing) e fornecerá uma posição de partição inicial, com base no _carimbo de data/hora_ do qual você deseja retomar o processamento.

Se você ainda tiver acesso ao armazenamento de ponto de verificação do hub de eventos do qual está mudando, os [metadados propagados](#service-assigned-metadata) discutidos acima o ajudarão a ignorar eventos que já foram manipulados e retomarem precisamente de onde você parou por último.

## <a name="merge"></a>Mesclar

O padrão de mesclagem tem uma ou mais tarefas de replicação que apontam para um destino, possivelmente simultaneamente com produtores regulares, que também enviam eventos para o mesmo destino.

As variações desses padrões são:

- Duas ou mais funções de replicação adquirindo simultaneamente eventos de fontes separadas e enviando-os para o mesmo destino.
- Mais uma função de replicação adquirindo eventos de uma fonte enquanto o destino também é usado diretamente por produtores.
- O padrão anterior, mas espelhado entre dois ou mais hubs de eventos, resultando nesses hubs de eventos contendo os mesmos fluxos, independentemente de onde os eventos são produzidos.

As duas primeiras variações de padrão são triviais e não diferem das tarefas de replicação simples.

O último cenário requer a replicação de eventos já replicados novamente. A técnica é demonstrada e explicada no exemplo de [EventHubToEventHubMerge](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/code/EventHubMerge) .

## <a name="editor"></a>Editor

O padrão do editor se baseia no padrão de [replicação](#replication) , mas as mensagens são modificadas antes de serem encaminhadas. 

Exemplos para tais modificações são:

- **_Transcodificação_** – se o conteúdo do evento (também conhecido como "corpo" ou "carga") chegar da fonte codificada usando o formato _Apache Avro_ ou algum formato de serialização proprietário, mas a expectativa do sistema que possui o destino é que o conteúdo seja codificado em _JSON_ , uma tarefa de replicação de transcodificação primeiro desserializará a carga do _Apache Avro_ em um grafo de objeto na memória e, em seguida, serializará esse grafo no formato _JSON_ para o evento que está sendo encaminhado. A transcodificação também inclui a **compactação de conteúdo** e as tarefas de descompactação.
- **_Transformação_** -os eventos que contêm dados estruturados podem exigir a reformatação desses dados para facilitar o consumo por consumidores de downstream. Isso pode envolver o trabalho como nivelar estruturas aninhadas, remover elementos de dados estranhos ou reformatar a carga para ajustar-se exatamente a um determinado esquema.
- **_Envio em lote_** -os eventos podem ser recebidos em lotes (vários eventos em uma única transferência) de uma fonte, mas precisam ser encaminhados individualmente para um destino, ou vice-versa. Portanto, uma tarefa pode encaminhar vários eventos com base em uma única transferência de evento de entrada ou agregar um conjunto de eventos que são transferidos juntos.
- Os dados de evento de **_validação_** de fontes externas geralmente precisam ser verificados se estão em conformidade com um conjunto de regras antes que possam ser encaminhados. As regras podem ser expressas usando esquemas ou código. Eventos que não devem estar em conformidade podem ser removidos, com o problema anotado em logs ou podem ser encaminhados a um destino de destino especial para tratá-los ainda mais.
- **_Enriquecimento_** -os dados de eventos provenientes de algumas fontes podem exigir aprimoramentos com contexto adicional para que sejam utilizáveis em sistemas de destino. Isso pode envolver a pesquisa de dados de referência e a inserção desses dados com o evento ou a adição de informações sobre a origem que é conhecida pela tarefa de replicação, mas não contidas nos eventos.
- **_Filtragem_** -alguns eventos que chegam de uma fonte podem precisar ser retidos do destino com base em alguma regra. Um filtro testará o evento em relação a uma regra e descartará o evento se o evento não corresponder à regra. Filtrar eventos duplicados observando determinados critérios e descartar eventos subsequentes com os mesmos valores é uma forma de filtragem.
- **_Criptografia_** – uma tarefa de replicação pode ter que descriptografar o conteúdo que chega do conteúdo de origem e/ou criptografar encaminhado em diante para um destino e/ou pode ter que verificar a integridade do conteúdo e dos metadados em relação a uma assinatura realizada no evento ou anexar tal assinatura.
- **_Atestado_** -uma tarefa de replicação pode anexar metadados, potencialmente protegidos por uma assinatura digital, a um evento que atesta que o evento foi recebido por meio de um canal específico ou em um momento específico.
- **_Encadeamento_** -uma tarefa de replicação pode aplicar assinaturas a fluxos de eventos, de modo que a integridade do fluxo seja protegida e os eventos ausentes possam ser detectados.

Os padrões de transformação, processamento em lote e enriquecimento geralmente são mais bem implementados com trabalhos de [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) . 

Todos esses padrões podem ser implementados usando Azure Functions, usando o [gatilho de hubs de eventos](../azure-functions/functions-bindings-event-hubs-trigger.md) para adquirir eventos e a associação de saída do hub de [eventos](../azure-functions/functions-bindings-event-hubs-output.md) para entregá-los.

## <a name="routing"></a>Roteamento

O padrão de roteamento baseia-se no padrão de [replicação](#replication) , mas em vez de ter uma origem e um destino, a tarefa de replicação tem vários destinos, ilustrados aqui em C#:

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest1", Connection = "EventHubConnectionAppSetting")] EventHubClient output1,
    [EventHub("dest2", Connection = "EventHubConnectionAppSetting")] EventHubClient output2,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // send to output1 and/or output2 based on criteria
        EventHubReplicationTasks.ConditionalForwardToEventHub(input, output1, log, (eventData) => {
            return ( inputEvent.SystemProperties.SequenceNumber%2==0 ) ? inputEvent : null;
        });
        EventHubReplicationTasks.ConditionalForwardToEventHub(input, output2, log, (eventData) => {
            return ( inputEvent.SystemProperties.SequenceNumber%2!=0 ) ? inputEvent : null;
        });
    }
}
```

A função de roteamento considerará os metadados da mensagem e/ou a carga da mensagem e, em seguida, selecionará um dos destinos disponíveis aos quais enviar.

No Azure Stream Analytics, você pode obter o mesmo com a definição de várias saídas e, em seguida, a execução de uma consulta por saída.

```sql
select * into dest1Output from inputSource where Info = 1
select * into dest2Output from inputSource where Info = 2
```


## <a name="log-projection"></a>Projeção de log

O padrão de projeção de log mescla o fluxo de eventos em um banco de dados indexado, com eventos que se tornam registros no banco de dados. Normalmente, os eventos são adicionados à mesma coleção ou tabela, e a chave de partição do hub de eventos se torna parte da chave primária que procura tornar o registro exclusivo.

A projeção de log pode produzir uma Historian de série temporal de seus dados de evento ou uma exibição compactada, na qual apenas o evento mais recente é retido para cada chave de partição. A forma do banco de dados de destino é, por fim, a você e as necessidades do seu aplicativo. Esse padrão também é conhecido como "fornecimento do evento".

> [!TIP]
> Você pode criar facilmente projeções de log no [banco de dados SQL do Azure](../stream-analytics/sql-database-output.md) e [Azure Cosmos DB](../stream-analytics/azure-cosmos-db-output.md) em Azure Stream Analytics e preferir essa opção.

A função do Azure a seguir projeta o conteúdo de um hub de eventos compactado em uma coleção de CosmosDB do Azure.

```C#
[FunctionName("Eh1ToCosmosDb1Json")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static async Task Eh1ToCosmosDb1Json(
    [EventHubTrigger("eh1", ConsumerGroup = "Eh1ToCosmosDb1", Connection = "Eh1ToCosmosDb1-source-connection")] EventData[] input,
    [CosmosDB(databaseName: "SampleDb", collectionName: "foo", ConnectionStringSetting = "CosmosDBConnection")] IAsyncCollector<object> output,
    ILogger log)
{
    foreach (var ev in input)
    {
        if (!string.IsNullOrEmpty(ev.SystemProperties.PartitionKey))
        {
            var record = new
            {
                id = ev.SystemProperties.PartitionKey,
                data = JsonDocument.Parse(ev.Body),
                properties = ev.Properties
            };
            await output.AddAsync(record);
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas

- [Aplicativos do replicador de eventos no Azure Functions][1]
- [Replicando eventos entre os hubs de eventos][2]
- [Replicando eventos para o barramento de serviço do Azure][3]

[1]: event-hubs-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus
