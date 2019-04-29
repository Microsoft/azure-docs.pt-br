---
title: Trabalhando com a biblioteca do processador de feed de alterações no Azure Cosmos DB
description: Usando a biblioteca do processador de feed de alterações do Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: cf03233c6a92b7fd1b782f8128787bfda5582f7d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60893298"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Processador do feed de alterações no Azure Cosmos DB 

A [biblioteca do processador de feed de alterações do Azure Cosmos DB](sql-api-sdk-dotnet-changefeed.md) ajuda a distribuir o processamento de eventos entre vários consumidores. Essa biblioteca simplifica as alterações de leitura em partições e vários threads operando em paralelo.

O principal benefício da biblioteca do processador de feed de alterações é que você não precisa gerenciar cada partição e token de continuação e não precisa pesquisar cada contêiner manualmente.

A biblioteca do processador de feed de alterações simplifica as alterações de leitura em partições e vários threads operando em paralelo. Ela gerencia automaticamente as alterações de leitura entre partições usando um mecanismo de concessão. Como é possível ver na imagem a seguir, se você iniciar dois clientes que usam a biblioteca do processador de feed de alterações, eles dividirão o trabalho entre si. Na medida em que você continua aumentando o número de clientes, eles continuarão dividindo o trabalho entre si.

![Usando a biblioteca do processador de feed de alterações do Azure Cosmos DB](./media/change-feed-processor/change-feed-output.png)

O cliente esquerdo foi iniciado primeiro e ele começou a monitorar todas as partições, então o segundo cliente foi iniciado e, por fim, o primeiro libera algumas das concessões para o segundo cliente. Essa é uma maneira eficiente de distribuir o trabalho entre diferentes computadores e clientes.

Se você tiver duas funções do Azure sem servidor monitorando o mesmo contêiner e usando a mesma concessão, as duas funções poderão obter documentos diferentes dependendo de como a biblioteca do processador decide processar as partições.

## <a name="implementing-the-change-feed-processor-library"></a>Implementando a biblioteca do processador de feed de alterações

Há quatro componentes principais de implementação da biblioteca do processador de feed de alterações: 

1. **Contêiner monitorado:** O contêiner monitorado possui os dados a partir dos quais o feed de alterações é gerado. Todas as inserções e alterações no contêiner monitorado são refletidas no feed de alterações do contêiner.

1. **Contêiner de concessão:** O contêiner de concessão coordena o processamento do feed de alterações em vários trabalhos. Um contêiner separado é usado para armazenar as concessões com uma concessão por partição. É vantajoso armazenar esse contêiner de concessão em uma conta diferente, com a região de gravação mais próxima do local em que o processador de feed de alterações está em execução. Um objeto de concessão contém os seguintes atributos:

   * Proprietário: Especifica o host que é proprietário da concessão.

   * Continuação: Especifica a posição (token de continuação) no feed de alterações para uma partição específica.

   * Carimbo de data/hora: Última vez em que a concessão foi atualizada, e o registro de data e hora pode ser usado para verificar se a concessão é considerada expirada.

1. **Host do processador:** Cada host determina quantas partições devem ser processadas com base no número de instâncias de hosts com concessões ativas.

   * Quando um host é iniciado, ele adquire concessões para balancear a carga de trabalho entre todos os hosts. Um host renova concessões periodicamente para que as concessões permaneçam ativas.

   * Um host realiza pontos de verificação do último token de continuação em relação à respectiva concessão para cada leitura. Para garantir a segurança de simultaneidade, um host verifica o Etag de cada atualização de concessão. Também há suporte para outras estratégias de ponto de verificação.

   * Durante o desligamento, um host libera todas as concessões, mas mantém as informações de continuação, para que possa retomar a leitura do ponto de verificação armazenado mais tarde.

   Atualmente, o número de hosts não pode ser maior que o número de partições (concessões).

1. **Consumidores:** Consumidores ou trabalhos, são os threads que realizam o processamento do feed de alterações iniciado por cada host. Cada host de processador pode ter vários consumidores. Cada consumidor lê o feed de alterações da partição à qual ele é atribuído e notifica o respectivo host sobre as alterações e as concessões expiradas.

Para compreender melhor como esses quatro elementos do processador de feed de alterações funcionam em conjunto, vamos examinar um exemplo no diagrama a seguir. A coleção monitorada armazena documentos e usa 'Cidade' como chave de partição. Podemos ver que a partição azul contém documentos com o campo "Cidade" de "A a E", e assim por diante. Há dois hosts, cada um com dois consumidores lendo das quatro partições em paralelo. As setas mostram os consumidores lendo de um ponto específico no feed de alterações. Na primeira partição, o azul mais escuro representa as alterações não lidas enquanto que o azul claro representa as alterações já lidas no feed de alterações. Os hosts de usam a coleção de concessão para armazenar um valor de "continuação" a fim de manter um registro da posição atual de leitura para cada consumidor.

![Exemplo de processador do feed de alterações](./media/change-feed-processor/changefeedprocessor.png)

### <a name="change-feed-and-provisioned-throughput"></a>Feed de alterações e taxa de transferência provisionada

Você é cobrado pelas RUs consumidas, pois a movimentação de dados para dentro e para fora dos contêineres do Cosmos sempre consome RUs. Você é cobrado pelas RUs consumidas pelo contêiner de concessão.

## <a name="additional-resources"></a>Recursos adicionais

* [Biblioteca do processador de feed de alterações do Azure Cosmos DB](sql-api-sdk-dotnet-changefeed.md)
* [Pacote NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [Exemplos adicionais sobre o GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

## <a name="next-steps"></a>Próximas etapas

Agora, você pode prosseguir para saber mais sobre o feed de alterações nos seguintes artigos:

* [Visão geral do feed de alterações](change-feed.md)
* [Maneiras de ler o feed de alterações](read-change-feed.md)
* [Usando o feed de alterações com o Azure Functions](change-feed-functions.md)
