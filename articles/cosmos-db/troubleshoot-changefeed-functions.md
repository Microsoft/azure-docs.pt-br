---
title: Diagnosticar e solucionar problemas ao usar o gatilho de Azure Functions para Cosmos DB
description: Problemas comuns, soluções alternativas e etapas de diagnóstico, ao usar o gatilho de Azure Functions para Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.date: 07/17/2019
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b90986e449df7e81f97f9ef86ce3cf69621c76d6
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335741"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>Diagnosticar e solucionar problemas ao usar o gatilho de Azure Functions para Cosmos DB

Este artigo aborda problemas comuns, soluções alternativas e etapas de diagnóstico, quando você usa o [gatilho de Azure Functions para Cosmos DB](change-feed-functions.md).

## <a name="dependencies"></a>Dependências

O gatilho Azure Functions e as associações para Cosmos DB dependem dos pacotes de extensão no tempo de execução de Azure Functions base. Sempre mantenha esses pacotes atualizados, pois eles podem incluir correções e novos recursos que podem solucionar possíveis problemas encontrados:

* Para Azure Functions v2, consulte [Microsoft. Azure. webjobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* Para Azure Functions v1, consulte [Microsoft. Azure. webjobs. Extensions. DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Este artigo sempre fará referência a Azure Functions v2 sempre que o tempo de execução for mencionado, a menos que seja especificado explicitamente.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>Consumir o SDK do Azure Cosmos DB de forma independente

A principal funcionalidade do pacote de extensão é fornecer suporte para o gatilho de Azure Functions e associações para Cosmos DB. Ele também inclui o [SDK do .net Azure Cosmos DB](sql-api-sdk-dotnet-core.md), que é útil se você deseja interagir com Azure Cosmos DB programaticamente sem usar o gatilho e as associações.

Se quiser usar o SDK do Azure Cosmos DB, certifique-se de não adicionar ao seu projeto outra referência de pacote NuGet. Em vez disso, **permita que a referência do SDK seja resolvida por meio do pacote de extensão do Azure Functions**. Consumir o SDK do Azure Cosmos DB separadamente do gatilho e das associações

Além disso, se você estiver criando manualmente sua própria instância do [cliente SDK do Azure Cosmos DB](./sql-api-sdk-dotnet-core.md), deverá seguir o padrão de ter apenas uma instância do cliente [usando uma abordagem de padrão singleton](../azure-functions/manage-connections.md#documentclient-code-example-c). Esse processo evitará os problemas de soquete em potencial em suas operações.

## <a name="common-scenarios-and-workarounds"></a>Cenários comuns e soluções alternativas

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>A função do Azure falha com a coleta de mensagens de erro não existe

A função do Azure falha com a mensagem de erro "a coleção de origem ' Collection-Name ' (no banco de dados ' Database-Name ') ou a coleção de concessão ' Collection2-Name ' (no banco de dados ' Database2-Name ') não existe. Ambas as coleções devem existir antes de o ouvinte ser iniciado. Para criar automaticamente a coleção de concessão, defina ' CreateLeaseCollectionIfNotExists ' como ' true ' "

Isso significa que um ou ambos os contêineres Cosmos do Azure necessários para o gatilho funcionar não existem ou não estão acessíveis para a função do Azure. **O erro em si informará qual contêiner e banco de dados Cosmos do Azure é o gatilho procurando** com base em sua configuração.

1. Verifique o `ConnectionStringSetting` atributo e se ele **faz referência a uma configuração existente no aplicativo de funções do Azure**. O valor nesse atributo não deve ser a própria cadeia de conexão, mas o nome do parâmetro de configuração.
2. Verifique se o `databaseName` e `collectionName` o existem em sua conta do Azure Cosmos. Se você estiver usando a substituição automática de valor `%settingName%` (usando padrões), verifique se o nome da configuração existe em sua aplicativo de funções do Azure.
3. Se você não especificar um `LeaseCollectionName/leaseCollectionName`, o padrão será "concessões". Verifique se esse contêiner existe. Opcionalmente, você pode definir `CreateLeaseCollectionIfNotExists` o atributo em seu gatilho `true` para para criá-lo automaticamente.
4. Verifique a [configuração de firewall da sua conta do Azure Cosmos](how-to-configure-firewall.md) para ver se não está bloqueando a função do Azure.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Falha ao iniciar a função do Azure com "a coleção de produtividade compartilhada deve ter uma chave de partição"

As versões anteriores da extensão de Azure Cosmos DB não tinham suporte usando um contêiner de concessões que foi criado em um [banco de dados de produtividade compartilhado](./set-throughput.md#set-throughput-on-a-database). Para resolver esse problema, atualize a extensão [Microsoft. Azure. webjobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) para obter a versão mais recente.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Falha na inicialização da função do Azure com "a coleção de concessão, se particionada, deve ter a chave de partição igual à ID".

Esse erro significa que o contêiner de concessões atuais está particionado, mas o caminho da chave de `/id`partição não é. Para resolver esse problema, você precisa recriar o contêiner de concessões com `/id` o como a chave de partição.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>Você verá um "o valor não pode ser nulo. Nome do parâmetro: o "em seus logs de Azure Functions quando você tenta executar o gatilho

Esse problema será exibido se você estiver usando o portal do Azure e tentar selecionar o botão **executar** na tela ao inspecionar uma função do Azure que usa o gatilho. O gatilho não exige que você selecione executar para iniciar, ele será iniciado automaticamente quando a função do Azure for implantada. Se você quiser verificar o fluxo de log da função do Azure na portal do Azure, basta acessar o contêiner monitorado e inserir alguns novos itens, você verá automaticamente o gatilho em execução.

### <a name="my-changes-take-too-long-be-received"></a>Minhas alterações demoram muito para serem recebidas

Esse cenário pode ter várias causas e todas elas devem ser verificadas:

1. Sua função do Azure está implantada na mesma região que sua conta do Azure Cosmos? Para uma latência de rede ideal, a função do Azure e sua conta do Azure Cosmos devem ser colocalizadas na mesma região do Azure.
2. As alterações que ocorrem no contêiner Cosmos do Azure são contínuas ou esporádicas?
Se for o último, pode haver algum atraso entre as alterações sendo armazenadas e a função do Azure que a seleciona. Isso ocorre porque, internamente, quando o gatilho verifica se há alterações no contêiner Cosmos do Azure e não localiza nenhum pendente para ser lido, ele será suspenso por uma quantidade configurável de tempo (5 segundos, por padrão) antes de verificar se há novas alterações (para evitar o alto consumo de RU). Você pode configurar esse tempo de suspensão por `FeedPollDelay/feedPollDelay` meio da configuração na [configuração](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) do seu gatilho (espera-se que o valor esteja em milissegundos).
3. O contêiner Cosmos do Azure pode ser [limitado por taxa](./request-units.md).
4. Você pode usar o `PreferredLocations` atributo em seu gatilho para especificar uma lista separada por vírgulas de regiões do Azure para definir uma ordem de conexão preferencial personalizada.

### <a name="some-changes-are-missing-in-my-trigger"></a>Algumas alterações estão ausentes no meu gatilho

Se você descobrir que algumas das alterações que ocorreram em seu contêiner Cosmos do Azure não estão sendo coletadas pela função do Azure, há uma etapa de investigação inicial que precisa ocorrer.

Quando sua função do Azure recebe as alterações, ele geralmente as processa e, opcionalmente, envia o resultado para outro destino. Quando você estiver investigando alterações ausentes, certifique-se de **que você meça quais alterações estão sendo recebidas no ponto de ingestão** (quando a função do Azure é iniciada), não no destino.

Se algumas alterações estiverem ausentes no destino, isso pode significar que o erro ocorre durante a execução da função do Azure depois que as alterações foram recebidas.

Nesse cenário, o melhor curso de ação é adicionar `try/catch blocks` seu código e dentro dos loops que possam estar processando as alterações, para detectar qualquer falha em um determinado subconjunto de itens e tratá-los adequadamente (enviá-los para outro armazenamento para mais análise ou tentar novamente). 

> [!NOTE]
> Por padrão, o gatilho de Azure Functions para Cosmos DB não repetirá um lote de alterações se houver uma exceção sem tratamento durante a execução do código. Isso significa que o motivo pelo qual as alterações não chegaram no destino é porque você está falhando em processá-las.

Se você descobrir que algumas alterações não foram recebidas por seu gatilho, o cenário mais comum é que há **outra função do Azure em execução**. Pode ser outra função do Azure implantada no Azure ou uma função do Azure em execução localmente na máquina de um desenvolvedor que tenha **exatamente a mesma configuração** (mesmo contêineres monitorados e de concessão), e essa função do Azure está roubando um subconjunto das alterações que você esperaria que sua função do Azure fosse processada.

Além disso, o cenário pode ser validado, se você souber quantas instâncias do Azure Aplicativo de funções você está executando. Se você inspecionar o contêiner de concessões e contar o número de itens de concessão no, os valores `Owner` distintos da propriedade neles devem ser iguais ao número de instâncias de seu aplicativo de funções. Se houver mais proprietários do que as instâncias conhecidas do Azure Aplicativo de funções, isso significa que esses proprietários extras são o "roubar" as alterações.

Uma maneira fácil de solucionar essa situação é aplicar um `LeaseCollectionPrefix/leaseCollectionPrefix` à sua função com um valor novo/diferente ou, como alternativa, testar com um novo contêiner de concessões.

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>A associação só pode ser feita com\<> de documento IReadOnlyList ou JArray

Esse erro ocorrerá se seu projeto Azure Functions (ou qualquer projeto referenciado) contiver uma referência manual do NuGet para o SDK do Azure Cosmos DB com uma versão diferente daquela fornecida pela [extensão Azure Functions Cosmos DB](./troubleshoot-changefeed-functions.md#dependencies).

Para solucionar essa situação, remova a referência manual do NuGet que foi adicionada e permita que o Azure Cosmos DB referência do SDK seja resolvido por meio do pacote de extensão Azure Functions Cosmos DB.

## <a name="next-steps"></a>Próximas etapas

* [Habilitar o monitoramento para seu Azure Functions](../azure-functions/functions-monitoring.md)
* [Solução de problemas do SDK do .NET Azure Cosmos DB](./troubleshoot-dot-net-sdk.md)
