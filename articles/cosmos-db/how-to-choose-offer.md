---
title: Como escolher entre manual e dimensionamento automático em Azure Cosmos DB
description: Saiba como escolher entre a taxa de transferência padrão (manual) e a taxa de transferência provisionada de dimensionamento automático para sua carga de trabalho.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: dech
ms.openlocfilehash: d8a6471d53ad4b2428504f9c53cbec6bc1967c49
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93089629"
---
# <a name="how-to-choose-between-standard-manual-and-autoscale-provisioned-throughput"></a>Como escolher entre a taxa de transferência padrão (manual) e a de dimensionamento automático provisionada 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O Azure Cosmos DB dá suporte a dois tipos ou ofertas de taxa de transferência provisionada: padrão (manual) e de dimensionamento automático. Os dois tipos de taxa de transferência são adequados para cargas de trabalho de missão crítica que exigem alto desempenho e escala, além disso, são apoiados pelos mesmos SLAs do Azure Cosmos DB no que diz respeito a taxa de transferência, disponibilidade, latência e consistência.

Este artigo descreve como escolher entre a taxa de transferência padrão (manual) e a de dimensionamento automático provisionada para sua carga de trabalho. 

## <a name="overview-of-provisioned-throughput-types"></a>Visão geral dos tipos de taxa de transferência provisionados
Antes de se aprofundar sobre a diferença entre a taxa padrão (manual) e a de dimensionamento automático, é importante entender primeiro como a taxa de transferência provisionada funciona no Azure Cosmos DB. 

Ao usar a taxa de transferência provisionada, você define a taxa de transferência, medida em unidades de solicitação por segundo (RU/s), que são necessárias para sua carga de trabalho. O serviço provisiona a capacidade necessária para dar suporte aos requisitos de taxa de transferência. As operações de banco de dados no serviço, tal como leituras, gravações e consultas, consomem alguma quantidade de unidades de solicitação (RUs). Saiba mais sobre [unidades de solicitação](request-units.md).

A tabela a seguir mostra uma comparação de alto nível entre o padrão (manual) e o dimensionamento automático.

|Descrição|Padrão (manual)|Autoscale|
|-------------|------|-------|
|Mais indicado para|Cargas de trabalho com tráfego constante ou previsível|Cargas de trabalho com tráfego variável ou imprevisível. Consulte os [casos de uso do dimensionamento automático](provision-throughput-autoscale.md#use-cases-of-autoscale).|
|Como ele funciona|Você provisiona uma quantidade definida de RU/s `T`, que é estática ao longo do tempo, a menos que você altere essa quantidade manualmente. A cada segundo, você pode usar uma taxa de transferência de até `T` RU/s. <br/><br/>Por exemplo, se você definir o padrão (manual) em 400 RU/s, a taxa de transferência permanecerá em 400 RU/s.|Você define o valor maior ou o máximo de RU/s `Tmax` que o sistema não deve exceder. O sistema dimensiona automaticamente a taxa de transferência `T` de modo que `0.1* Tmax <= T <= Tmax`. <br/><br/>Por exemplo, se você definir o dimensionamento automático a 4.000 RU/s, o sistema será dimensionado entre 400 e 4.000 RU/s.|
|Quando usar isso|Você quer gerenciar manualmente sua capacidade de taxa de transferência (RU/s) e dimensionar por conta própria.<br/><br/>Você tem uma utilização alta e consistente de RU/s provisionadas. De todas as horas em um mês, se você definir um valor de RU/s provisionadas `T` e usar 66% da quantidade total de horas ou mais, estima-se que você economizará com RU/s provisionadas padrão (manual).<br/><br/>Isso é baseado em uma comparação entre a configuração `T` em modo padrão (manual) e a mesma quantidade `Tmax` em dimensionamento automático. |Você deseja que o Azure Cosmos DB gerencie sua capacidade de taxa de transferência (RU/s) e escala com base no uso.<br/><br/>O seu uso de RU/s é variável ou difícil de prever. Do total de horas em um mês, se você definir o valor máximo de RU/s de dimensionamento automático `Tmax` e usar a quantidade total `Tmax` em 66% das horas ou menos, estima-se que você economizará com o dimensionamento automático.<br/><br/>Isso se baseia em uma comparação entre configurar o dimensionamento automático `Tmax` e o mesmo valor `T` na taxa de transferência padrão (manual).|
|Modelo de cobrança|A cobrança das RU/s provisionadas é feita por hora, independentemente de quantas RUs foram consumidas.<br/><br/>Exemplo: <li>Provisionar 400 RU/s</li><li>Hora 1: nenhuma solicitação</li><li>Hora 2: 400 RU/s de solicitações</li><br/><br/>Para as horas 1 e 2, você será cobrado 400 RU/s por ambas as horas nas tarifas [padrão (manual)](https://azure.microsoft.com/pricing/details/cosmos-db/).|A cobrança é feita por hora, para o valor mais alto de RU/s que o sistema escalou durante essa hora. <br/><br/>Exemplo: <li>Provisionar dimensionamento automático máximo de 4.000 RU/s (escalas entre 400 e 4.000 RU/s)</li><li>Hora 1: o sistema escalou verticalmente para o valor mais alto de 3.500 RU/s</li><li>Hora 2: o sistema foi reduzido para o mínimo de 400 RU/s (sempre 10% do `Tmax`), devido à ausência de uso</li><br/><br/>Você será cobrado por 3.500 RU/s na hora 1 e 400 RU/s na hora 2 nas [taxas de transferência provisionadas pelo dimensionamento automático](https://azure.microsoft.com/pricing/details/cosmos-db/). A taxa de dimensionamento automático por RU/s é 1,5 * a taxa padrão (manual).
|O que acontece se você exceder as RU/s provisionadas|As RU/s permanecem estáticas no que está provisionado. Todas as solicitações que consumirem além das RUs provisionadas em um segundo serão limitadas por taxa, com uma resposta que recomenda um tempo de espera antes de tentar novamente. Você pode aumentar ou diminuir as RU/s manualmente, se necessário.| O sistema dimensionará as RU/s até o valor máximo de dimensionamento automático. Todas as solicitações que consumirem para além do valor máximo de dimensionamento automático de RU/s em um segundo serão limitadas por taxa, com uma resposta que recomenda um tempo de espera antes de tentar novamente.|

## <a name="understand-your-traffic-patterns"></a>Entenda seus padrões de tráfego

### <a name="new-applications"></a>Novos aplicativos ###

Se você estiver criando um novo aplicativo e ainda não souber seu padrão de tráfego, poderá iniciar com as RU/s do ponto de entrada (ou com o mínimo de RU/s) para evitar excesso de provisionamento no início. Ou, se você tiver um aplicativo pequeno que não precise de alta escala, poderá provisionar apenas o mínimo de RU/s do ponto de entrada para otimizar o custo. Para aplicativos pequenos com um tráfego de baixo esperado, você também pode considerar o modo de capacidade sem [servidor](throughput-serverless.md) .

Se você planeja usar o padrão (manual) ou o dimensionamento automático, veja o que você deve considerar:

Se você provisionar RU/s padrão (manual) no ponto de entrada de 400 RU/s, não será possível consumir acima de 400 RU/s, a menos que você altere manualmente a taxa de transferência. Você será cobrado, por hora, 400 RU/s na taxa de taxa de transferência provisionada padrão (manual).

Se você provisionar a taxa de transferência de dimensionamento automático no ponto de entrada com no máximo 4.000 RU/s, o recurso será dimensionado entre 400 e 4.000 RU/s. Como o valor de cobrança da taxa de transferência de dimensionamento automático por RU/s equivale a 1,5x o valor da taxa padrão (manual), por horas em que o sistema foi reduzido para o mínimo de 400 RU/s, sua fatura será mais alta do que se você provisionar 400 RU/s manualmente. No entanto, com o dimensionamento automático, em qualquer momento, se o tráfego do aplicativo atingir o pico, você poderá consumir até 4.000 RU/s sem a necessidade de qualquer ação por parte do usuário. Em geral, você deve avaliar o benefício de poder consumir até o máximo de RU/s em qualquer momento com a taxa de 1,5x de dimensionamento automático.

Use a [calculadora de capacidade](estimate-ru-with-capacity-planner.md) do Azure Cosmos DB para estimar os requisitos de taxa de transferência. 

### <a name="existing-applications"></a>Aplicativos existentes ###

Se você tiver um aplicativo existente usando a taxa de transferência padrão (manual) provisionada, poderá usar as [Métricas do Azure Monitor](../azure-monitor/insights/cosmosdb-insights-overview.md) para determinar se o seu padrão de tráfego é adequado para o dimensionamento automático. 

Primeiro, localize a [métrica de consumo da unidade de solicitação normalizada](monitor-normalized-request-units.md#view-the-normalized-request-unit-consumption-metric) do seu banco de dados ou contêiner. A utilização normalizada é uma medida do quanto você está usando atualmente sua taxa de transferência padrão (manual) provisionada. Quanto mais próximo de 100% estiver o número, mais você estará usando as RU/s provisionadas. [Saiba mais](monitor-normalized-request-units.md#view-the-normalized-request-unit-consumption-metric) sobre a métrica.

Em seguida, determine como a utilização normalizada varia ao longo do tempo. Localize a maior utilização normalizada para cada hora. Em seguida, calcule a utilização média normalizada em todas as horas. Se você observar que a utilização média é menor que 66%, habilite o dimensionamento automático no banco de dados ou contêiner. Por outro lado, se a utilização média for maior que 66%, é recomendável permanecer na taxa de transferência padrão (manual) provisionada.

> [!TIP]
> Se sua conta estiver configurada para usar gravações de várias regiões e tiver mais de uma região, a taxa por 100 RU/s será a mesma para manual e dimensionamento automático. Isso significa que habilitar o dimensionamento automático não provoca nenhum custo adicional, independentemente da utilização. Como resultado, é sempre recomendável usar o dimensionamento automático com gravações de várias regiões quando você tem mais de uma região, para tirar proveito da economia de pagar apenas pela RU/s em que seu aplicativo é dimensionado. Se você tiver gravações de várias regiões e uma região, use a utilização média para determinar se o dimensionamento automático resultará em economia de custos. 

#### <a name="examples"></a>Exemplos

Vamos dar uma olhada em duas cargas de trabalho de exemplo diferentes e analisar se elas são adequadas para taxa de transferência manual ou de dimensionamento automático. Para ilustrar a abordagem geral, analisaremos três horas de histórico para determinar a diferença de custo entre o uso de manual e de dimensionamento automático. Para cargas de trabalho de produção, é recomendável usar de 7 a 30 dias de histórico (ou mais, se disponível) para estabelecer um padrão de uso de RU/s.

> [!NOTE]
> Todos os exemplos mostrados neste documento baseiam-se no preço de uma conta do Azure Cosmos implantada em uma região não governamental nos EUA. O preço e o cálculo variam de acordo com a região que você está usando, consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) Azure Cosmos DB para obter as informações de preços mais recentes.

Suposições:
- Suponha que, no momento, temos uma taxa de transferência manual de 30.000 RU/s. 
- Nossa região é configurada com gravações de região única, com uma região. Se tivéssemos várias regiões, multiplicamos o custo por hora pelo número de regiões.
- Use taxas de preço público para manual (US $ $0.08 USD por 100 RU/s por hora) e taxa de transferência de dimensionamento automático ($0.12 USD por 100 RU/s por hora) em contas de gravação de região única. Consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) para obter detalhes. 

#### <a name="example-1-variable-workload-autoscale-recommended"></a>Exemplo 1: carga de trabalho variável (recomendado dimensionamento automático)

Em primeiro lugar, examinamos o consumo de RU normalizado. Essa carga de trabalho tem tráfego variável, com consumo normalizado de RU, variando de 6% a 100%. Há picos ocasionais de 100% que são difíceis de prever, mas muitas horas com baixa utilização. 

:::image type="content" source="media/how-to-choose-offer/variable-workload_use_autoscale.png" alt-text="Carga de trabalho com tráfego variável-consumo de RU normalizado entre 6% e 100% para todas as horas":::

Vamos comparar o custo do provisionamento manual de 30.000 RU/s, versus definir o dimensionamento automático de RU/s para 30.000 (dimensiona entre 3000-30.000 RU/s). 

Agora, vamos analisar o histórico. Suponha que tenhamos a utilização descrita na tabela a seguir. A utilização média entre essas três horas é de 39%. Como a média de consumo de RU normalizada é inferior a 66%, salvamos usando o dimensionamento automático. 

Observe que, na hora 1, quando houver 6% de uso, o dimensionamento automático faturará RU/s para 10% do máximo de RU/s, que é o mínimo por hora. Embora o custo do dimensionamento automático possa ser maior do que a taxa de transferência manual em determinadas horas, desde que a utilização média seja inferior a 66% em todas as horas, o dimensionamento automático será mais barato no geral.

|Período de tempo  | Utilização |Autoescalar RU/s cobrado  |Opção 1: manual 30.000 RU/s  | Opção 2: dimensionamento automático entre 3000-30.000 RU/s |
|---------|---------|---------|---------|---------|
|Hora 1  | 6%  |     3000  |  30.000 * 0, 8/100 = $2.40        |   3000 * 0, 12/100 = $0.36      |
|Hora 2  | 100%  |     30,000    |  30.000 * 0, 8/100 = $2.40       |  30.000 * 0, 12/100 = $3.60      |
|Hora 3 |  11  |     3.300    |  30.000 * 0, 8/100 = $2.40       |    3300 * 0, 12/100 = $0.40     |
|**Total**   |  |        |  $7.20       |    $4.36 (39% de economia)    |

#### <a name="example-2-steady-workload-manual-throughput-recommended"></a>Exemplo 2: carga de trabalho estável (taxa de transferência manual recomendada)

Essa carga de trabalho tem tráfego constante, com consumo de RU normalizado, que varia de 72% a 100%. Com 30.000 RU/s provisionado, isso significa que estamos consumindo entre 21.600 e 30.000 RU/s.

:::image type="content" source="media/how-to-choose-offer/steady_workload_use_manual_throughput.png" alt-text="Carga de trabalho com tráfego estável-consumo de RU normalizado entre 72% e 100% por todas as horas":::

Vamos comparar o custo do provisionamento manual de 30.000 RU/s, versus definir o dimensionamento automático de RU/s para 30.000 (dimensiona entre 3000-30.000 RU/s).

Suponha que tenhamos o histórico de utilização conforme descrito na tabela. Nossa utilização média entre essas três horas é de 88%. Como a média de consumo de RU normalizada é maior que 66%, salvamos usando a taxa de transferência manual.

Em geral, se a utilização média em todas as 730 horas em um mês for maior que 66%, vamos salvar usando a taxa de transferência manual. 

| Período de tempo | Utilização |Autoescalar RU/s cobrado  |Opção 1: manual 30.000 RU/s  | Opção 2: dimensionamento automático entre 3000-30.000 RU/s |
|---------|---------|---------|---------|---------|
|Hora 1  | 72%  |     21.600   |  30.000 * 0, 8/100 = $2.40        |   21600 * 0, 12/100 = $2.59      |
|Hora 2  | 93%  |     28.000    |  30.000 * 0, 8/100 = $2.40       |  28.000 * 0, 12/100 = $3.36       |
|Hora 3 |  100%  |     30,000    |  30.000 * 0, 8/100 = $2.40       |    30.000 * 0, 12/100 = $3.60     |
|**Total**   |  |        |  $7.20       |    $9.55     |

> [!TIP]
> Com a taxa de transferência padrão (manual), você pode usar a métrica de utilização normalizada para estimar as RU/s reais que você pode usar se alternar para o dimensionamento automático. Multiplique a utilização normalizada em um momento determinado pelas RU/s padrão (manual) configuradas atualmente. Por exemplo, se você tiver provisionado 5.000 RU/s e a utilização normalizada for de 90%, o uso de RU/s será de 0,9 * 5.000 = 4.500 RU/s. Se você observar que o seu padrão de tráfego é variável, mas está acima ou abaixo do provisionado, poderá habilitar o dimensionamento automático e, em seguida, alterar a configuração de RU/s de dimensionamento automático de forma adequada.

#### <a name="how-to-calculate-average-utilization"></a>Como calcular a utilização média
Faturas de dimensionamento automático para as RU/s mais altas dimensionadas para em uma hora. Ao analisar o consumo de RU normalizado ao longo do tempo, é importante usar a maior utilização por hora ao calcular a média. 

Para calcular a média da maior utilização em todos os horários:
1. Defina a **agregação** na métrica de consumo de Noramlized ru como **máx**.
1. Selecione a **granularidade de tempo** para 1 hora.
1. Navegue até **Opções de gráfico**.
1. Selecione a opção gráfico de barras. 
1. Em **compartilhamento**, selecione a opção **baixar para o Excel** . Na planilha gerada, calcule a utilização média em todas as horas. 

:::image type="content" source="media/how-to-choose-offer/variable-workload-highest-util-by-hour.png" alt-text="Para ver o consumo de RU normalizado por hora, 1) selecione granularidade de tempo para 1 hora; 2) Editar configurações de gráfico; 3) Selecione a opção de gráfico de barras; 4) em compartilhamento, selecione a opção baixar para o Excel para calcular a média em todas as horas. ":::

## <a name="measure-and-monitor-your-usage"></a>Medição e monitoramento de uso
Ao longo do tempo, depois de escolher o tipo de taxa de transferência, monitore seu aplicativo e faça ajustes conforme necessário. 

Ao usar o dimensionamento automático, use o Azure Monitor para ver o máximo de RU/s de dimensionamento automático provisionado (**Taxa de transferência máxima de dimensionamento automático**) e a RU/s em que o sistema está atualmente dimensionado (**Taxa de transferência provisionada**). Abaixo está um exemplo de carga de trabalho variável ou imprevisível usando o dimensionamento automático. Observação quando não há tráfego, o sistema dimensiona as RU/s para o mínimo de 10% do máximo de RU/s, que neste caso é de 5.000 RU/s e 50.000 RU/s, respectivamente. 

:::image type="content" source="media/how-to-choose-offer/autoscale-metrics-azure-monitor.png" alt-text="Exemplo de carga de trabalho usando dimensionamento automático, com dimensionamento automático RU/s de 50.000 RU/s e taxa de transferência variando de 5000-50.000 RU/s":::

> [!NOTE]
> Quando você usa a taxa de transferência padrão (manual) provisionada, a métrica de **taxa de transferência provisionada** refere-se ao que você definiu como um usuário. Quando você usa a taxa de transferência de dimensionamento automático, essa métrica se refere às RU/s nas quais o sistema está atualmente dimensionado.

## <a name="next-steps"></a>Próximas etapas
* Use a [calculadora de RU](https://cosmos.azure.com/capacitycalculator/) para estimar a taxa de transferência para novas cargas de trabalho.
* Use o [Azure Monitor](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) para monitorar suas cargas de trabalho existentes.
* Saiba como [provisionar a taxa de transferência de dimensionamento automático em um banco de dados ou contêiner do Azure Cosmos](how-to-provision-autoscale-throughput.md).
* Examine as [Perguntas frequentes sobre dimensionamento automático](autoscale-faq.md).