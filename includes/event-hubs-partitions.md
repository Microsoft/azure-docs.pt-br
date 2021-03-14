---
title: incluir arquivo
description: incluir arquivo
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 01/05/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: f6bd0c13d5cbad802613e2bdea8fd6002f4deea2
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445771"
---
O Hub de Eventos organiza as sequências de eventos em uma ou mais partições. À medida que novos eventos chegam, eles são adicionados ao final dessa sequência. Uma partição pode ser pensada como "log de confirmação".

As partições bloqueiam os dados de evento que contêm o corpo do evento, um recipiente de propriedades definido pelo usuário que descreve o evento e os metadados como o deslocamento na partição, o número na sequência de fluxo dela e o carimbo de data/hora do lado do serviço no qual ele foi aceito.

![Diagrama que exibe a sequência de eventos mais antigos para os mais recentes.](./media/event-hubs-partitions/partition.png)

Os Hubs de Eventos são projetados para ajudar no processamento de grandes volumes de eventos, e o particionamento ajuda com isso de duas maneiras:

Primeiro, embora os Hubs de Eventos sejam um serviço de PaaS, há uma realidade física por baixo, e a manutenção de um log que preserva a ordem dos eventos exige que esses eventos sejam mantidos juntos no armazenamento subjacente e suas réplicas e que resultem em um teto de taxa de transferência para tal log. O particionamento permite que vários logs paralelos sejam usados para o mesmo Hub de Eventos e, portanto, multiplique a capacidade de taxa de transferência de E/S bruta disponível.

Em segundo lugar, seus próprios aplicativos devem ser capazes de acompanhar o processamento do volume de eventos que estão sendo enviados para um Hub de Eventos. Isso pode ser complexo e exige uma capacidade substancial, de expansão e de processamento paralelo. A lógica para partições é a mesma acima: A capacidade de um único processo de lidar com eventos é limitada e, portanto, você precisa de vários processos, e as partições são como sua solução alimenta esses processos e, ainda assim, garantem que cada evento tenha um proprietário de processamento claro. 

Os Hubs de Eventos mantêm os eventos por um período de retenção configurado que se aplica a todas as partições. Os eventos são removidos automaticamente quando o período de retenção é atingido. Se você especificar um período de retenção de um dia, o evento ficará indisponível exatamente 24 horas depois de ser aceito. Não é possível excluir eventos explicitamente. 

O tempo de retenção permitido é de até sete dias para os Hubs de Eventos Standard e até 90 dias para Hubs de Eventos Dedicados. Se você precisar arquivar eventos além do período de retenção permitido, é possível tê-los [armazenados automaticamente no Armazenamento do Azure ou Azure Data Lake ativando o recurso de Captura dos Hubs de Eventos](../articles/event-hubs/event-hubs-capture-overview.md) e, se precisar pesquisar ou analisar esses arquivos detalhados, é possível [importá-los facilmente para o Azure Synapse](../articles/event-hubs/store-captured-data-data-warehouse.md) ou outras lojas semelhantes e plataformas de análise. 

O motivo para o limite dos Hubs de Eventos na retenção de dados com base no tempo é evitar que grandes volumes de dados históricos do cliente sejam interceptados em um repositório profundo que está indexado apenas por um carimbo de data/hora e que permita acesso sequencial. A filosofia arquitetônica aqui é que os dados históricos precisam de indexação mais avançada e mais acesso direto do que a interface de eventos em tempo real que os Hubs de Eventos ou Kafka fornecem. Os mecanismos de fluxo de eventos não são adequados para desempenhar a função de data lakes ou arquivos de longo prazo para fornecimento de eventos. 

Como as partições são independentes e contêm sua própria sequência de dados, elas geralmente crescem a taxas diferentes. Nos Hubs de Eventos, isso não é uma preocupação que exige intervenção administrativa como seriam, por exemplo, no Apache Kafka, mas a distribuição desigual levará a um carregamento desigual nos processadores de eventos downstream.

![Hubs de Eventos](./media/event-hubs-partitions/multiple-partitions.png)

O número de partições é especificado na criação e precisa estar entre 1 e 32 nos Hubs de Eventos Standard. A contagem de partições pode ter até 2.000 partições por unidade de capacidade em Hubs de Eventos Dedicados. 

Recomendamos que você escolha pelo menos tantas partições quanto espera exigir nas [TU (unidades de produtividade)](../articles/event-hubs/event-hubs-faq.md#what-are-event-hubs-throughput-units) sustentadas durante o pico de carga do seu aplicativo para aquele Hub de Eventos em particular. É recomendável calcular com uma única partição que tem uma capacidade de taxa de transferência de 1 TU (1 MByte de entrada, 2 MByte de saída). É possível escalar as TUs no seu namespace ou nas unidades de capacidade do seu cluster independentemente da contagem de partições. Um Hub de Eventos com 32 partições ou um Hub de Eventos com uma partição incorre exatamente no mesmo custo quando o namespace é definido como capacidade de 1 TU. 

Os aplicativos controlam o mapeamento de eventos para partições de uma das três maneiras:

- Especificando a chave de partição, que é mapeada consistentemente (usando uma função de hash) para uma das partições disponíveis. 
- Não especificando uma chave de partição, que permite ao agente escolher aleatoriamente uma partição para um determinado evento.
- Enviando eventos explicitamente para uma partição específica.

A especificação de uma chave de partição permite manter eventos relacionados juntos na mesma partição e na ordem exata em que eles foram enviados. A chave de partição é uma cadeia de caracteres derivada do contexto do aplicativo e identifica a relação entre os eventos.

Uma sequência de eventos identificados por uma chave de partição é um *fluxo*. Uma partição é um repositório de logs multiplexado para muitos desses fluxos. 

A contagem de partições de um hub de eventos em um [cluster de Hubs de Eventos dedicados](../articles/event-hubs/event-hubs-dedicated-overview.md) pode ser [aumentada](../articles/event-hubs/dynamically-add-partitions.md) após o hub de eventos ter sido criado, mas a distribuição de fluxos entre partições será alterada quando for feita como o mapeamento de chaves de partição para alterações de partições. Portanto, você deve tentar evitar essas alterações se a ordem relativa dos eventos é importante em seu aplicativo.

Definir o número de partições com o valor máximo permitido é tentador, mas sempre tenha em mente que os fluxos de eventos precisam ser estruturados de um jeito que você possa tirar proveito de várias partições. Se você precisar de preservação de ordem absoluta em todos os eventos ou apenas em alguns subfluxos, talvez não seja possível tirar proveito de muitas partições. Além disso, muitas partições tornam o lado de processamento mais complexo. 

Embora as partições possam ser enviadas diretamente, isso não é recomendado. Em vez disso, será possível usar os constructos de nível superior apresentados na seção [Editores de eventos](../articles/event-hubs/event-hubs-features.md#event-publishers). 

