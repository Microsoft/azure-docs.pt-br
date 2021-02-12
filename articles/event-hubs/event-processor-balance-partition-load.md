---
title: Balancear a carga de partição em várias instâncias – hubs de eventos do Azure | Microsoft Docs
description: Descreve como balancear a carga de partição entre várias instâncias do seu aplicativo usando um processador de eventos e o SDK dos hubs de eventos do Azure.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: af307058d0eda6b96c0811bccc245c09e2bdd27d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025037"
---
# <a name="balance-partition-load-across-multiple-instances-of-your-application"></a>Balancear a carga de partição entre várias instâncias do seu aplicativo
Para dimensionar seu aplicativo de processamento de eventos, você pode executar várias instâncias do aplicativo e fazer com que ele Equilibre a carga entre si. Nas versões mais antigas, o [EventProcessorHost](event-hubs-event-processor-host.md) permitia que você balanceie a carga entre várias instâncias do programa e eventos de ponto de verificação ao receber. Nas versões mais recentes (5,0 em diante), **EventProcessorClient** (.net e Java) ou **EventHubConsumerClient** (Python e JavaScript) permite que você faça o mesmo. O modelo de desenvolvimento é simplificado com o uso de eventos. Você assina os eventos nos quais está interessado registrando um manipulador de eventos. Se você estiver usando a versão antiga da biblioteca de cliente, consulte os seguintes guias de migração: [.net](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md), [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/migration-guide.md), [python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/servicebus/azure-servicebus/migration_guide.md)e [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/migrationguide.md).

Este artigo descreve um cenário de exemplo para usar várias instâncias para ler eventos de um hub de eventos e fornecer detalhes sobre os recursos do cliente do processador de eventos, que permite receber eventos de várias partições ao mesmo tempo e balancear a carga com outros consumidores que usam o mesmo grupo de consumidores e de Hub de eventos.

> [!NOTE]
> A chave para reduzir os Hubs de Eventos horizontalmente é o modelo de consumidor particionado. Em contraste com o [consumidores concorrentes](/previous-versions/msp-n-p/dn568101(v=pandp.10)) padrão, o padrão de consumidor particionado permite alta escala, removendo o gargalo de contenção e promovendo o paralelismo de ponta a ponta.

## <a name="example-scenario"></a>Cenário de exemplo

Como um cenário de exemplo, considere uma empresa de segurança inicial que monitora 100.000 residências. A cada minuto, ele obtém dados de vários sensores, como um detector de movimento, sensor aberto de porta/janela, detector de rompimento de vidro e assim por diante, instalado em cada casa. A empresa oferece um site da web para residentes monitorarem a atividade da sua casa quase em tempo real.

Cada sensor envia dados para um hub de eventos. O hub de eventos é configurado com 16 partições. Na extremidade de consumo, você precisa de um mecanismo que possa ler esses eventos, consolidá-los (filtrar, agregar etc.) e despejar o agregado em um blob de armazenamento, que é projetado para uma página da Web amigável.

## <a name="write-the-consumer-application"></a>Escrever o aplicativo de consumidor

Ao projetar o consumidor em um ambiente distribuído, o cenário deve lidar com os seguintes requisitos:

1. **Escala:** criar vários consumidores, com cada consumidor assumindo a propriedade de leitura de algumas partições de Hubs de eventos.
2. **O balanceamento de carga:** aumentar ou reduzir os consumidores dinamicamente. Por exemplo, quando um novo tipo de sensor (por exemplo, um detector de monóxido de carbono) é adicionado a cada página inicial, aumenta o número de eventos. Nesse caso, o operador (um ser humano) aumenta o número de instâncias de consumidor. Em seguida, o pool de consumidores pode redistribuir o número de partições que são proprietárias, para compartilhar a carga com os consumidores recém-adicionados.
3. **Retomada direta em caso de falhas:** Se um consumidor (**consumidor a**) falhar (por exemplo, a máquina virtual que hospeda o consumidor falhar repentinamente), outros consumidores poderão pegar as partições pertencentes ao **consumidor a** e continuar. Além disso, o ponto de continuação, chamado de *checkpoint* ou *offset*, deve estar no ponto exato em que **consumidor A** falhou, ou um pouco antes disso.
4. **Eventos de consumo:** Embora os três pontos anteriores lidem com o gerenciamento do consumidor, deve haver código para consumir os eventos e fazer algo útil com ele. Por exemplo, agregue-a e carregue-a no armazenamento de BLOBs.

## <a name="event-processor-or-consumer-client"></a>Processador de eventos ou cliente consumidor

Você não precisa criar sua própria solução para atender a esses requisitos. Os SDKs dos hubs de eventos do Azure fornecem essa funcionalidade. Em SDKs .NET ou Java, você usa um cliente de processador de eventos (EventProcessorClient) e em SDKs de JavaScript e Python, você usa o EventHubConsumerClient. Na versão antiga do SDK, era o host do processador de eventos (EventProcessorHost) que suportava esses recursos.

Para a maioria dos cenários de produção, recomendamos que você use o cliente do processador de eventos para ler e processar eventos. O cliente de processador destina-se a fornecer uma experiência robusta para o processamento de eventos em todas as partições de um hub de eventos de maneira eficaz e tolerante a falhas, fornecendo um meio de ponto de verificação do seu progresso. Os clientes do processador de eventos podem trabalhar de forma cooperativa no contexto de um grupo de consumidores para um determinado Hub de eventos. Os clientes gerenciarão automaticamente a distribuição e o balanceamento de trabalho, pois as instâncias ficam disponíveis ou não estão disponíveis para o grupo.

## <a name="partition-ownership-tracking"></a>Acompanhamento de propriedade da partição

Uma instância do processador de eventos normalmente possui e processa eventos de uma ou mais partições. A propriedade de partições é distribuída uniformemente entre todas as instâncias de processador de eventos ativas associadas a uma combinação de Hub de eventos e grupo de consumidores. 

Cada processador de eventos recebe um identificador exclusivo e a propriedade de declarações de partições Adicionando ou atualizando uma entrada em um repositório de ponto de verificação. Todas as instâncias do processador de eventos se comunicam com esse armazenamento periodicamente para atualizar seu próprio estado de processamento, bem como para saber mais sobre outras instâncias ativas. Esses dados são usados para balancear a carga entre os processadores ativos. Novas instâncias podem ingressar no pool de processamento para escalar verticalmente. Quando as instâncias ficam inativas, devido a falhas ou para reduzir verticalmente, a propriedade da partição é transferida normalmente para outros processadores ativos.

Os registros de propriedade de partição no repositório de ponto de verificação controlam o namespace dos hubs de eventos, o nome do hub de eventos, o grupo de consumidores, o identificador do processador de eventos (também conhecido como proprietário), a ID da partição e a hora da última modificação.



| Namespace do Hubs de Eventos               | Nome do Hub de Eventos | **Grupo de consumidores** | Proprietário                                | Partition ID | Hora da última modificação  |
| ---------------------------------- | -------------- | :----------------- | :----------------------------------- | :----------- | :------------------ |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumer    | 3be3f9d3-9d9e-4c50-9491-85ece8334ff6 | 0            | 2020-01-15T01:22:15 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumer    | f5cc5176-ce96-4bb4-bbaa-a0e3a9054ecf | 1            | 2020-01-15T01:22:17 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumer    | 72b980e9-2efc-4ca7-ab1b-ffd7bece8472 | 2            | 2020-01-15T01:22:10 |
|                                    |                | :                  |                                      |              |                     |
|                                    |                | :                  |                                      |              |                     |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumer    | 844bd8fb-1f3a-4580-984d-6324f9e208af | 15           | 2020-01-15T01:22:00 |

Cada instância do processador de eventos adquire a propriedade de uma partição e começa a processar a partição do último [ponto de verificação](# Checkpointing)conhecido. Se um processador falhar (a VM é desligada), outras instâncias o detectam examinando a hora da última modificação. Outras instâncias tentam obter a propriedade das partições anteriormente pertencentes à instância inativa e o armazenamento de ponto de verificação garante que apenas uma das instâncias tenha sucesso ao reivindicar a propriedade de uma partição. Portanto, em qualquer dado momento, há no máximo um processador que recebe eventos de uma partição.

## <a name="receive-messages"></a>Receber mensagens

Ao criar um processador de eventos, você especifica as funções que processarão eventos e erros. Cada chamada para a função que processa eventos fornece um único evento de uma partição específica. É sua responsabilidade lidar com esse evento. Se você quiser garantir que o consumidor processe cada mensagem pelo menos uma vez, você precisará escrever seu próprio código com a lógica de repetição. Mas tenha cuidado com mensagens suspeitas.

É recomendável que você faça coisas de forma relativamente rápida. Ou seja, faça o menor processamento possível. Se você precisar gravar no armazenamento e fazer algum roteamento, é melhor usar dois grupos de consumidores e ter dois processadores de eventos.

## <a name="checkpointing"></a>Definindo o ponto de verificação

O *ponto de verificação* é um processo pelo qual um processador de eventos marca ou confirma a posição do último evento processado com êxito em uma partição. Marcar um ponto de verificação normalmente é feito dentro da função que processa os eventos e ocorre em uma base por partição dentro de um grupo de consumidores. 

Se um processador de eventos se desconectar de uma partição, outra instância poderá retomar o processamento da partição no ponto de verificação que foi confirmado anteriormente pelo último processador dessa partição nesse grupo de consumidores. Quando o processador se conecta, ele passa o deslocamento para o Hub de eventos para especificar o local no qual começar a ler. Dessa forma, você pode usar o ponto de verificação para marcar eventos como "concluídos" por aplicativos downstream e fornecer resiliência quando um processador de eventos ficar inativo. É possível retornar aos dados mais antigos especificando um deslocamento inferior desse processo de ponto de verificação. 

Quando o ponto de verificação é executado para marcar um evento como processado, uma entrada no repositório de ponto de verificação é adicionada ou atualizada com o deslocamento e o número de sequência do evento. Os usuários devem decidir a frequência de atualização do ponto de verificação. A atualização após cada evento processado com êxito pode ter implicações de desempenho e custo, pois dispara uma operação de gravação para o armazenamento de ponto de verificação subjacente. Além disso, o ponto de verificação de cada evento único é um indício de um padrão de mensagens em fila para o qual uma fila do barramento de serviço pode ser uma opção melhor do que um hub de eventos. A ideia por trás dos Hubs de eventos é que você obtenha entregas "pelo menos uma vez" em grande escala. Ao tornar seus sistemas downstream idempotentes, é fácil recuperar-se de falhas ou reinicializações que resultam na recepção dos mesmos eventos várias vezes.

> [!NOTE]
> Se você estiver usando o armazenamento de BLOBs do Azure como o armazenamento de ponto de verificação em um ambiente que dá suporte a uma versão diferente do SDK do Storage BLOB que os normalmente estão disponíveis no Azure, você precisará usar o código para alterar a versão da API do serviço de armazenamento para a versão específica com suporte desse ambiente. Por exemplo, se você estiver executando os [hubs de eventos em um hub de Azure Stack versão 2002](/azure-stack/user/event-hubs-overview), a versão mais alta disponível para o serviço de armazenamento é a versão 2017-11-09. Nesse caso, você precisa usar o código para direcionar a versão da API do serviço de armazenamento para 2017-11-09. Para obter um exemplo de como direcionar uma versão de API de armazenamento específica, consulte estes exemplos no GitHub: 
> - [.Net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/). 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript) ou  [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/)

## <a name="thread-safety-and-processor-instances"></a>Instâncias de processador e de segurança do thread

Por padrão, a função que processa os eventos é chamada sequencialmente para uma determinada partição. Os eventos subsequentes e as chamadas para essa função da mesma fila em diante nos bastidores à medida que a bomba do evento continua a ser executada em segundo plano em outros threads. Os eventos de partições diferentes podem ser processados simultaneamente e qualquer estado compartilhado que seja acessado entre partições deve ser sincronizado.

## <a name="next-steps"></a>Próximas etapas
Consulte o seguinte início rápido:

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
