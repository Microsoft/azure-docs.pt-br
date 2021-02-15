---
title: Azure Monitor a agregação de métricas de métricas e a exibição explicada
description: Informações detalhadas sobre como as métricas são agregadas no Azure Monitor
author: rboucher
ms.author: robb
services: azure-monitor
ms.topic: conceptual
ms.date: 01/12/2020
ms.subservice: metrics
ms.openlocfilehash: 1d83ef07714e0ce69f01aa240cc3058195c7b1af
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251974"
---
# <a name="azure-monitor-metrics-metrics-aggregation-and-display-explained"></a>Azure Monitor a agregação de métricas de métricas e a exibição explicada

Este artigo explica a agregação de métricas no banco de dados Azure Monitor série temporal que volta Azure Monitor [métricas de plataforma](data-platform.md) e [métricas personalizadas](metrics-custom-overview.md). Este artigo também se aplica a [métricas de Application insights](../app/app-insights-overview.md)padrão. 

Este é um tópico complexo e não é necessário entender todas as informações neste artigo para usar Azure Monitor métricas com eficiência.

## <a name="overview-and-terms"></a>Visão geral e termos

Quando você adiciona uma métrica a um gráfico, o Metrics Explorer seleciona automaticamente sua agregação padrão. O padrão faz sentido nos cenários básicos, mas você pode usar agregações diferentes para obter mais informações sobre a métrica. A exibição de agregações diferentes em um gráfico requer que você entenda como as métricas Explorer as manipula.

Primeiro, vamos definir alguns termos claramente:

- **Valor de métrica** – um valor de medida único coletado para um recurso específico.
- **Período de tempo** – um período de tempo genérico.
- **Intervalo de tempo** – o período de tempo entre a coleta de dois valores de métrica. 
- **Intervalo de tempo** – o período de tempo exibido em um gráfico. O padrão típico é 24 horas. Somente intervalos específicos estão disponíveis. 
- **Granularidade de tempo** ou **intervalo de tempo** – o período de tempo usado para agregar valores em conjunto para permitir a exibição em um gráfico. Somente intervalos específicos estão disponíveis. O mínimo atual é 1 minuto. O valor de granularidade de tempo deve ser menor do que o intervalo de tempo selecionado para ser útil; caso contrário, apenas um valor é mostrado para todo o gráfico. 
- **Tipo de agregação** – um tipo de estatística calculada de vários valores de métrica.  
- **Agregação** – o processo de pegar vários valores de entrada e, em seguida, usá-los para produzir um único valor de saída por meio das regras definidas pelo tipo de agregação. Por exemplo, usando uma média de vários valores.  

Métricas são uma série de valores de métrica capturados em um intervalo de tempo regular. Quando você plota um gráfico, os valores da métrica selecionada são agregados separadamente na granularidade do tempo (também conhecido como intervalo de tempo). Você seleciona o tamanho da granularidade de tempo usando o [painel Metrics Explorer seletor de tempo](metrics-getting-started.md#select-a-time-range). Se você não fizer uma seleção explícita, a granularidade de tempo será selecionada automaticamente com base no intervalo de tempo atualmente selecionado. Depois de selecionado, os valores de métrica que foram capturados durante cada intervalo de granularidade de tempo são agregados e colocados no gráfico-um DataPoint por intervalo.

## <a name="aggregation-types"></a>Tipos de agregação 

Há cinco tipos de agregação básicos disponíveis no Metrics Explorer. O Metrics Explorer oculta as agregações que são irrelevantes e não podem ser usadas para uma determinada métrica. 

- **Sum** – a soma de todos os valores capturados sobre o intervalo de agregação. Às vezes, chamado de agregação total.
- **Contagem** – o número de medições capturadas no intervalo de agregação. Count não examina o valor da medida, apenas o número de registros. 
- **Média** – a média dos valores de métrica capturados no intervalo de agregação. Para a maioria das métricas, esse valor é soma/contagem. 
- **Min** – o menor valor capturado sobre o intervalo de agregação.
- **Max** – o maior valor capturado sobre o intervalo de agregação.

Por exemplo, suponha que um gráfico esteja mostrando a métrica **total da rede** para uma VM usando a agregação **sum** durante o último período de 24 horas. O intervalo de tempo e a granularidade podem ser alterados do canto superior direito do gráfico, conforme mostrado na captura de tela a seguir.

:::image type="content" source="media/metrics-aggregation-explained/time-range-granularity-picker.png" alt-text="Captura de tela mostrando o intervalo de tempo e o seletor de granularidade de tempo" border="true":::

Para granularidade de tempo = 30 minutos e o intervalo de tempo = 24 horas:

- O gráfico é desenhado a partir de 48 pontos de extremidade. Isso é 24 horas x 2 pontos de extremidade por hora (60 min/30 min) agregados de 1 minuto. 
- O gráfico de linhas conecta 48 pontos na área de plotagem do gráfico. 
- Cada DataPoint representa a soma de todos os bytes de saída de rede enviados durante cada um dos períodos de tempo de 30 minutos relevantes. 


 :::image type="content" source="media/metrics-aggregation-explained/24-hour-30-min-gran.png" alt-text="Captura de tela mostrando dados em um grafo de linha definido como intervalo de tempo de 24 horas e granularidade de tempo de 30 minutos" border="true" lightbox="media/metrics-aggregation-explained/24-hour-30-min-gran.png":::

*Clique nas imagens desta seção para ver as versões maiores.*

Se você alternar a granularidade de tempo para 15 minutos, o gráfico será desenhado a partir de 96 pontos de dados agregados. Ou seja, 60 min/15 min = 4 pontos de extremidade por hora x 24 horas.

:::image type="content" source="media/metrics-aggregation-explained/24-hour-15-min-gran.png" alt-text="Captura de tela mostrando dados em um grafo de linha definido como intervalo de tempo de 24 horas e granularidade de tempo de 15 minutos" border="true" lightbox="media/metrics-aggregation-explained/24-hour-15-min-gran.png":::

Para a granularidade de tempo de 5 minutos, você obtém 24 x (60/5) = 288 pontos. 

:::image type="content" source="media/metrics-aggregation-explained/24-hour-5-min-gran.png" alt-text="Captura de tela mostrando dados em um grafo de linha definido como intervalo de tempo de 24 horas e granularidade de tempo de 5 minutos" border="true" lightbox="media/metrics-aggregation-explained/24-hour-15-min-gran.png":::

Para a granularidade de tempo de 1 minuto (o menor possível no gráfico), você obtém 24 x 60/1 = 1440 pontos. 

:::image type="content" source="media/metrics-aggregation-explained/24-hour-1-min-gran.png" alt-text="Captura de tela mostrando dados em um grafo de linha definido como intervalo de tempo de 24 horas e granularidade de tempo de 1 minuto" border="true" lightbox="media/metrics-aggregation-explained/24-hour-1-min-gran.png":::

Os gráficos parecem diferentes para essas somas, conforme mostrado nas capturas de tela anteriores.  Observe como essa VM tem muita saída em um período de tempo pequeno em relação ao restante da janela de tempo.  

A granularidade de tempo permite ajustar a proporção de "sinal para ruído" em um gráfico. As agregações mais altas removem os picos de ruído e suavização.  Observe as variações no gráfico de 1 minuto inferior e como elas são suavizadas à medida que você passa para valores de granularidade mais altos. 

Esse comportamento de suavização é importante quando você envia esses dados para outros sistemas, por exemplo, alertas. Normalmente, normalmente você não deseja ser alertado por picos muito curtos no tempo de CPU em mais de 90%. Mas se a CPU permanecer em 90% por 5 minutos, isso provavelmente é importante. Se você configurar uma regra de alerta na CPU (ou em qualquer métrica), tornar a granularidade de tempo maior pode reduzir o número de alertas falsos recebidos. 

É importante estabelecer o que é "normal" para sua carga de trabalho saber qual intervalo de tempo é melhor. Esse é um dos benefícios de [alertas dinâmicos](alerts-dynamic-thresholds.md), que é um tópico diferente não abordado aqui.  

## <a name="how-the-system-collects-metrics"></a>Como o sistema coleta métricas

A coleta de dados varia de acordo com a métrica. Há dois tipos de períodos de coleta.

### <a name="measurement-collection-frequency"></a>Frequência de coleta de medida 

- **Regular** -a métrica é coletada em um intervalo de tempo consistente que não varia.

- **Baseado em atividade** -a métrica é coletada com base em quando uma transação de um determinado tipo ocorre. Cada transação tem uma entrada de métrica e um carimbo de data/hora. Eles não são coletados em intervalos regulares, portanto, há um número variável de registros em um determinado período de tempo. 

### <a name="granularity"></a>Granularidade

O intervalo de tempo mínimo é de 1 minuto, mas o sistema subjacente pode capturar dados mais rapidamente dependendo da métrica. Por exemplo, a porcentagem de CPU é controlada a cada 15 segundos em um intervalo regular. Como as falhas de HTTP são rastreadas como transações, elas podem facilmente exceder muito mais de um minuto. Outras métricas, como o armazenamento do SQL, são capturadas a cada 20 minutos. Essa opção é até o provedor de recursos individual e o tipo. A maioria tenta fornecer o menor intervalo possível.

### <a name="dimensions-splitting-and-filtering"></a>Dimensões, divisão e filtragem

As métricas são capturadas para cada recurso individual. No entanto, o nível no qual as métricas são coletadas, armazenadas e conseguirá ser gráfico pode variar. Esse nível é representado por métricas adicionais disponíveis nas **dimensões de métricas**. Cada provedor de recursos individual Obtém a definição de como os dados coletados são detalhados. Azure Monitor define apenas como esses detalhes devem ser apresentados e armazenados. 

Quando você fornece um gráfico de uma métrica no Metrics Explorer, tem a opção de "dividir" o gráfico por uma dimensão.  Dividir um gráfico significa que você está procurando os dados subjacentes para obter mais detalhes e ver os dados em gráfico ou filtrados no Metrics Explorer.

Por exemplo, [Microsoft. ApiManagement/Service](metrics-supported.md#microsoftapimanagementservice) tem o *local* como uma dimensão para muitas métricas. 

- A **capacidade** é uma dessas métricas. Ter a dimensão de *local* implica que o sistema subjacente está armazenando um registro de métrica para a capacidade de cada local, em vez de apenas um para o valor agregado. Em seguida, você pode recuperar ou dividir essas informações em um gráfico de métrica.  

- Observando a **duração geral das solicitações de gateway**, há duas dimensões *Location* e *hostname*, novamente informando que você sabe o local de uma duração e de qual nome de host ele veio.  

- Uma das métricas mais flexíveis, **solicitações**, tem 7 dimensões diferentes. 
 
Verifique o artigo Azure Monitor [métricas com suporte](metrics-supported.md) para obter detalhes sobre cada métrica e as dimensões disponíveis. Além disso, a documentação para cada provedor de recursos e tipo pode fornecer informações adicionais sobre as dimensões e o que elas medem.

Você pode usar a divisão e a filtragem em conjunto para se aprofundar em um problema. Abaixo está um exemplo de um gráfico que mostra os *bytes médios de gravação de disco* para um grupo de VMs em um grupo de recursos. Temos um acúmulo de todas as VMs com essa métrica, mas talvez queiramos nos aprofundar para ver quais são, na verdade, responsáveis pelos picos em relação a 6h. Eles são o mesmo computador? Quantas máquinas estão envolvidas?  

:::image type="content" source="media/metrics-aggregation-explained/total-disk write-bytes-all-VMs.png" alt-text="Captura de tela mostrando total de bytes de gravação do disco para todas as máquinas virtuais no grupo de recursos de hotéis da contoso" border="true" lightbox="media/metrics-aggregation-explained/total-disk write-bytes-all-VMs.png":::

*Clique nas imagens desta seção para ver as versões maiores.*

Quando aplicamos a divisão, podemos ver os dados subjacentes, mas isso é um pouco de bagunça. Acontece que há 20 VMs sendo agregadas no gráfico acima. Nesse caso, usamos nosso mouse para focalizar o grande pico em 6h que nos diz que CH-DCVM11 é a causa. Mas é difícil ver o restante dos dados associados a essa VM por causa de outras VMs obstruindo o gráfico. 

:::image type="content" source="media/metrics-aggregation-explained/split-total-disk write-bytes-all-VMs.png" alt-text="Captura de tela mostrando bytes de gravação de disco para todas as máquinas virtuais no grupo de recursos de hotéis da Contoso dividida por nome de máquina virtual" border="true" lightbox="media/metrics-aggregation-explained/split-total-disk write-bytes-all-VMs.png":::

O uso da filtragem nos permite limpar o gráfico para ver o que realmente está acontecendo. Você pode marcar ou desmarcar as VMs que deseja ver. Observe as linhas pontilhadas. Eles são mencionados em uma seção posterior.

:::image type="content" source="media/metrics-aggregation-explained/split-filter-total-disk write-bytes-all-VMs.png" alt-text="Captura de tela mostrando bytes de gravação de disco para todas as máquinas virtuais na contoso grupo de recursos de hotéis dividir e filtrado pelo nome da máquina virtual" border="true" lightbox="media/metrics-aggregation-explained/split-filter-total-disk write-bytes-all-VMs.png":::

Para obter mais informações sobre como mostrar dados de dimensão dividida em um gráfico do Gerenciador de métricas, consulte [recursos avançados do Metrics Explorer – filtros e divisão](metrics-charts.md#filters).

### <a name="null-and-zero-values"></a>Valores nulos e zero

Quando o sistema espera dados de métrica de um recurso, mas não o recebe, ele registra um valor nulo.  NULL é diferente de um valor zero, que se torna importante no cálculo de agregações e gráficos. Valores nulos não são contados como medidas válidas. 

Os nulos aparecem de forma diferente em diferentes gráficos. As plotagens de dispersão ignoram a exibição de um ponto no gráfico. Os gráficos de barras são ignorados mostrando a barra. Em gráficos de linhas, NULL pode aparecer como [linhas pontilhadas ou tracejadas](metrics-troubleshoot.md#chart-shows-dashed-line) como as mostradas na captura de tela na seção anterior. Ao calcular as médias que incluem nulos, há menos pontos de dados dos quais obter a média.  Esse comportamento pode, às vezes, resultar em valores de queda inesperados em um gráfico, embora geralmente menos que se o valor fosse convertido em um zero e usado como um DataPoint válido.  

As [métricas personalizadas](metrics-custom-overview.md) sempre usam valores nulos quando nenhum dado é recebido. Com as [métricas de plataforma](data-platform.md), cada provedor de recursos decide se deve usar zeros ou nulos com base no que faz mais sentido para uma determinada métrica.

Azure Monitor alertas usam os valores que o provedor de recursos grava no banco de dados de métrica, portanto, é importante saber como o provedor de recursos lida com nulos exibindo os dados primeiro.

## <a name="how-aggregation-works"></a>Como funciona a agregação

Os gráficos de métricas no sistema anterior mostram tipos diferentes de dados agregados. O sistema agrega os dados previamente para que os gráficos solicitados possam ser exibidos mais rapidamente sem muita computação repetida.  

Neste exemplo:

- Estamos coletando uma métrica transacional **fictícia** chamada **falhas de http** 
- *Server* é uma dimensão para a métrica de **falhas de http** .
- Temos 3 servidores-servidor A, B e C.

Para simplificar a explicação, começaremos apenas com o tipo de agregação SUM. 

### <a name="sub-minute-to-1-minute-aggregation"></a>Subminuto para agregação de 1 minuto

Os primeiros dados de métrica brutos são coletados e armazenados no banco Azure Monitor dados de métricas. Nesse caso, cada servidor tem registros de transação armazenados com um carimbo de data/hora porque o *servidor* é uma dimensão. Considerando que o menor período de tempo que você pode exibir como um cliente é de 1 minuto, esses carimbos de data/hora são agregados primeiro em valores de métrica de 1 minuto para cada servidor individual.  O processo de agregação para o servidor B é mostrado no gráfico abaixo. Os servidores A e C são realizados da mesma maneira e têm dados diferentes.  

:::image type="content" source="media/metrics-aggregation-explained/sub-minute-transaction.png" alt-text="Captura de tela mostrando entradas transacionais de subminuto em agregações de 1 minuto. " border="false":::

Os valores agregados de 1 minuto resultantes são armazenados como novas entradas no banco de dados de métricas para que possam ser coletados para cálculos posteriores. 

:::image type="content" source="media/metrics-aggregation-explained/sub-minute-transaction-dimension-aggregated.png" alt-text="Captura de tela mostrando várias entradas agregadas de 1 minuto na dimensão do servidor. Servidor A, B e C mostrados individualmente" border="false":::

### <a name="dimension-aggregation"></a>Agregação de dimensão

Os cálculos de 1 minuto são então recolhidos pela dimensão e novamente armazenados como registros individuais.   Nesse caso, todos os dados de todos os servidores individuais são agregados em uma métrica de intervalo de 1 minuto e armazenados no banco de dados de métricas para uso em agregações posteriores.

:::image type="content" source="media/metrics-aggregation-explained/1-minute-transaction-dimension-flattened-aggregated.png" alt-text="Captura de tela mostrando várias entradas agregadas de 1 minuto do servidor A, B e C agregadas em um minuto todos os servidores são inteiros" border="false":::

Para maior clareza, a tabela a seguir mostra o método de agregação.

| Período   | Servidor A | Servidor B | o Servidor C | Sum (A + B + C)|
| -------- | -------- | -------- | -------- | --------   |  
| Minuto 1 | 1        | 1        | 1        | 3          |
| Minuto 2 | 0        | 5        | 1        | 6          |
| Minuto 3 | 0        | 5        | 1        | 6          |
| Minuto 4 | 2        | 3        | 4        | 9          |
| Minuto 5 | 1        | 0        | 3        | 4          |
| Minuto 6 | 1        | 0        | 4        | 5          |
| Minuto 7 | 1        | 2        | 4        | 7          |
| Minuto 8 | 0        | 1        | 0        | 1          |
| Minuto 9 | 1        | 1        | 4        | 6          |
| Minuto 10| 2        | 1        | 0        | 3          |

Somente uma dimensão é mostrada acima, mas esse mesmo processo de agregação e armazenamento ocorre para **todas as dimensões** às quais uma métrica dá suporte.

- Colete valores em um conjunto agregado de 1 minuto por essa dimensão. Armazene esses valores. 
- Recolha a dimensão em uma soma agregada de 1 minuto. Armazene esses valores. 

Vamos introduzir outra dimensão de falhas de HTTP chamada adaptador. Digamos que tenhamos um número variável de adaptadores por servidor.

- O servidor A tem 1 adaptador
- O servidor B tem 2 adaptadores
- O servidor C tem 3 adaptadores

Coletamos dados para as transações a seguir separadamente. Eles serão marcados com:

- Uma hora
- Um valor
- O servidor de origem da transação
- O adaptador de origem da transação

Cada um desses fluxos de subminutos seria agregado em valores de série temporal de 1 minuto e armazenados no banco de dados de métrica Azure Monitor:

- Servidor A, adaptador 1
- Servidor B, adaptador 1
- Servidor B, adaptador 2
- Servidor C, adaptador 1
- Servidor C, adaptador 2
- Servidor C, adaptador 3

Além disso, as seguintes agregações recolhidas também seriam armazenadas:

- Servidor A, adaptador 1 (porque não há nada a ser recolhido, ele seria armazenado novamente)
- Servidor B, adaptador 1 + 2
- Servidor C, adaptador 1 + 2 + 3
- Todos os servidores, todos os adaptadores

Isso mostra que as métricas com grandes números de dimensões têm um número maior de agregações. Não é importante conhecer todas as permutas, apenas entender o raciocínio. O sistema deseja ter os dados individuais e os dados agregados armazenados para recuperação rápida para acesso em qualquer gráfico. O sistema escolhe a agregação armazenada mais relevante ou os dados brutos subjacentes, dependendo do que você escolher para exibir. 

### <a name="aggregation-with-no-dimensions"></a>Agregação sem dimensões

Como essa métrica tem um *servidor* de dimensão, você pode obter os dados subjacentes para o servidor a, B e C acima por meio de divisão e filtragem, conforme explicado anteriormente neste artigo. Se a métrica não tivesse o *servidor* como uma dimensão, você como um cliente poderia acessar apenas as somas de 1 minuto agregadas mostradas em preto no diagrama. Ou seja, os valores de 3, 6, 6, 9, etc. O sistema também não faria o trabalho subjacente para agregar valores de divisão que nunca os usaria no Gerenciador de métricas nem os enviaria por meio da API REST de métricas. 

## <a name="viewing-time-granularities-above-1-minute"></a>Exibindo granularidades de tempo acima de 1 minuto

Se você solicitar métricas em uma granularidade maior, o sistema usará as somas agregadas de 1 minuto para calcular as somas das granularidades de tempo maiores.  Abaixo, as linhas pontilhadas mostram o método somatório para as granularidades de tempo de 2 e 5 minutos. Novamente, estamos mostrando apenas o tipo de agregação SUM para simplificar.

:::image type="content" source="media/metrics-aggregation-explained/1-minute-to-2-min-5-min.png" alt-text="Captura de tela mostrando várias entradas agregadas de 1 minuto na dimensão de servidor agregada em períodos de tempo 2 e 5 min." border="false":::

Para a granularidade de tempo de 2 minutos.

| Período       | Totaliza       
| -------------|-------------|  
| Minuto 1 & 2 | (3 + 6) = 9 |
| Minuto 3 & 4 | (6 + 9) = 15|
| Minuto 4 & 5 | (4 + 5) = 9 |
| Minuto 6 & 7 | (7 + 1) = 8 |
| Minuto 8 & 9 | (6 + 3) = 9 |

Para uma granularidade de tempo de 5 minutos.

| Período              |            Totaliza        |
|---------------------|------------------------|  
| Minuto 1 a 5  | 3 + 6 + 6 + 9 + 4 = 28 |
| Minuto 6 a 10 | 5 + 7 + 1 + 6 + 3 = 22 |

O sistema usa os dados agregados armazenados que proporcionam o melhor desempenho. 

Abaixo está o diagrama maior para o processo de agregação de 1 minuto acima, com algumas das setas deixadas para melhorar a legibilidade.

:::image type="content" source="media/metrics-aggregation-explained/sum-aggregation-full.png" alt-text="Captura de tela mostrando a consolidação das três capturas de tela anteriores. Várias entradas agregadas de 1 minuto na dimensão do servidor agregadas em intervalos de 1 minuto, 2 minutos e 5 minutos. Servidor A, B e C mostrados individualmente" border="false":::

## <a name="more-complex-example"></a>exemplo mais complexo

Veja a seguir um exemplo maior usando valores para uma métrica fictícia chamada tempo de resposta HTTP em milissegundos.  Aqui, apresentamos níveis adicionais de complexidade.

1. Mostramos a agregação para Sum, Count, min e Max e o cálculo para Average.
2. Mostramos valores nulos e como eles afetam os cálculos. 

Considere o exemplo a seguir. As caixas e setas mostram exemplos de como os valores são agregados e calculados. 

O mesmo processo de pré-agregação de 1 minuto, conforme descrito na seção anterior, ocorre para somas, contagem, mínimo e máximo.  No entanto, a média não é previamente agregada. Ele é recalculado usando dados agregados para evitar erros de cálculo.
 
:::image type="content" source="media/metrics-aggregation-explained/full-aggregation-example-all-types.png" alt-text="Captura de tela mostrando um exemplo complexo de agregação e cálculo de Sum, Count, min, Max e Average de 1 minuto a 10 minutos." border="false" lightbox="media/metrics-aggregation-explained/full-aggregation-example-all-types.png":::

Considere o minuto 6 para a agregação de 1 minuto, conforme destacado acima. Este minuto é o ponto em que o servidor B ficou offline e interrompeu os dados de relatório, talvez devido a uma reinicialização. 

A partir do minuto 6 acima, os tipos de agregação calculados de 1 minuto são: 

| Tipo de agregação | Valor        | Observações |
|------------------|--------------|-------|
| Somar              | 53 + 20 = 73 | |
| Contagem            | 2            | Mostra o efeito de nulos.  O valor teria sido 3 se o servidor estivesse online.  |
| Mínimo          | 20           | |
| Máximo          | 53           | |
| Média          | 73/2       | Sempre a soma dividida pela contagem. Ele nunca é armazenado e sempre recalculado para cada granularidade de tempo usando os números agregados para essa granularidade. Observe o recálculo para as granularidades de tempo de 5 e 10 minutos, conforme destacado acima. |

A cor de texto vermelho indica os valores que podem ser considerados fora do intervalo normal e mostra como eles se propagam (ou falham) à medida que a granularidade de tempo aumenta. Observe como o *mínimo* e o *máximo* indicam que há anomalias subjacentes enquanto a *média* e as *somas* perdem essas informações à medida que a granularidade do tempo aumenta.  

Você também pode ver que os nulos oferecem um melhor cálculo de média, se zeros foram usados.  

> [!NOTE] 
> Embora não seja o caso neste exemplo, *Count* é igual a *sum* em casos em que uma métrica sempre é capturada com o valor de 1. Isso é comum quando uma métrica acompanha a ocorrência de um evento transacional – por exemplo, o número de falhas de HTTP mencionadas em um exemplo anterior neste artigo.

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Metrics Explorer](metrics-getting-started.md)
- [Gerenciador de métricas avançadas](metrics-charts.md)
