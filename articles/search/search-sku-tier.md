---
title: Escolher um tipo de preço
titleSuffix: Azure Cognitive Search
description: 'Saiba mais sobre os tipos de preço (ou SKUs) para Pesquisa Cognitiva do Azure. Um serviço de pesquisa pode ser provisionado nessas camadas: gratuita, básica e Standard. O Standard está disponível em várias configurações de recursos e níveis de capacidade.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.custom: contperf-fy21q2
ms.openlocfilehash: df218c2a4c066343ef571c6f24554ecaa806b639
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99987877"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Escolha um tipo de preço para o Azure Pesquisa Cognitiva

Parte da [criação de um serviço de pesquisa](search-create-service-portal.md) significa escolher um tipo de preço (ou SKU) que é corrigido para o tempo de vida do serviço. Os preços ou o custo mensal estimado de execução do serviço-são mostrados na página **Selecionar tipo de preço** do portal quando você cria o serviço. Se você estiver Provisionando por meio do PowerShell ou CLI do Azure em vez disso, a camada será especificada por meio do **`-Sku`** parâmetro e você deverá verificar o [preço do serviço](https://azure.microsoft.com/pricing/details/search/) para saber mais sobre os custos estimados.

A camada selecionada determina:

+ Número máximo de índices e outros objetos permitidos no serviço
+ Tamanho e velocidade de partições (armazenamento físico)
+ Taxa Faturável como um custo mensal fixo, mas também um custo incremental se você adicionar capacidade

Em algumas instâncias, a camada escolhida determina a disponibilidade dos [recursos premium](#premium-features).

> [!NOTE]
> Procurando informações sobre "SKUs do Azure"? Comece com os [preços do Azure](https://azure.microsoft.com/pricing/) e role para baixo para obter links para as páginas de preços por serviço.

## <a name="tier-descriptions"></a>Descrições da camada

As camadas incluem **gratuito**, **básico**, **Standard** e **otimizado para armazenamento**. O padrão e o armazenamento otimizado estão disponíveis com várias configurações e capacidades. A captura de tela a seguir de portal do Azure mostra as camadas disponíveis, menos preços (que você pode encontrar no portal e na [página de preços](https://azure.microsoft.com/pricing/details/search/). 

:::image type="content" source="media/search-sku-tier/tiers.png" alt-text="Gráfico de tipo de preço" border="true":::

**Gratuito** cria um serviço de pesquisa limitado para projetos menores, como a execução de tutoriais e exemplos de código. Internamente, os recursos do sistema são compartilhados entre vários assinantes. Você não pode dimensionar um serviço gratuito ou executar cargas de trabalho significativas.

**Básico** e **Standard** são as camadas faturáveis usadas com mais frequência, com **padrão** sendo o padrão, pois oferece mais flexibilidade no dimensionamento de cargas de trabalho. Com recursos dedicados sob seu controle, você pode implantar projetos maiores, otimizar o desempenho e aumentar a capacidade.

Algumas camadas são projetadas para determinados tipos de trabalho. Por exemplo, a **alta densidade padrão (S3 HD)** é um *modo de hospedagem* para S3, em que o hardware subjacente é otimizado para um grande número de índices menores e destina-se a cenários de multilocação. S3 HD tem o mesmo encargo por unidade que S3, mas o hardware é otimizado para leituras rápidas de arquivos em um grande número de índices menores.

As camadas de **armazenamento otimizado** oferecem maior capacidade de armazenamento a um preço menor por TB do que as camadas padrão. A compensação primária é maior latência de consulta, que você deve validar para os requisitos de aplicativo específicos. Para saber mais sobre as considerações de desempenho dessa camada, consulte [Considerações sobre desempenho e otimização](search-performance-optimization.md).

Você pode saber mais sobre as várias camadas na [página de preços](https://azure.microsoft.com/pricing/details/search/), no artigo limites de [serviço no Azure pesquisa cognitiva](search-limits-quotas-capacity.md) e na página do portal quando estiver provisionando um serviço.

<a name="premium-features"></a>

## <a name="feature-availability-by-tier"></a>Disponibilidade de recursos por camada

A maioria dos recursos está disponível em todas as camadas, incluindo a camada gratuita. Em alguns casos, a camada escolhida afetará sua capacidade de implementar um recurso. A tabela a seguir descreve as restrições de recurso relacionadas à camada de serviço.

| Recurso | Limitações |
|---------|-------------|
| [indexadores](search-indexer-overview.md) | Os indexadores não estão disponíveis no S3 HD.  |
| [Enriquecimento de IA](search-security-manage-encryption-keys.md) | É executado na camada gratuita, mas não é recomendado. |
| [Identidades gerenciadas ou confiáveis para acesso de saída (indexador)](search-howto-managed-identities-data-sources.md) | Não disponível na camada gratuita.|
| [Chaves de criptografia gerenciadas pelo cliente](search-security-manage-encryption-keys.md) | Não disponível na camada gratuita. |
| [Acesso de firewall IP](service-configure-firewall.md) | Não disponível na camada gratuita. |
| [Ponto de extremidade privado (integração com o link privado do Azure)](service-create-private-endpoint.md) | Para conexões de entrada para um serviço de pesquisa, não disponível na camada gratuita. Para conexões de saída por indexadores para outros recursos do Azure, não disponível em alta ou S3 HD. Para indexadores que usam habilidades, não disponível em gratuito, básico, S1 ou S3 HD.| 
| [Zonas de Disponibilidade](search-performance-optimization.md) | Não disponível na camada gratuita e na camada básica. |

Recursos com uso intensivo de recursos podem não funcionar bem, a menos que você forneça capacidade suficiente. Por exemplo, o [enriquecimento de ia](cognitive-search-concept-intro.md) tem habilidades de longa execução que atingirão o tempo limite em um serviço gratuito, a menos que o conjunto de informações seja pequeno.

## <a name="upper-limits"></a>Limites superiores

As camadas determinam o armazenamento máximo do serviço em si, bem como o número máximo de índices, indexadores, fontes de dados, habilidades e mapas de sinônimos que você pode criar. Para uma interrupção completa de todos os limites, consulte [limites de serviço no Azure pesquisa cognitiva](search-limits-quotas-capacity.md). 

## <a name="partition-size-and-speed"></a>Tamanho e velocidade da partição

Os preços de camada incluem detalhes sobre o armazenamento por partição que varia de 2 GB para básico, até 2 TB para camadas de armazenamento otimizado (L2). Outras características de hardware, como velocidade de operações, latência e taxas de transferência, não são publicadas, mas as camadas projetadas para arquiteturas de solução específicas são criadas em hardware que tem os recursos para dar suporte a esses cenários. Para obter mais informações sobre partições, consulte [estimar e gerenciar a capacidade](search-capacity-planning.md) e [a escala de desempenho](search-performance-optimization.md).

## <a name="billing-rates"></a>Taxas de cobrança

As camadas têm taxas de cobrança diferentes, com taxas mais altas para camadas que são executadas em hardware mais caro ou fornecem recursos mais caros. A taxa de cobrança por camada pode ser encontrada nas [páginas de preços do Azure](https://azure.microsoft.com/pricing/details/search/) para pesquisa cognitiva do Azure.

Depois de criar um serviço, a taxa de cobrança torna-se um *custo fixo* de execução do serviço em todo o relógio e um *custo incremental* se você optar por adicionar mais capacidade.

Os serviços de pesquisa são recursos de computação alocados na forma de *partições* (para armazenamento) e *réplicas* (instâncias do mecanismo de consulta). Inicialmente, um serviço é criado com um de cada, e a taxa de cobrança é inclusiva dos dois recursos. No entanto, se você dimensionar a capacidade, os custos aumentarão ou reduzirão em incrementos da taxa faturável.

O exemplo a seguir ilustra esse cenário. Suponha uma taxa de cobrança hipotética de $100 por mês. Se você mantiver o serviço de pesquisa em sua capacidade inicial de uma partição e uma réplica, $100 será o que você pode esperar pagar no final do mês. No entanto, se você adicionar mais duas réplicas para obter alta disponibilidade, a fatura mensal aumentará para $300 ($100 para o primeiro par de partição de réplica, seguido por $200 para as duas réplicas).

Esse modelo de preços se baseia no conceito de aplicação da taxa de cobrança às *unidades de pesquisa* de número (Su) usadas por um serviço de pesquisa. Todos os serviços são inicialmente provisionados em uma SU, mas você pode aumentar o SUs adicionando partições ou réplicas para lidar com cargas de trabalho maiores. Para obter mais informações, consulte [como estimar os custos de um serviço de pesquisa](search-sku-manage-costs.md).

## <a name="next-steps"></a>Próximas etapas

A melhor maneira de escolher um tipo de preço é começar com uma camada de menor custo e, em seguida, permitir que a experiência e o teste informam sua decisão de manter o serviço ou criar um novo em uma camada superior. Para as próximas etapas, recomendamos que você crie um serviço de pesquisa em uma camada que possa acomodar o nível de teste que você propõe fazer e, em seguida, examine as diretrizes a seguir para obter recomendações sobre como estimar o custo e a capacidade.

+ [Criar um serviço de pesquisa](search-create-service-portal.md)
+ [Estimar custos](search-sku-manage-costs.md)
+ [Estimar a capacidade](search-sku-manage-costs.md)