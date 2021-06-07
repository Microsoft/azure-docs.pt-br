---
title: Solucionar problemas ao usar o gatilho de Azure Functions para Cosmos DB
description: Problemas comuns, soluções alternativas e etapas de diagnóstico, ao usar o gatilho de Azure Functions para Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 12/29/2020
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 1b7b82ea07b7e00d281739011c9c9f83ab4dff73
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97825618"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>Diagnosticar e solucionar problemas ao usar o gatilho de Azure Functions para Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo aborda problemas comuns, soluções alternativas e etapas de diagnóstico, quando você usa o [gatilho de Azure Functions para Cosmos DB](change-feed-functions.md).

## <a name="dependencies"></a>Dependências

O gatilho Azure Functions e as associações para Cosmos DB dependem dos pacotes de extensão no tempo de execução de Azure Functions base. Sempre mantenha esses pacotes atualizados, pois eles podem incluir correções e novos recursos que podem solucionar possíveis problemas encontrados:

* Para Azure Functions v2, consulte [Microsoft. Azure. webjobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* Para Azure Functions v1, consulte [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Este artigo sempre fará referência a Azure Functions v2 sempre que o tempo de execução for mencionado, a menos que seja especificado explicitamente.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>Consumir o SDK do Azure Cosmos DB de forma independente

A principal funcionalidade do pacote de extensão é fornecer suporte para o gatilho de Azure Functions e associações para Cosmos DB. Ele também inclui o [SDK do .net Azure Cosmos DB](sql-api-sdk-dotnet-core.md), que é útil se você deseja interagir com Azure Cosmos DB programaticamente sem usar o gatilho e as associações.

Se quiser usar o SDK do Azure Cosmos DB, certifique-se de não adicionar ao seu projeto outra referência de pacote NuGet. Em vez disso, **permita que a referência do SDK seja resolvida por meio do pacote de extensão do Azure Functions**. Consumir o SDK do Azure Cosmos DB separadamente do gatilho e das associações

Além disso, se você estiver criando manualmente sua própria instância do [cliente SDK do Azure Cosmos DB](./sql-api-sdk-dotnet-core.md), deverá seguir o padrão de ter apenas uma instância do cliente [usando uma abordagem de padrão singleton](../azure-functions/manage-connections.md#documentclient-code-example-c). Esse processo evitará os problemas de soquete em potencial em suas operações.

## <a name="common-scenarios-and-workarounds"></a>Cenários comuns e soluções alternativas

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>A função do Azure falha com a coleta de mensagens de erro não existe

A função do Azure falha com a mensagem de erro "a coleção de origem ' Collection-Name ' (no banco de dados ' Database-Name ') ou a coleção de concessão ' Collection2-Name ' (no banco de dados ' Database2-Name ') não existe. Ambas as coleções devem existir antes de o ouvinte ser iniciado. Para criar automaticamente a coleção de concessão, defina ' CreateLeaseCollectionIfNotExists ' como ' true ' "

Isso significa que um ou ambos os contêineres Cosmos do Azure necessários para o gatilho funcionar não existem ou não estão acessíveis para a função do Azure. **O erro em si informará qual banco de dados e contêiner do Azure cosmos é o gatilho procurando** com base em sua configuração.

1. Verifique o `ConnectionStringSetting` atributo e se ele **faz referência a uma configuração existente no aplicativo de funções do Azure**. O valor nesse atributo não deve ser a própria cadeia de conexão, mas o nome do parâmetro de configuração.
2. Verifique se o `databaseName` e o `collectionName` existem em sua conta do Azure Cosmos. Se você estiver usando a substituição automática de valor (usando `%settingName%` padrões), verifique se o nome da configuração existe em sua aplicativo de funções do Azure.
3. Se você não especificar um `LeaseCollectionName/leaseCollectionName` , o padrão será "concessões". Verifique se esse contêiner existe. Opcionalmente, você pode definir o `CreateLeaseCollectionIfNotExists` atributo em seu gatilho para `true` para criá-lo automaticamente.
4. Verifique a [configuração de firewall da sua conta do Azure Cosmos](how-to-configure-firewall.md) para ver se não está bloqueando a função do Azure.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Falha ao iniciar a função do Azure com "a coleção de produtividade compartilhada deve ter uma chave de partição"

As versões anteriores da extensão de Azure Cosmos DB não tinham suporte usando um contêiner de concessões que foi criado em um [banco de dados de produtividade compartilhado](./set-throughput.md#set-throughput-on-a-database). Para resolver esse problema, atualize a extensão [Microsoft. Azure. webjobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) para obter a versão mais recente.

### <a name="azure-function-fails-to-start-with-partitionkey-must-be-supplied-for-this-operation"></a>Falha na inicialização da função do Azure com "PartitionKey deve ser fornecido para esta operação".

Esse erro significa que você está usando atualmente uma coleção de concessão particionada com uma [dependência de extensão](#dependencies)antiga. Atualize para a versão mais recente disponível. Se você estiver executando o Azure Functions v1 no momento, será necessário atualizar para o Azure Functions v2.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Falha na inicialização da função do Azure com "a coleção de concessão, se particionada, deve ter a chave de partição igual à ID".

Esse erro significa que o contêiner de concessões atuais está particionado, mas o caminho da chave de partição não é `/id` . Para resolver esse problema, você precisa recriar o contêiner de concessões com `/id` o como a chave de partição.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>Você verá um "o valor não pode ser nulo. Nome do parâmetro: o "em seus logs de Azure Functions quando você tenta executar o gatilho

Esse problema será exibido se você estiver usando o portal do Azure e tentar selecionar o botão **executar** na tela ao inspecionar uma função do Azure que usa o gatilho. O gatilho não exige que você selecione executar para iniciar, ele será iniciado automaticamente quando a função do Azure for implantada. Se você quiser verificar o fluxo de log da função do Azure na portal do Azure, basta acessar o contêiner monitorado e inserir alguns novos itens, você verá automaticamente o gatilho em execução.

### <a name="my-changes-take-too-long-to-be-received"></a>Minhas alterações demoram muito para serem recebidas

Esse cenário pode ter várias causas e todas elas devem ser verificadas:

1. O seu Azure Function está sendo implantado na mesma região da sua conta Azure Cosmos? Para melhor latência de rede, o Azure Function e sua conta Azure Cosmos devem ser colocados na mesma região do Azure.
2. As alterações que estão ocorrendo em seu contêiner Azure Cosmos são contínuas ou esporádicas?
Se for a última opção, pode haver algum atraso entre as alterações sendo armazenadas e o Azure Function pegando-as. Isso acontece porque internamente, quando o gatilho verifica as alterações em seu contêiner Azure Cosmos e não encontra nada pendente para ser lido, suspenderá por uma quantidade de tempo configurável (5 segundos, por padrão) antes de verificar novas alterações (para evitar alto consumo da RU). Você pode configurar este tempo de suspensão ao `FeedPollDelay/feedPollDelay`fazer as [configurações](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) do seu gatilho (o valor é esperado em milissegundos).
3. O contêiner Cosmos do Azure pode ser [limitado por taxa](./request-units.md).
4. Você pode usar o `PreferredLocations` atributo em seu gatilho para especificar uma lista separada por vírgulas de regiões do Azure para definir uma ordem de conexão preferencial personalizada.

### <a name="some-changes-are-repeated-in-my-trigger"></a>Algumas alterações são repetidas no meu gatilho

O conceito de uma "alteração" é uma operação em um documento. Os cenários mais comuns em que os eventos para o mesmo documento são recebidos são:
* A conta está usando a consistência eventual. Ao consumir o feed de alterações em um nível de consistência eventual, pode haver eventos duplicados entre as operações de leitura do feed de alterações subsequentes (o último evento de uma operação de leitura aparece como o primeiro da próxima).
* O documento está sendo atualizado. O feed de alterações pode conter várias operações para os mesmos documentos, se esse documento estiver recebendo atualizações, ele poderá pegar vários eventos (um para cada atualização). Uma maneira fácil de distinguir entre diferentes operações para o mesmo documento é controlar a `_lsn` [propriedade para cada alteração](change-feed.md#change-feed-and-_etag-_lsn-or-_ts). Se eles não corresponderem, essas alterações serão diferentes no mesmo documento.
* Se você estiver identificando documentos apenas pelo `id` , lembre-se de que o identificador exclusivo de um documento é o `id` e sua chave de partição (pode haver dois documentos com a mesma `id` chave de partição diferente).

### <a name="some-changes-are-missing-in-my-trigger"></a>Algumas alterações estão ausentes no meu gatilho

Se você descobrir que algumas das alterações que ocorreram em seu contêiner Cosmos do Azure não estão sendo coletadas pela função do Azure ou algumas alterações estão ausentes no destino quando você a estiver copiando, siga as etapas abaixo.

Quando sua função do Azure recebe as alterações, ele geralmente as processa e, opcionalmente, envia o resultado para outro destino. Quando você estiver investigando alterações ausentes, certifique-se de **que você meça quais alterações estão sendo recebidas no ponto de ingestão** (quando a função do Azure é iniciada), não no destino.

Se algumas alterações estiverem ausentes no destino, isso pode significar que o erro ocorre durante a execução da função do Azure depois que as alterações foram recebidas.

Nesse cenário, o melhor curso de ação é adicionar `try/catch` blocos em seu código e dentro dos loops que podem estar processando as alterações, para detectar qualquer falha em um determinado subconjunto de itens e tratá-los de forma adequada (enviá-los para outro armazenamento para análise posterior ou repetição).

> [!NOTE]
> Por padrão, o gatilho de Azure Functions para Cosmos DB não repetirá um lote de alterações se houver uma exceção sem tratamento durante a execução do código. Isso significa que o motivo pelo qual as alterações não chegaram no destino é porque você está falhando em processá-las.

Se o destino for outro contêiner Cosmos e você estiver executando operações Upsert para copiar os itens, **Verifique se a definição de chave de partição no contêiner monitorado e de destino são iguais**. As operações Upsert podem estar salvando vários itens de origem como um no destino devido a essa diferença de configuração.

Se você achar que algumas alterações não foram recebidas por seu gatilho, o cenário mais comum é que há **outra função do Azure em execução**. Pode ser outra função do Azure implantada no Azure ou uma função do Azure em execução localmente na máquina de um desenvolvedor que tenha **exatamente a mesma configuração** (mesmo contêineres monitorados e de concessão), e essa função do Azure está roubando um subconjunto das alterações que você esperaria que sua função do Azure processasse.

Além disso, o cenário pode ser validado, se você souber quantas instâncias do Azure Aplicativo de funções você está executando. Se você inspecionar o contêiner de concessões e contar o número de itens de concessão no, os valores distintos da `Owner` Propriedade neles devem ser iguais ao número de instâncias de seu aplicativo de funções. Se houver mais proprietários do que as instâncias conhecidas do Azure Aplicativo de funções, isso significa que esses proprietários extras são aqueles que "roubam" as alterações.

Uma maneira fácil de contornar essa situação é aplicar um `LeaseCollectionPrefix/leaseCollectionPrefix` à sua função com um valor novo/diferente ou, como alternativa, testar com um novo contêiner de concessões.

### <a name="need-to-restart-and-reprocess-all-the-items-in-my-container-from-the-beginning"></a>É necessário reiniciar e reprocessar todos os itens no meu contêiner desde o início 
Para reprocessar todos os itens em um contêiner desde o início:
1. Pare sua função do Azure se ela estiver em execução no momento. 
1. Excluir os documentos na coleção de concessão (ou excluir e recriar a coleção de concessão para que ela fique vazia)
1. Defina o atributo [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) CosmosDBTrigger em sua função como true. 
1. Reinicie o Azure function. Agora, ele lerá e processará todas as alterações desde o início. 

Definir [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) como true instruirá a função do Azure a começar a ler as alterações desde o início do histórico da coleção, em vez da hora atual. Isso só funciona quando não há concessões já criadas (isto é, documentos na coleção de concessões). Definir essa propriedade como true quando houver concessões já criadas não tem efeito; Nesse cenário, quando uma função é interrompida e reiniciada, ela começa a ler do último ponto de verificação, conforme definido na coleção de concessões. Para reprocessar desde o início, siga as etapas acima de 1-4.  

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>A associação só pode ser feita com IReadOnlyList \<Document> ou JArray

Esse erro ocorrerá se seu projeto Azure Functions (ou qualquer projeto referenciado) contiver uma referência manual do NuGet para o SDK do Azure Cosmos DB com uma versão diferente daquela fornecida pela [extensão Azure Functions Cosmos DB](./troubleshoot-changefeed-functions.md#dependencies).

Para contornar essa situação, remova a referência manual do NuGet que foi adicionada e permita que a referência do SDK do Azure Cosmos DB seja resolvida por meio do pacote de extensão Azure Functions Cosmos DB.

### <a name="changing-azure-functions-polling-interval-for-the-detecting-changes"></a>Alterando o intervalo de sondagem da função do Azure para a detecção de alterações

Conforme explicado anteriormente para [que minhas alterações demore muito para serem recebidas, a](./troubleshoot-changefeed-functions.md#my-changes-take-too-long-to-be-received)função do Azure entrará em suspensão por um período configurável (5 segundos, por padrão) antes de verificar se há novas alterações (para evitar o alto consumo de ru). Você pode configurar este tempo de suspensão ao `FeedPollDelay/feedPollDelay`fazer as [configurações](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) do seu gatilho (o valor é esperado em milissegundos).

## <a name="next-steps"></a>Próximas etapas

* [Habilitar o monitoramento para seu Azure Functions](../azure-functions/functions-monitoring.md)
* [Solução de problemas do SDK do .NET Azure Cosmos DB](./troubleshoot-dot-net-sdk.md)
