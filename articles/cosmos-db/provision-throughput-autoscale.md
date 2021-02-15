---
title: Criar contêineres e bancos de dados do Azure Cosmos no modo de dimensionamento automático.
description: Saiba mais sobre os benefícios, casos de uso e como provisionar bancos de dados e contêineres do Azure Cosmos no modo de dimensionamento automático.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/11/2020
ms.custom: seo-nov-2020
ms.openlocfilehash: ba0dd347c4ee2cb41b34c2fc34f1848a7295dc3a
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368657"
---
# <a name="create-azure-cosmos-containers-and-databases-with-autoscale-throughput"></a>Criar contêineres e bancos de dados do Azure Cosmos com taxa de transferência de dimensionamento automático
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

No Azure Cosmos DB, você pode configurar a taxa de transferência padrão (manual) ou autoescala provisionada em seus bancos de dados e contêineres. A taxa de transferência provisionada prodimensionada no Azure Cosmos DB permite que você **dimensione a taxa de transferência (ru/s) do seu banco de dados ou contêiner de forma automática e instantânea**. A taxa de transferência é dimensionada com base no uso, sem afetar a disponibilidade, a latência, a taxa de transferência ou o desempenho da carga de trabalho.

A taxa de transferência provisionada de dimensionamento automático é adequada para cargas de trabalho críticas que têm padrões de tráfego variáveis ou imprevisíveis e exigem SLAs em alto desempenho e escala. Este artigo descreve os benefícios e casos de uso da taxa de transferência provisionada de dimensionamento automático.

## <a name="benefits-of-autoscale"></a>Benefícios de dimensionamento automático

Os bancos de dados e contêineres do Azure Cosmos configurados com taxa de transferência provisionada de dimensionamento automático têm os seguintes benefícios:

* **Simple:** O dimensionamento automático remove a complexidade de gerenciar RU/s com script personalizado ou capacidade colocação em escala manual. 

* **Escalonável:** Os bancos de dados e contêineres escalam automaticamente a taxa de transferência provisionada conforme necessário. Não há interrupção nas conexões do cliente, aplicativos ou impacto nos SLAs do Azure Cosmos DB.

* **Econômico:** O dimensionamento automático ajuda a otimizar o uso de RU/s e de custos ao reduzir verticalmente quando não estiver em uso. Você paga apenas pelos recursos que suas cargas de trabalho precisam por hora. De todas as horas em um mês, se você definir dimensionamento automático RU/s (tmax) e usar o valor total tmax por 66% das horas ou menos, você economizará com o dimensionamento automático. Para saber mais, consulte o artigo [como escolher entre a taxa de transferência padrão (manual) e a autoescala provisionada](how-to-choose-offer.md) .

* **Altamente disponível:** Os bancos de dados e contêineres que usam dimensionamento automático usam o mesmo back-end do Azure Cosmos DB de alta disponibilidade, tolerante a falhas e distribuído globalmente para garantir a durabilidade e a alta disponibilidade dos dados.

## <a name="use-cases-of-autoscale"></a>Casos de uso do dimensionamento automático

Os casos de uso do dimensionamento automático incluem:

* **Cargas de trabalho variáveis ou imprevisíveis:** Quando suas cargas de trabalho têm picos variáveis ou imprevisíveis de uso, o dimensionamento automático ajuda a aumentar e reduzir verticalmente automaticamente com base no uso. Os exemplos incluem sites de varejo que têm diferentes padrões de tráfego, dependendo do sazonalidade; cargas de trabalho de IOT que têm picos em vários momentos do dia; aplicativos de linha de negócios que apresentam pico de uso algumas vezes por mês ou ano, e muito mais. Com o dimensionamento automático, você não precisa mais provisionar manualmente a capacidade máxima ou média. 

* **Novos aplicativos:** Se você estiver desenvolvendo um novo aplicativo e não tiver certeza sobre a taxa de transferência (RU/s) de que precisa, o dimensionamento automático facilitará o início. Você pode começar com o ponto de entrada de dimensionamento automático de 400-4000 RU/s, monitorar o uso e determinar a RU/s corretas ao longo do tempo.

* **Aplicativos usados com pouca frequência:** Se você tiver um aplicativo que só é usado por algumas horas várias vezes por dia, semana ou mês — como um aplicativo/Web/site de blog de baixo volume — o dimensionamento automático ajustará a capacidade de lidar com o pico de uso e reduzirá verticalmente quando estiver acima. 

* **Cargas de trabalho de desenvolvimento e teste:** Se você ou sua equipe usam bancos de dados e contêineres do Azure Cosmos durante as horas de trabalho, mas não precisam deles nas noites ou nos fins de semana, o dimensionamento automático ajuda a economizar custos, reduzindo ao mínimo quando não estiver em uso. 

* **Cargas de trabalho/consultas de produção agendadas:** Se você tiver uma série de solicitações, operações ou consultas agendadas que deseja executar durante períodos ociosos, poderá fazer isso facilmente com o dimensionamento automático. Quando você precisar executar a carga de trabalho, a taxa de transferência será dimensionada automaticamente para o necessário e reduzida verticalmente posteriormente. 

A criação de uma solução personalizada para esses problemas não só requer uma enorme quantidade de tempo, mas também apresenta complexidade na configuração ou no código do seu aplicativo. O dimensionamento automático habilita os cenários acima imediatamente e elimina a necessidade de dimensionamento personalizado ou manual da capacidade. 

## <a name="how-autoscale-provisioned-throughput-works"></a>Como a taxa de transferência provisionada de dimensionamento automático funciona

Ao configurar contêineres e bancos de dados com dimensionamento automático, você especifica a taxa de transferência máxima `Tmax` necessária. O Azure Cosmos DB dimensiona a taxa de transferência `T` como `0.1*Tmax <= T <= Tmax`. Por exemplo, se você definir a taxa de transferência máxima como 20.000 RU/s, a taxa de transferência será dimensionada entre 2.000 e 20.000 RU/s. Como o dimensionamento é automático e instantâneo, a qualquer momento, você pode consumir até o `Tmax` provisionado sem atraso. 

A cada hora, você será cobrado pela maior taxa de transferência `T` para a qual o sistema é dimensionado dentro da hora.

O ponto de entrada para taxa de transferência máxima de dimensionamento automático `Tmax` começa com 4.000 RU/s, que escala entre 400-4.000 RU/s. Você pode definir `Tmax` em incrementos de 1.000 RU/s e alterar o valor a qualquer momento.  

## <a name="enable-autoscale-on-existing-resources"></a>Habilitar dimensionamento automático em recursos existentes

Use o [portal do Azure](how-to-provision-autoscale-throughput.md#enable-autoscale-on-existing-database-or-container), o [modelo de Azure Resource Manager](how-to-provision-autoscale-throughput.md#azure-resource-manager), a [CLI](how-to-provision-autoscale-throughput.md#azure-cli) ou o [PowerShell](how-to-provision-autoscale-throughput.md#azure-powershell) para habilitar o dimensionamento automático em um banco de dados ou contêiner existente. Você pode alternar entre o dimensionamento automático e a taxa de transferência provisionada (manual) padrão a qualquer momento. Confira esta [documentação](autoscale-faq.md#how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work) para obter mais informações.

## <a name="throughput-and-storage-limits-for-autoscale"></a><a id="autoscale-limits"></a> Limites de taxa de transferência e armazenamento para dimensionamento automático

Para qualquer valor de `Tmax`, o banco de dados ou contêiner pode armazenar um total de `0.01 * Tmax GB`. Após essa quantidade de armazenamento ser atingida, a RU/s máxima será aumentada automaticamente com base no novo valor de armazenamento, sem impacto no aplicativo. 

Por exemplo, se você começar com o máximo de 50.000 RU/s (o que é dimensionado entre 5000 e 50.000 RU/s), pode armazenar até 500 GB de dados. Se você exceder 500 GB - por exemplo, o armazenamento agora é de 600 GB, o novo máximo de RU/s será 60.000 RU/s (o que é dimensionado entre 6000 e 60.000 RU/s).

Ao usar a taxa de transferência no nível do banco de dados com dimensionamento automático, você pode fazer com que os 25 primeiros contêineres compartilhem um máximo de RU/s de 4000 (o que é dimensionado entre 400 e 4000 RU/s), desde que você não exceda 40 GB de armazenamento. Confira esta [documentação](autoscale-faq.md#can-i-change-the-max-rus-on-the-database-or-container) para obter mais informações.

## <a name="comparison--containers-configured-with-manual-vs-autoscale-throughput"></a>Comparação – Contêineres configurados com taxa de transferência manual vs dimensionamento automático
Para obter mais detalhes, consulte esta [documentação](how-to-choose-offer.md) sobre como escolher entre a taxa de transferência padrão (manual) e o dimensionamento automático.  

|| Contêineres com taxa de transferência padrão (manual)  | Contêineres com taxa de transferência de dimensionamento automático |
|---------|---------|---------|
| **Taxa de transferência provisionada (RU/s)** | Provisionado manualmente. | Dimensionado de forma automática e instantânea com base nos padrões de uso da carga de trabalho. |
| **Limitação de taxa de solicitações/operações (429)**  | Pode acontecer se o consumo exceder a capacidade provisionada. | Não ocorrerá se você consumir RU/s dentro do intervalo de taxa de transferência de dimensionamento automático definido.    |
| **Planejamento da capacidade** |  Você precisa fazer o planejamento de capacidade e provisionar a taxa de transferência exata necessária. |    O sistema cuida automaticamente do planejamento de capacidade e do gerenciamento de capacidade. |
| **Preços** | Você paga pelas RU/s provisionadas manualmente por hora, usando a [taxa de RU/s padrão (manual) por hora](https://azure.microsoft.com/pricing/details/cosmos-db/). | Você paga por hora pela RU/s mais alta que o sistema dimensionou dentro de uma hora. <br/><br/> Para contas de região de gravação única, você paga pela RU/s usada por hora, usando a [taxa de RU/s de dimensionamento automático por hora](https://azure.microsoft.com/pricing/details/cosmos-db/). <br/><br/>Para contas com várias regiões de gravação, não há custo adicional para o dimensionamento automático. Você paga pela taxa de transferência usada por hora usando as mesmas [taxas de gravação de várias regiões/s por hora](https://azure.microsoft.com/pricing/details/cosmos-db/). |
| **Mais adequado para tipos de carga de trabalho** |  Cargas de trabalho previsíveis e estáveis|   Cargas de trabalho imprevisíveis e variáveis  |

## <a name="next-steps"></a>Próximas etapas

* Examine as [Perguntas frequentes sobre dimensionamento automático](autoscale-faq.md).
* Saiba como [escolher entre taxa de transferência manual e de dimensionamento automático](how-to-choose-offer.md).
* Saiba como [provisionar a taxa de transferência de dimensionamento automático em um banco de dados ou contêiner do Azure Cosmos](how-to-provision-autoscale-throughput.md).
* Saiba mais sobre [particionamento](partitioning-overview.md) no Azure Cosmos DB.


