---
title: Adicionar partições dinamicamente a um hub de eventos nos Hubs de Eventos do Azure
description: Este artigo mostra como adicionar partições dinamicamente a um hub de eventos nos Hubs de Eventos do Azure.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: e6efdc7bab309f825032555c97f1e1128f5addd6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98625258"
---
# <a name="dynamically-add-partitions-to-an-event-hub-apache-kafka-topic-in-azure-event-hubs"></a>Adicionar partições dinamicamente a um hub de eventos (tópico do Apache Kafka) nos Hubs de Eventos do Azure
Os Hubs de Evento fornecem streaming de mensagens por meio de um padrão de consumidor particionado no qual cada consumidor lê somente um subconjunto específico, ou partição, do fluxo de mensagens. Esse padrão permite a escala horizontal para processamento de eventos e fornece outros recursos centrados no fluxo que não estão disponíveis em filas e tópicos. Uma partição é uma sequência ordenada de eventos que é mantida em um hub de eventos. À medida que novos eventos chegam, eles são adicionados ao final dessa sequência. Para obter mais informações sobre partições em geral, confira [Partições](event-hubs-scalability.md#partitions)

Você pode especificar o número de partições no momento da criação de um hub de eventos. Em alguns cenários, talvez seja necessário adicionar partições após o hub de eventos ser criado. Este artigo descreve como adicionar partições dinamicamente a um hub de eventos existente. 

> [!IMPORTANT]
> As adições dinâmicas de partições estão disponíveis apenas em clusters de Hubs de Eventos **Dedicados**.

> [!NOTE]
> Para clientes Apache Kafka, um **hub de eventos** é mapeado para um **tópico do Kafka**. Para obter mais mapeamentos entre os Hubs de Eventos do Azure e o Apache Kafka, confira [Mapeamento conceitual de Kafka e dos Hubs de Eventos](event-hubs-for-kafka-ecosystem-overview.md#kafka-and-event-hub-conceptual-mapping)


## <a name="update-the-partition-count"></a>Atualizar a contagem de partições
Esta seção mostra como atualizar a contagem de partições de um hub de eventos de diferentes maneiras (PowerShell, CLI e assim por diante).

### <a name="powershell"></a>PowerShell
Use o comando [Set-AzureRmEventHub](/powershell/module/azurerm.eventhub/Set-AzureRmEventHub) do PowerShell para atualizar partições em um hub de eventos. 

```azurepowershell-interactive
Set-AzureRmEventHub -ResourceGroupName MyResourceGroupName -Namespace MyNamespaceName -Name MyEventHubName -partitionCount 12
```

### <a name="cli"></a>CLI
Use o [`az eventhubs eventhub update`](/cli/azure/eventhubs/eventhub#az-eventhubs-eventhub-update) comando da CLI para atualizar partições em um hub de eventos. 

```azurecli-interactive
az eventhubs eventhub update --resource-group MyResourceGroupName --namespace-name MyNamespaceName --name MyEventHubName --partition-count 12
```

### <a name="resource-manager-template"></a>Modelo do Resource Manager
Atualize o valor da propriedade `partitionCount` no modelo do Resource Manager e reimplante o modelo para atualizar o recurso. 

```json
    {
        "apiVersion": "2017-04-01",
        "type": "Microsoft.EventHub/namespaces/eventhubs",
        "name": "[concat(parameters('namespaceName'), '/', parameters('eventHubName'))]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[resourceId('Microsoft.EventHub/namespaces', parameters('namespaceName'))]"
        ],
        "properties": {
            "messageRetentionInDays": 7,
            "partitionCount": 12
        }
    }
```

### <a name="apache-kafka"></a>Apache Kafka
Use a API `AlterTopics` (por exemplo, por meio da ferramenta CLI **kafka-topics**) para aumentar a contagem de partições. Para obter detalhes, confira os [tópicos sobre como modificar Kafka](http://kafka.apache.org/documentation/#basic_ops_modify_topic). 

## <a name="event-hubs-clients"></a>Clientes dos Hubs de Eventos
Vejamos como os clientes dos Hubs de Eventos se comportam quando a contagem de partições é atualizada em um hub de eventos. 

Quando você adiciona uma partição a um hub par existente, o cliente do hub de eventos recebe um `MessagingException` do serviço informando aos clientes que os metadados de entidade (entidade é o Hub de eventos e os metadados são as informações de partição) foram alterados. Os clientes reabrirão automaticamente os links de AMQP que, em seguida, coletarão as informações de metadados alteradas. Depois disso, os clientes operam normalmente.

### <a name="senderproducer-clients"></a>Clientes do remetente/produtor
Os Hubs de Eventos fornecem três opções de remetente:

- **Remetente da partição**: nesse cenário, os clientes enviam eventos diretamente para uma partição. Embora as partições sejam identificáveis e os eventos possam ser enviados diretamente a elas, não recomendamos esse padrão. A adição de partições não afeta esse cenário. Recomendamos que você reinicie os aplicativos para que eles possam detectar partições adicionadas recentemente. 
- **Remetente da chave de partição** – nesse cenário, os clientes enviam os eventos com uma chave para que todos os eventos que pertençam a essa chave terminem na mesma partição. Nesse caso, o serviço faz hash da chave e das rotas para a partição correspondente. A atualização de contagem de partições pode causar problemas fora de ordem devido à alteração de hash. Portanto, se você se preocupa com a ordenação, verifique se o aplicativo consome todos os eventos de partições existentes antes de aumentar a contagem de partições.
- **Remetente de Round Robin (padrão)** – neste cenário, o serviço de hubs de eventos Arredonde Robin os eventos entre partições e também usa um algoritmo de balanceamento de carga. O serviço de Hubs de Eventos reconhece alterações de contagem de partições e solicitará novas partições segundos após uma alteração da contagem de partições.

### <a name="receiverconsumer-clients"></a>Clientes de receptor/consumidor
Os Hubs de Eventos fornecem receptores diretos e uma biblioteca de consumidor fácil de usar, chamada de [Host do Processador de Eventos (SDK antigo)](event-hubs-event-processor-host.md) ou [Processador de Eventos (SDK novo)](event-processor-balance-partition-load.md).

- **Receptores diretos** – os receptores diretos escutam partições específicas. O comportamento de runtime deles não é afetado quando as partições são escaladas horizontalmente para um hub de eventos. O aplicativo que usa receptores diretos precisa cuidar para selecionar as novas partições e atribuir os receptores de acordo.
- **Host do processador de eventos** – esse cliente não atualiza automaticamente os metadados da entidade. Portanto, ele não detectaria o aumento da contagem de partições. A recriação de uma instância de processador de eventos causará uma busca de metadados de entidade que, por sua vez, criará blobs para as partições adicionadas recentemente. Os blobs pré-existentes não serão afetados. É recomendável reiniciar todas as instâncias do processador de eventos para garantir que todas as instâncias estejam cientes das partições adicionadas recentemente e que o balanceamento de carga seja manipulado corretamente entre os consumidores.

    Se você estiver usando a versão antiga do SDK do .NET ([WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)), o host do processador de eventos removerá um ponto de verificação existente na reinicialização se a contagem de partições no ponto de verificação não corresponder à contagem de partições buscada no serviço. Esse comportamento pode ter um impacto no seu aplicativo. 

## <a name="apache-kafka-clients"></a>Clientes do Apache Kafka
Esta seção descreve como clientes do Apache Kafka que usam o ponto de extremidade do Kafka dos Hubs de Eventos do Azure se comportam quando a contagem de partições é atualizada para um hub de eventos. 

Os clientes do Kafka que usam os Hubs de Eventos com o protocolo do Apache Kafka se comportam de maneira diferente dos clientes de hub de eventos que usam o protocolo AMQP. Os clientes do Kafka atualizam os respectivos metadados uma vez a cada `metadata.max.age.ms` milissegundos. Você especifica esse valor nas configurações do cliente. As bibliotecas `librdkafka` também usam a mesma configuração. As atualizações de metadados informam os clientes sobre alterações de serviço, incluindo os aumentos na contagem de partições. Para obter uma lista de configurações, consulte [configurações de Apache Kafka para os hubs de eventos](apache-kafka-configurations.md).

### <a name="senderproducer-clients"></a>Clientes do remetente/produtor
Os produtores sempre ditam que as solicitações de envio contêm o destino da partição para cada conjunto de registros produzidos. Portanto, todo o particionamento dos produtores é feito no lado do cliente, com a exibição do produtor mostrando os metadados do agente. Depois que as novas partições forem adicionadas à exibição de metadados do produtor, elas estarão disponíveis para solicitações do produtor.

### <a name="consumerreceiver-clients"></a>Clientes de receptor/consumidor
Quando um membro do grupo de consumidores executa uma atualização de metadados e seleciona as partições recém-criadas, esse membro inicia um rebalanceamento de grupo. Os metadados do consumidor serão atualizados para todos os membros do grupo e as novas partições serão atribuídas pelo líder de rebalanceamento alocado.

## <a name="recommendations"></a>Recomendações

- Se você usar a chave de partição com seus aplicativos de produtor e depender do hash de chave para garantir a ordenação em uma partição, a adição dinâmica de partições não é recomendada. 

    > [!IMPORTANT]
    > Enquanto os dados existentes preservarem a ordenação, o hash de partição será interrompido para mensagens com hash após a alteração na contagem de partições devido à adição de partições.
- A adição de uma partição a uma instância do hub de eventos ou tópico existente é recomendada nos seguintes casos:
    - Quando você usa o método padrão de envio de eventos
     - Kafka estratégias de particionamento padrão, exemplo – estratégia de atribuição de adesivo


## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre partições, confira [Partições](event-hubs-scalability.md#partitions).

