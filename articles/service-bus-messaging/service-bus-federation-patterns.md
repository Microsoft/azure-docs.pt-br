---
title: Padrões de tarefa de replicação de mensagem-barramento de serviço do Azure | Microsoft Docs
description: Este artigo fornece diretrizes detalhadas para a implementação de padrões de tarefa de replicação de mensagem específicos
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: d823ee7ccd4f53bfc3e10211a4f44908273a110d
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657331"
---
# <a name="message-replication-tasks-patterns"></a>Padrões de tarefas de replicação de mensagem

As visão geral da [Federação](service-bus-federation-overview.md) e as [funções do replicador](service-bus-federation-replicator-functions.md) explicam a lógica para os elementos básicos das tarefas de replicação, e é recomendável que você se familiarize com eles antes de continuar com este artigo.

Neste artigo, detalharemos as diretrizes de implementação para vários dos padrões realçados na seção visão geral. 

## <a name="replication"></a>Replicação 

O padrão de replicação copia mensagens de uma fila ou de um tópico para o próximo, ou de uma fila ou tópico para outro destino, como um hub de eventos. As mensagens são encaminhadas sem fazer modificações na carga da mensagem. 

A implementação desse padrão é coberta pela replicação de [mensagem de e para o exemplo do barramento de serviço do Azure](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy) .

### <a name="sequences-and-order-preservation"></a>Sequências e preservação de pedidos

O modelo de replicação não tem como objetivo preservar a ordem absoluta de mensagens de uma fila ou tópico de origem em uma fila ou tópico de destino, mas se concentra, sempre que necessário, ao preservar a ordem relativa de mensagens em que o aplicativo exige isso. O aplicativo habilita isso habilitando o suporte de sessão para a entidade de origem e agrupando mensagens relacionadas com a mesma [chave de sessão](message-sessions.md).

As funções de replicação predefinidas com reconhecimento de sessão garantem que as sequências de mensagens com a mesma ID de sessão recuperadas de uma entidade de origem sejam enviadas para a fila ou tópico de destino como um lote na sequência original e com a mesma ID de sessão. 

### <a name="service-assigned-metadata"></a>Metadados atribuídos ao serviço 

Os metadados atribuídos ao serviço de uma mensagem obtida da fila ou do tópico de origem, a hora de enfileiramento original e o número de sequência, são substituídos por novos valores atribuídos ao serviço na fila ou no tópico de destino, mas com as tarefas de replicação padrão fornecidas em nossos exemplos, os valores originais são preservados nas propriedades do usuário: `repl-enqueue-time` (cadeia de caracteres ISO8601) e `repl-sequence` .

Essas propriedades são do tipo cadeia de caracteres e contêm o valor em cadeias das respectivas propriedades originais.  Se a mensagem for encaminhada várias vezes, os metadados atribuídos ao serviço da fonte imediata serão anexados às propriedades já existentes, com valores separados por ponto e vírgula. 

### <a name="failover"></a>Failover

Se você estiver usando a replicação para fins de recuperação de desastre, para proteger contra mensagens de disponibilidade regionais no serviço do barramento de serviço ou contra interrupções de rede, qualquer cenário de falha desse tipo exigirá a execução de um failover de uma fila ou de um tópico para o próximo, informando produtores e/ou consumidores para usar o ponto de extremidade secundário.

Para todos os cenários de failover, supõe-se que os elementos necessários dos namespaces sejam estruturalmente idênticos, o que significa que as filas e os tópicos são nomeados de forma idêntica e que as regras de assinatura de acesso compartilhado e/ou as regras de controle de acesso baseado em função são configuradas da mesma maneira. Você pode criar (e atualizar) um namespace secundário seguindo as [diretrizes para mover namespaces](move-across-regions.md) e omitir a etapa de limpeza.

Para forçar produtores e consumidores a mudarem, você precisará fazer as informações sobre qual namespace usar disponível para pesquisa em um local que seja fácil de acessar e atualizar. Se produtores ou consumidores encontrarem erros frequentes ou persistentes, eles deverão consultar esse local e ajustar sua configuração. Há várias maneiras de compartilhar essa configuração, mas indicamos dois nos seguintes itens: DNS e compartilhamentos de arquivos.

#### <a name="dns-based-failover-configuration"></a>Configuração de failover baseado em DNS

Uma abordagem candidata é manter as informações em Registros SRV do DNS em um DNS que você controla e apontar para os respectivos pontos de extremidade de fila ou tópico. Lembre-se de que os hubs de mensagem não permitem que seus pontos de extremidade tenham um alias direto com registros CNAME, o que significa que você usará o DNS como um mecanismo de pesquisa resiliente para endereços de ponto de extremidade e não para resolver diretamente as informações de endereço IP. 

Suponha que você tenha o domínio `example.com` e, para seu aplicativo, uma zona `test.example.com` . Para dois Service Bus alternativo, agora você criará duas zonas aninhadas adicionais e um registro SRV em cada. 

Os registros SRV são, seguindo a Convenção comum, prefixados com `_azure_servicebus._amqp` e retêm dois registros de ponto de extremidade: um para AMQP-over-TLS na porta 5671 e outro para AMQP-over-WebSockets na porta 443, apontando para o ponto de extremidade do barramento de serviço correspondente à zona.

| Zona                 | Registro SRV
|----------------------|-------------------------------------------------------------
| `sb1.test.example.com` | **`_azure_servicebus._amqp.sb1.test.example.com`**<br>`1 1 5671 sb1-test-example-com.servicebus.windows.net`<br>`2 2 443 sb1-test-example-com.servicebus.windows.net`
| `sb2.test.example.com` | **`_azure_servicebus._amqp.sb1.test.example.com`**<br>`1 1 5671 sb2-test-example-com.servicebus.windows.net`<br>`2 2 443 sb2-test-example-com.servicebus.windows.net`

Na zona do aplicativo, você criará uma entrada CNAME que aponta para a zona subordinada correspondente à sua fila ou tópico primário:

| Registro CNAME                 | Alias
|------------------------------|-------------------------------------------------------------
| `servicebus.test.example.com`  | `test1.test.example.com`

Usando um cliente DNS que permite a consulta de registros CNAME e SRV explicitamente (os clientes internos do Java e do .NET só permitem a resolução simples de nomes para endereços IP), você pode então resolver o ponto de extremidade desejado. Com [DnsClient.net](https://dnsclient.michaco.net/), por exemplo, a função Lookup é:

``` C#
static string GetServiceBusName(string aliasName)
{
    const string SrvRecordPrefix = "_azure_servicebus._amqp.";
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

Esse procedimento é semelhante a como funciona o [geo-Dr do barramento de serviço](service-bus-geo-dr.md) , mas totalmente sob seu próprio controle e também funciona com cenários ativos/ativos.

#### <a name="file-share-based-failover-configuration"></a>Configuração de failover com base em compartilhamento de arquivo

A alternativa mais simples de usar o DNS para compartilhar informações de ponto de extremidade é colocar o nome do ponto de extremidade primário em um arquivo de texto sem formatação e fornecer o arquivo de uma infraestrutura que seja robusta contra interrupções e que ainda permita atualizações. 

Se você já tiver executado uma infraestrutura de site altamente disponível com disponibilidade global e replicação de conteúdo, adicionar esse arquivo e republicar o arquivo se uma opção for necessária.

## <a name="merge"></a>Mesclar

O padrão de mesclagem tem uma ou mais tarefas de replicação que apontam para um destino, possivelmente simultaneamente com produtores regulares que também enviam mensagens para o mesmo destino. 

As variações desse padrão são:
- Duas ou mais funções de replicação adquirindo simultaneamente mensagens de fontes separadas e enviando-as para o mesmo destino.
- Mais uma função de replicação adquirindo mensagens de uma origem enquanto o destino também é usado diretamente por produtores. 
- O padrão anterior, mas mensagens espelhadas entre dois ou mais tópicos, resultando nesses tópicos que contêm as mesmas mensagens, independentemente de onde as mensagens são produzidas.

As duas primeiras variações de padrão são triviais e não diferem das tarefas de replicação simples.

O último cenário requer a replicação de mensagens já replicadas novamente. A técnica é demonstrada e explicada no exemplo ativo/ativo.

## <a name="editor"></a>Editor

O padrão do editor se baseia no padrão de [replicação](#replication) , mas as mensagens são modificadas antes de serem encaminhadas. Exemplos para tais modificações são:

- **_Transcodificação_* _-se o conteúdo da mensagem (também conhecido como "corpo" ou "carga") chegar da origem codificada usando o formato _Apache Avro * ou algum formato de serialização proprietário, mas a expectativa do sistema que possui o destino é que o conteúdo seja codificado em *JSON* , uma tarefa de replicação de transcodificação primeiro desserializará a carga do *Apache Avro* em um grafo de objeto na memória e, em seguida, serializará esse grafo no formato *JSON* para a mensagem que está sendo encaminhada. A transcodificação também inclui a **compactação de conteúdo** e as tarefas de descompactação.
- *A *_transformação_* _-messages que contêm dados estruturados pode exigir a reformatação desses dados para um consumo mais fácil por consumidores de downstream. Isso pode envolver o trabalho como nivelar estruturas aninhadas, remover elementos de dados estranhos ou reformatar a carga para ajustar-se exatamente a um determinado esquema.
- _*_Envio em lote_*_ -as mensagens podem ser recebidas em lotes (várias mensagens em uma única transferência) de uma fonte, mas precisam ser encaminhadas individualmente para um destino, ou vice-versa. Portanto, uma tarefa pode encaminhar várias mensagens com base em uma única transferência de mensagem de entrada ou agregar um conjunto de mensagens que são então transferidas juntas. 
- A _*_validação_*_ -dados de mensagens de fontes externas geralmente precisam ser verificados se estão em conformidade com um conjunto de regras antes que elas possam ser encaminhadas. As regras podem ser expressas usando esquemas ou código. as mensagens que não estão em conformidade podem ser removidas, com o problema anotado nos logs ou podem ser encaminhadas a um destino de destino especial para tratá-las ainda mais.   
- _*_Enriquecimento_*_ – os dados de mensagens provenientes de algumas fontes podem exigir aprimoramentos com contexto adicional para que sejam utilizáveis em sistemas de destino. Isso pode envolver a pesquisa de dados de referência e a inserção desses dados com a mensagem, ou a adição de informações sobre a origem que é conhecida pela tarefa de replicação, mas não contidas nas mensagens. 
- _*_Filtragem_*_ – algumas mensagens que chegam de uma fonte podem ter que ser retidas do destino com base em alguma regra. Um filtro testará a mensagem em relação a uma regra e descartará a mensagem se a mensagem não corresponder à regra. Filtrar mensagens duplicadas observando determinados critérios e descartar mensagens subsequentes com os mesmos valores é uma forma de filtragem.
- _*_Roteamento e particionamento_*_ – algumas tarefas de replicação podem permitir dois ou mais destinos alternativos e definir regras para as quais o destino de replicação é escolhido para qualquer mensagem específica com base nos metadados ou no conteúdo da mensagem. Uma forma especial de roteamento é o particionamento, em que a tarefa atribui explicitamente partições em um destino de replicação com base em regras.
- _*_Criptografia_*_ – uma tarefa de replicação pode ter que descriptografar o conteúdo que chega do conteúdo de origem e/ou criptografar encaminhado em diante para um destino e/ou pode ter que verificar a integridade do conteúdo e dos metadados em relação a uma assinatura transportada na mensagem ou anexar tal assinatura. 
- _*_Atestado_*_ -uma tarefa de replicação pode anexar metadados, potencialmente protegidos por uma assinatura digital, a uma mensagem que atesta que a mensagem foi recebida por meio de um canal específico ou em um momento específico.     
- _ *_Encadeamento_**-uma tarefa de replicação pode aplicar assinaturas a sequências de mensagens, de modo que a integridade da sequência seja protegida e mensagens ausentes possam ser detectadas.

Todos esses padrões podem ser implementados usando Azure Functions, usando o [gatilho de hubs de mensagem](../azure-functions/functions-bindings-service-bus-trigger.md) para adquirir mensagens e a associação de saída da [fila ou do tópico](../azure-functions/functions-bindings-service-bus-output.md) para entregá-las. 

## <a name="routing"></a>Roteamento

O padrão de roteamento baseia-se no padrão de [replicação](#replication) , mas em vez de ter uma origem e um destino, a tarefa de replicação tem vários destinos, ilustrados aqui em C#:

``` csharp
[FunctionName("SBRouter")]
public static async Task Run(
    [ServiceBusTrigger("source", Connection = "serviceBusConnectionAppSetting")] Message[] messages,
    [ServiceBus("dest1", Connection = "serviceBusConnectionAppSetting")] QueueClient output1,
    [ServiceBus("dest2", Connection = "serviceBusConnectionAppSetting")] QueueClient output2,
    ILogger log)
{
    foreach (Message messageData in messages)
    {
        // send to output1 or output2 based on criteria 
    }
}
```

A função de roteamento considerará os metadados da mensagem e/ou a carga da mensagem e, em seguida, selecionará um dos destinos disponíveis aos quais enviar.

## <a name="next-steps"></a>Próximas etapas

- [aplicativos do replicador de mensagens no Azure Functions][1]
- [Replicando mensagens entre o barramento de serviço][2]
- [Replicando mensagens para os hubs de eventos do Azure][3]

[1]: service-bus-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub