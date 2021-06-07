---
title: Como escolher entre a taxa de transferência provisionada e sem servidor no Azure Cosmos DB
description: Saiba mais sobre como escolher entre a taxa de transferência provisionada e sem servidor para sua carga de trabalho.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/08/2021
ms.openlocfilehash: 3f5c3400f319a3f9d5f1544457b009f90d479634
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98049823"
---
# <a name="how-to-choose-between-provisioned-throughput-and-serverless"></a>Como escolher entre taxa de transferência provisionada e sem servidor
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB está disponível em dois modos de capacidade diferentes: [taxa de transferência provisionada](set-throughput.md) e sem [servidor](serverless.md). Você pode executar exatamente as mesmas operações de banco de dados em ambos os modos, mas a maneira como você é cobrado por essas operações é radicalmente diferente. O vídeo a seguir explica as principais diferenças entre esses modos e como eles se encaixam em diferentes tipos de cargas de trabalho:

> [!VIDEO https://www.youtube.com/embed/CgYQo6uHyt0]

## <a name="detailed-comparison"></a>Comparação detalhada

| Critérios | Taxa de transferência provisionada | Sem servidor |
| --- | --- | --- |
| Status | Disponível para o público geral | Em versão prévia |
| Mais indicado para | Cargas de trabalho com tráfego sustentado que exige desempenho previsível | Cargas de trabalho com tráfego intermitente ou imprevisível e taxa baixa de tráfego de média para pico |
| Como ele funciona | Para cada um de seus contêineres, você provisiona uma quantidade de produtividade expressa em [unidades de solicitação](request-units.md) por segundo. A cada segundo, essa quantidade de unidades de solicitação está disponível para suas operações de banco de dados. A taxa de transferência provisionada pode ser atualizada manualmente ou ajustada automaticamente com o [dimensionamento automático](provision-throughput-autoscale.md). | Você executa as operações de banco de dados em seus contêineres sem precisar provisionar nenhuma capacidade. |
| Distribuição geográfica | Disponível (número ilimitado de regiões do Azure) | Não disponível (as contas sem servidor só podem ser executadas em uma região do Azure) |
| Armazenamento máximo por contêiner | Ilimitado | 50 GB |
| Desempenho | Latência de 10 ms de < para leituras de ponto e gravações cobertas pelo SLA | < latência de 10 ms para leituras de ponto e < 30 ms para gravações cobertas pelo SLO |
| Modelo de cobrança | A cobrança das RU/s provisionadas é feita por hora, independentemente de quantas RUs foram consumidas. | A cobrança é feita por hora para a quantidade de RUs consumida pelas operações de banco de dados. |

> [!IMPORTANT]
> Algumas das limitações sem servidor podem ser facilitou ou removidas quando o servidor não estiver disponível de forma geral e **seus comentários** nos ajudarão a decidir! Alcance e conte-nos mais sobre sua experiência sem servidor: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

## <a name="estimating-your-expected-consumption"></a>Estimando o consumo esperado

Em algumas situações, pode ficar claro se a taxa de transferência provisionada ou sem servidor deve ser escolhida para uma determinada carga de trabalho. Para ajudar com essa decisão, você pode estimar o **consumo geral esperado**, ou seja, o número total de RUs que você pode consumir em um mês (você pode estimar isso com a ajuda da tabela mostrada [aqui](plan-manage-costs.md#estimating-serverless-costs))

**Exemplo 1**: espera-se que uma carga de trabalho seja estourada para um máximo de 500 ru/s e consuma um total de 20 milhões RUs em um mês.

- No modo de taxa de transferência provisionado, você provisionia um contêiner com 500 RU/s por um custo mensal de: $0.08 * 5 * 730 = **$29.20**
- No modo sem servidor, você pagaria pelo RUs consumido: $0.25 * 20 = **$5**

**Exemplo 2**: espera-se que uma carga de trabalho seja estourada para um máximo de 500 ru/s e consuma um total de 250 milhões RUs em um mês.

- No modo de taxa de transferência provisionado, você provisionia um contêiner com 500 RU/s por um custo mensal de: $0.08 * 5 * 730 = **$29.20**
- No modo sem servidor, você pagaria pelo RUs consumido: $0.25 * 250 = **$62.50**

(esses exemplos não são contábeis para o custo de armazenamento, que é o mesmo entre os dois modos)

> [!NOTE]
> Os custos mostrados no exemplo anterior são apenas para fins de demonstração. Consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) para obter as informações de preços mais recentes.

## <a name="next-steps"></a>Próximas etapas

- Leia mais sobre como [provisionar a taxa de transferência no Azure Cosmos DB](set-throughput.md)
- Leia mais sobre o [Azure Cosmos DB sem servidor](serverless.md)
- Familiarize-se com o conceito de [unidades de solicitação](request-units.md)
