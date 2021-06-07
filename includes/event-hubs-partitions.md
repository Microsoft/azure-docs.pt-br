---
title: incluir arquivo
description: incluir arquivo
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 03/15/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 1dd78ba3799573e05e4ebbf55887bae3d9674b7c
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310063"
---
Os Hubs de Eventos organizam sequências de eventos enviados a um hub de eventos em uma ou mais partições. À medida que novos eventos chegam, eles são adicionados ao final dessa sequência. 

![Hubs de Eventos](./media/event-hubs-partitions/multiple-partitions.png)

Uma partição pode ser considerada como um "log de confirmação". As partições armazenam dados de evento que contêm o corpo do evento, um recipiente de propriedades definido pelo usuário que descreve o evento e os metadados como o deslocamento na partição, o número na sequência de fluxo e o carimbo de data/hora do lado do serviço no qual ele foi aceito.

![Diagrama que exibe a sequência de eventos mais antigos para os mais recentes.](./media/event-hubs-partitions/partition.png)

### <a name="advantages-of-using-partitions"></a>Vantagens do uso de partições
Os Hubs de Eventos são projetados para ajudar no processamento de grandes volumes de eventos, e o particionamento ajuda com isso de duas maneiras:

- Embora os Hubs de Eventos sejam um serviço de PaaS, há uma realidade física subjacente. Além disso, a manutenção de um log que preserva a ordem dos eventos exige que esses eventos sejam mantidos juntos no armazenamento subjacente e nas respectivas réplicas, o que resulta em um teto de taxa de transferência para esse log. O particionamento permite que vários logs paralelos sejam usados para o mesmo hub de eventos, multiplicando, portanto, a capacidade de taxa de transferência de E/S bruta disponível.
- Seus aplicativos precisam conseguir acompanhar o processamento do volume de eventos que estão sendo enviados para um hub de eventos. Isso pode ser complexo e exige uma capacidade substancial, de expansão e de processamento paralelo. A capacidade de um só processo de lidar com os eventos é limitada. Portanto, é necessário ter vários processos. As partições são como a sua solução alimenta esses processos, garantindo, ainda, que cada evento tenha um proprietário de processamento claro. 

### <a name="number-of-partitions"></a>Número of partições
O número de partições é especificado na criação e precisa estar entre 1 e 32 nos Hubs de Eventos Standard. A contagem de partições pode ter até 2.000 partições por unidade de capacidade em Hubs de Eventos Dedicados. 

Recomendamos que você escolha pelo menos tantas partições quanto espera exigir nas [TU (unidades de produtividade)](../articles/event-hubs/event-hubs-faq.yml#what-are-event-hubs-throughput-units-) sustentadas durante o pico de carga do seu aplicativo para aquele Hub de Eventos em particular. É recomendável calcular com uma única partição que tem uma capacidade de taxa de transferência de 1 TU (1 MByte de entrada, 2 MByte de saída). É possível escalar as TUs no seu namespace ou nas unidades de capacidade do seu cluster independentemente da contagem de partições. Um Hub de Eventos com 32 partições ou um Hub de Eventos com uma partição incorre exatamente no mesmo custo quando o namespace é definido como capacidade de 1 TU. 

A contagem de partições de um hub de eventos em um [cluster de Hubs de Eventos dedicados](../articles/event-hubs/event-hubs-dedicated-overview.md) pode ser [aumentada](../articles/event-hubs/dynamically-add-partitions.md) após o hub de eventos ter sido criado, mas a distribuição de fluxos entre partições será alterada quando for feita como o mapeamento de chaves de partição para alterações de partições. Portanto, você deve tentar evitar essas alterações se a ordem relativa dos eventos é importante em seu aplicativo.

Definir o número de partições com o valor máximo permitido é tentador, mas sempre tenha em mente que os fluxos de eventos precisam ser estruturados de um jeito que você possa tirar proveito de várias partições. Se você precisar de preservação de ordem absoluta em todos os eventos ou apenas em alguns subfluxos, talvez não seja possível tirar proveito de muitas partições. Além disso, muitas partições tornam o lado de processamento mais complexo. 


### <a name="mapping-of-events-to-partitions"></a>Mapeamento de eventos para partições
Você pode usar uma chave de partição para mapear dados de evento de entrada em partições específicas para fins de organização de dados. A chave de partição é um valor fornecido pelo remetente passado para um hub de eventos. Ela é processada por meio de uma função de hash estática, que cria a atribuição de partição. Se você não especificar uma chave de partição ao publicar um evento, uma atribuição de round robin será usada.

O editor de eventos só está ciente da sua chave de partição, não da partição para a qual os eventos são publicados. Essa desassociação de chave e partição isenta o remetente da necessidade de saber muito sobre o processamento de downstream. Uma identidade por dispositivo ou exclusiva do usuário é uma boa chave de partição, mas outros atributos, como geografia, também podem ser usados para agrupar eventos relacionados em uma única partição.

A especificação de uma chave de partição permite manter eventos relacionados juntos na mesma partição e na ordem exata em que eles foram enviados. A chave de partição é uma cadeia de caracteres derivada do contexto do aplicativo e identifica a relação entre os eventos. Uma sequência de eventos identificados por uma chave de partição é um *fluxo*. Uma partição é um repositório de logs multiplexado para muitos desses fluxos. 

> [!NOTE]
> Embora você possa enviar eventos diretamente para as partições, não recomendamos isso, especialmente quando a alta disponibilidade é importante para você. Isso faz o downgrade da disponibilidade de um hub de eventos para o nível de partição. Para obter mais informações, confira [Disponibilidade e consistência](../articles/event-hubs/event-hubs-availability-and-consistency.md).

