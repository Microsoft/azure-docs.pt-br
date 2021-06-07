---
title: Visão geral dos hubs de eventos dedicados – Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral dos hubs de eventos dedicados do Azure, que oferece implantações de um único locatário de hubs de eventos.
ms.topic: article
ms.date: 10/23/2020
ms.openlocfilehash: 721acf354c7d14c1362b4f760982af37d59115f9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101715608"
---
# <a name="overview-of-event-hubs-dedicated"></a>Visão geral de Hubs de Eventos Dedicados

Os *clusters de hubs de eventos* oferecem implantações de locatário único para clientes com as necessidades de streaming mais exigentes. Essa oferta de locatário único tem um SLA de 99,99% garantido e está disponível apenas em nosso tipo de preço dedicado. Um cluster de Hubs de Eventos possível insere milhões de eventos por segundo com latência de subsegundos e capacidade garantida. Os namespaces e os hubs de eventos criados no cluster dedicado incluem todos os recursos da oferta padrão e muito mais, mas sem nenhum limite de entrada. Ele também inclui o recurso popular de [captura de hubs de eventos](event-hubs-capture-overview.md) sem custo adicional. Esse recurso permite que você faça automaticamente o lote e o log de fluxos de dados para o armazenamento do Azure ou Azure Data Lake. 

Os clusters são provisionados e cobrados por **unidades de capacidade (cus)**, uma quantidade alocada de CPU e recursos de memória. Você pode comprar 1, 2, 4, 8, 12, 16 ou 20 UCS para cada cluster. O quanto você pode ingerir e transmitir por CU depende de uma variedade de fatores, como os seguintes: 

- Número de produtores e consumidores
- Forma da carga
- Taxa de egresso

> [!NOTE]
> Todos os clusters de hubs de eventos são habilitados para Kafka por padrão e dão suporte a pontos de extremidade Kafka que podem ser usados por seus aplicativos baseados em Kafka existentes. Ter o Kafka habilitado no cluster não afeta os casos de uso não Kafka; Não há nenhuma opção ou é necessário desabilitar o Kafka em um cluster.

## <a name="why-dedicated"></a>Por que o dedicado?

Os hubs de eventos dedicados oferecem três benefícios atraentes para clientes que precisam de capacidade de nível empresarial:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>A locação única garante capacidade para melhor desempenho

Um cluster dedicado garante a capacidade em grande escala. Ele pode fazer a entrada de gigabytes de dados de streaming com o armazenamento totalmente durável e a latência de subsegundos para acomodar qualquer intermitência no tráfego. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Acesso inclusivo e exclusivo aos recursos 
A oferta dedicada inclui recursos como a captura sem custo adicional e acesso exclusivo a recursos futuros como o Bring Your Own Key (BYOK). O serviço também gerencia o balanceamento de carga, as atualizações do sistema operacional, OS patches de segurança e o particionamento. Portanto, você pode gastar menos tempo na manutenção da infraestrutura e mais tempo na criação de recursos do lado do cliente.  

#### <a name="cost-savings"></a>Economia de custos
Em volumes de entrada altos (>unidades de produtividade 100), um cluster custa significativamente menos por hora do que comprar uma quantidade comparável de unidades de produtividade na oferta padrão.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Cotas e limites de Hubs de Eventos Dedicados

A oferta de Hubs de Eventos Dedicados é cobrada a um preço mensal fixo, com um mínimo de quatro horas de uso. A camada dedicada oferece todos os recursos do plano padrão, mas com capacidade e limites de escala empresarial para clientes com cargas de trabalho exigentes. 

| Recurso | Standard | Dedicado |
| --- |:---|:---|
| Largura de banda | 20 TUs (até 40 TUs) | 20 CUs |
| Namespaces |  1 | 50 por CU |
| Hubs de Eventos |  10 por namespace | 1\.000 por namespace |
| Eventos de entrada | Pagamento por milhão de eventos | Incluso |
| Tamanho da mensagem | 1 milhão de bytes | 1 milhão de bytes |
| Partições | 32 por Hub de Eventos | 1\.024 por Hub de Eventos<br/>2\.000 por CU |
| Grupos de consumidores | 20 por Hub de Eventos | Nenhum limite por CU, 1000 por hub de eventos |
| Conexões orientadas | 1\.000 incluídos, 5.000 no máximo | 100 mil incluídos e máximo |
| [Retenção de eventos](event-hubs-features.md#event-retention) | Sete dias, 84 GB incluídos por CU | 90 dias, 10 TB incluídos por CU |
| Capturar | Pagamento por hora | Incluso |

Para obter mais cotas e limites, consulte [limites e cotas de hubs de eventos](event-hubs-quotas.md)

## <a name="how-to-onboard"></a>Como fazer a integração

A experiência de autoatendimento para [criar um cluster de hubs de eventos](event-hubs-dedicated-cluster-create-portal.md) por meio do [portal do Azure](https://aka.ms/eventhubsclusterquickstart) agora está em versão prévia. Se você tiver alguma dúvida ou precisar de ajuda com a integração ao Hubs de Eventos Dedicados, entre em contato com a [equipe dos hubs de eventos](mailto:askeventhubs@microsoft.com).

## <a name="faqs"></a>Perguntas frequentes

[!INCLUDE [event-hubs-dedicated-clusters-faq](../../includes/event-hubs-dedicated-clusters-faq.md)]

## <a name="next-steps"></a>Próximas etapas

Entre em contato com seu representante de vendas da Microsoft ou Suporte da Microsoft para obter detalhes adicionais sobre Hubs de Eventos Dedicados. Você também pode criar um cluster ou saber mais sobre os tipos de preço dos hubs de eventos visitando os links a seguir:

- [Criar um cluster de hubs de eventos por meio do portal do Azure](https://aka.ms/eventhubsclusterquickstart) 
- [Preço de Hubs de Eventos Dedicados](https://azure.microsoft.com/pricing/details/event-hubs/). Você também pode entrar em contato com seu representante de vendas da Microsoft ou com o Suporte da Microsoft para obter mais detalhes sobre a capacidade dos Hubs de Eventos Dedicados.
- As [perguntas frequentes sobre Hubs de Eventos](event-hubs-faq.md) contêm informações sobre preços e tiram algumas dúvidas sobre os Hubs de Eventos.
