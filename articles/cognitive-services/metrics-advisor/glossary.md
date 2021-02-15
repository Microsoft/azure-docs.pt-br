---
title: Glossário do revisor de métricas
titleSuffix: Azure Cognitive Services
description: Principais ideias e conceitos para o serviço do assistente de métricas
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: 6c461983053a145dfda58b9e3d26b39db0c339e5
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92893413"
---
# <a name="metrics-advisor-glossary-of-common-vocabulary-and-concepts"></a>Glossário do revisor de métricas de vocabulário e conceitos comuns

Este documento explica os termos técnicos usados no assistente de métricas. Use este artigo para saber mais sobre os conceitos e objetos comuns que você pode encontrar ao usar o serviço.

## <a name="data-feed"></a>Feed de dados

> [!NOTE]
> Várias métricas podem compartilhar a mesma fonte de dados e até mesmo o mesmo feed de dados.

Um feed de dados é o que o Orientador de métricas ingeri em sua fonte de dados, como Cosmos DB ou um SQL Server. Um feed de dados contém linhas de:
* carimbos de data/hora
* zero ou mais dimensões
* uma ou mais medidas. 

## <a name="metric"></a>Métrica

Uma métrica é uma medida quantificável usada para monitorar e avaliar o status de um processo comercial específico. Pode ser uma combinação de vários valores de série temporal divididos em dimensões. Por exemplo, uma métrica de *integridade da Web* pode conter dimensões para *contagem de usuários* e o *mercado en-US* .

## <a name="dimension"></a>Dimensão

Uma dimensão é um ou mais valores categóricos. A combinação desses valores identifica uma série de tempo monovariável específica, por exemplo: país, idioma, locatário e assim por diante.

## <a name="multi-dimensional-metric"></a>Métrica multidimensional

O que é uma métrica de várias dimensões? Vamos usar dois exemplos. 

**Receita de um negócio**

Suponha que você tenha dados para a receita de sua empresa. Os dados de série temporal podem ter uma aparência semelhante a esta:

| Timestamp | Categoria | Market | Receita |
| ----------|----------|--------|----- |
| 2020-6-1 | Alimentos | EUA | 1000 |
| 2020-6-1 | Vestuário | EUA | 2000 |
| 2020-6-2 | Alimentos | Reino Unido | 800 | 
| ...      | ...  |... | ... |

Neste exemplo, a *categoria* e o *mercado* são dimensões. *Receita* é o KPI (indicador chave de desempenho) que pode ser dividido em diferentes categorias e/ou mercados e também pode ser agregado. Por exemplo, a receita de *alimentos* para todos os mercados.
 
**Contagens de erros para um aplicativo complexo**

Suponha que você tenha dados para o número de erros registrados em um aplicativo. Os dados de série temporal podem ter uma aparência semelhante a esta:

| Timestamp | Componente de aplicativo | Região | Contagem de erros |
| ----------|----------|--------|----- |
| 2020-6-1 | Banco de dados de funcionários | UE OCIDENTAL | 9000 |
| 2020-6-1 | Fila de mensagens | LESTE DOS EUA | 1000 |
| 2020-6-2 | Fila de mensagens | LESTE DOS EUA | 8000| 
| ...      | ...           | ...     |  ...|

Neste exemplo, *componente de aplicativo* e *região* são dimensões. *Contagem de erros* é o KPI que pode ser dividido em diferentes categorias e/ou mercados e também pode ser agregado. Por exemplo, a contagem de erros da *fila de mensagens* em todas as regiões.

## <a name="measure"></a>Medida

Uma medida é um termo fundamental ou específico da unidade e um valor quantificável da métrica.

## <a name="time-series"></a>Série temporal

Uma série temporal é uma série de pontos de dados indexados (ou listados ou incluídos em gráficos) em ordem cronológica. Normalmente, uma série temporal é uma sequência feita em pontos sucessivos e igualmente espaçados. É uma sequência de dados em tempo discreto.

No Orientador de métricas, os valores de uma métrica em uma combinação de dimensão específica são chamados de uma série.

## <a name="granularity"></a>Granularidade

A granularidade indica a frequência com que os pontos de dados serão gerados na fonte de dados. Por exemplo, diariamente, por hora.

## <a name="start-time"></a>Hora de início

A hora de início é a hora em que você deseja que o assistente de métricas comece a ingerir dados de sua fonte de dados. Sua fonte de dados deve ter dados na hora de início especificada.

## <a name="confidence-boundaries"></a>Limites de confiança

> [!NOTE]
> Os limites de confiança não são a única medida usada para localizar anomalias. É possível que os pontos de dados fora desse limite sejam sinalizados como normais pelo modelo de detecção. 

No Orientador de métricas, os limites de confiança representam a sensibilidade do algoritmo usado e são usados para filtrar anomalias excessivamente confidenciais. No portal da Web, os limites de confiança aparecem como uma banda azul transparente. Todos os pontos dentro da banda são tratados como pontos normais.

O Metrics Advisor fornece ferramentas para ajustar a sensibilidade dos algoritmos usados. Consulte [como configurar métricas e ajuste fino da detecção de configuração](how-tos/configure-metrics.md) para obter mais informações.

![Limites de confiança](media/confidence-bounds.png)


## <a name="hook"></a>Fixação

O revisor de métricas permite criar e assinar alertas em tempo real. Esses alertas são enviados pela Internet, [usando um gancho](how-tos/alerts.md).

## <a name="anomaly-incident"></a>Incidente de anomalias

Depois que uma configuração de detecção é aplicada às métricas, os incidentes são gerados sempre que qualquer série dentro dela tem uma anomalia. Em conjuntos de dados grandes, isso pode ser impressionante, portanto, o Metrics Advisor agrupa a série de anomalias em uma métrica em um incidente. O serviço também avaliará a gravidade e fornecerá ferramentas para [diagnosticar o incidente](how-tos/diagnose-incident.md).

### <a name="incident-tree"></a>Árvore de incidentes

No assistente de métricas, você pode aplicar a detecção de anomalias nas métricas, e o assistente de métricas monitora automaticamente todas as séries de tempo de todas as combinações de dimensão. Sempre que houver qualquer anomalia detectada, o Orientador de métrica agregará anomalias em incidentes.
Após a ocorrência de um incidente, o assistente de métricas fornecerá uma árvore de incidentes com uma hierarquia de anomalias de contribuição e a identificará com o maior impacto. Cada incidente tem uma anomalia de causa raiz, que é o nó superior da árvore.

### <a name="anomaly-grouping"></a>Agrupamento de anomalias

O assistente de métricas fornece a capacidade de encontrar uma série temporal relacionada com padrões semelhantes. Ele também pode fornecer informações mais aprofundadas sobre o impacto sobre outras dimensões e correlacionar as anomalias.

### <a name="time-series-comparison"></a>Comparação de série temporal

Você pode escolher várias séries temporais para comparar tendências em uma única visualização. Isso fornece uma maneira clara e criteriosa de exibir e comparar séries relacionadas.

## <a name="detection-configuration"></a>Configuração de detecção

>[!Note]
>As configurações de detecção são aplicadas somente dentro de uma métrica individual.

No portal da Web do Metrics Advisor, uma configuração de detecção (como sensibilidade, adiamento automático e direção) é listada no painel esquerdo ao exibir uma métrica. Os parâmetros podem ser ajustados e aplicados a todas as séries dentro dessa métrica.

Uma configuração de detecção é necessária para cada série temporal e determina se um ponto na série temporal é uma anomalia. O Orientador de métricas definirá uma configuração padrão para toda a métrica quando você carregar os dados pela primeira vez. 

Além disso, é possível refinar a configuração aplicando parâmetros de ajuste em um grupo de séries ou em um específico. Somente uma configuração será aplicada a uma série temporal:
* As configurações aplicadas a uma série específica substituirão as configurações de um grupo
* As configurações de um grupo substituirão as configurações aplicadas à métrica inteira.

O revisor de métricas fornece vários [métodos de detecção](how-tos/configure-metrics.md#anomaly-detection-methods)e você pode combiná-los usando operadores lógicos.

### <a name="smart-detection"></a>Detecção inteligente

Detecção de anomalias usando vários algoritmos de aprendizado de máquina.

**Sensibilidade** : um valor numérico para ajustar a tolerância da detecção de anomalias. Visualmente, quanto maior o valor, mais estreito os limites superiores e inferiores em relação à série temporal.

### <a name="hard-threshold"></a>Limite rígido

Os valores fora dos limites superior ou inferior são anomalias.

**Min** : o limite inferior

**Max** : o limite superior

### <a name="change-threshold"></a>Alterar limite

Use o valor de ponto anterior para determinar se esse ponto é uma anomalia.

**Alterar porcentagem** : em comparação com o ponto anterior, o ponto atual será uma anomalia se a porcentagem de alteração for maior que esse parâmetro.

**Mudar em pontos** : Quantos pontos deve ser examinado novamente.

### <a name="common-parameters"></a>Parâmetros comuns

**Direção** : um ponto é uma anomalia somente quando o desvio ocorre na direção *para cima* , *para baixo* ou para *ambos* .

**Anomalias inválidas até** : um ponto de dados é apenas uma anomalia se um percentual especificado de pontos anteriores também for anomalias.

## <a name="alert-settings"></a>Configurações de alerta

As configurações de alerta determinam quais anomalias devem disparar um alerta. Você pode definir vários alertas com configurações diferentes. Por exemplo, você pode criar um alerta para anomalias com menor impacto nos negócios e outro para alertas mais importantes.

Você também pode criar um alerta entre as métricas. Por exemplo, um alerta que só será disparado se duas métricas especificadas tiverem anomalias.

### <a name="alert-scope"></a>Escopo do alerta

O escopo do alerta refere-se ao escopo ao qual o alerta se aplica. Há quatro opções:

**Anomalias de todas as séries: os** alertas serão disparados para anomalias em todas as séries dentro da métrica.

**Anomalias no grupo de séries** : os alertas só serão disparados para anomalias em dimensões específicas do grupo de séries. O número de dimensões especificadas deve ser menor do que o número total de dimensões.

**Anomalias na série favorita** : alertas serão disparados somente para anomalias que são adicionadas como favoritos. Você pode escolher um grupo de séries como um favorito para cada configuração de detecção.

**Anomalias no N superior de todas as séries: os** alertas só serão disparados para anomalias na série N superior. Você pode definir parâmetros para especificar o número de carimbos de data/hora a serem levados em conta, e quantas anomalias devem estar neles para enviar o alerta.

### <a name="severity"></a>Severity

A gravidade é uma classificação que o assistente de métricas usa para descrever a severidade do incidente, incluindo *alta* , *média* e *baixa* .

Atualmente, o Orientador de métricas usa os seguintes fatores para medir a severidade do alerta:
1. A proporção de valor e a proporção de quantidade de anomalias na métrica.
1. Confiança de anomalias.
1. Suas configurações favoritas também contribuem para a gravidade.

### <a name="auto-snooze"></a>Adiar automaticamente

Algumas anomalias são problemas transitórios, especialmente para métricas de granularidade pequena. Você pode *adiar* um alerta para um número específico de pontos de tempo. Se forem encontradas anomalias dentro desse número especificado de pontos, nenhum alerta será disparado. O comportamento do adiamento automático pode ser definido no nível de métrica ou nível de série.

O comportamento de adiar pode ser definido no nível de métrica ou nível de série.

## <a name="data-feed-settings"></a>Configurações de feed de dados

### <a name="ingestion-time-offset"></a>Deslocamento do tempo de ingestão

Por padrão, os dados são ingeridos de acordo com a granularidade (como *diariamente* ). Usando um inteiro positivo, você pode atrasar a ingestão dos dados pelo valor especificado. Usando um número negativo, você pode avançar a ingestão pelo valor especificado.

### <a name="max-ingestion-per-minute"></a>Ingestão máxima por minuto

Defina esse parâmetro se sua fonte de dados der suporte à simultaneidade limitada. Caso contrário, deixe as configurações padrão.

### <a name="stop-retry-after"></a>Parar repetição após

Se a ingestão de dados falhar, o Orientador de métricas tentará automaticamente após um período de tempo. O início do período é a hora em que a primeira ingestão de dados ocorreu. O comprimento do período de repetição é definido de acordo com a granularidade. Se você usar o valor padrão ( `-1` ), o período de repetição será determinado de acordo com a granularidade:

| Granularidade       | Parar repetição após           |
| :------------ | :--------------- |
| Diário, personalizado (>= 1 dia), semanal, mensal, anual     | 7 dias          |
| Por hora, personalizado (< 1 dia)       | 72 horas |

### <a name="min-retry-interval"></a>Intervalo de repetição mín.

Você pode especificar o intervalo mínimo ao tentar efetuar pull de dados da origem. Se você usar o valor padrão ( `-1` ), o intervalo de repetição será determinado de acordo com a granularidade:

| Granularidade       | Intervalo de repetição mínimo           |
| :------------ | :--------------- |
| Diário, personalizado (>= 1 dia), semanalmente, mensalmente     | 30 minutos          |
| Por hora, personalizado (< 1 dia)      | 10 minutos |
| Anualmente | 1 dia          |

### <a name="grace-period"></a>Período de carência

> [!Note]
> O período de carência começa no tempo de ingestão regular, além do deslocamento de tempo de ingestão especificado.
    
Um período de carência é um período de tempo em que o assistente de métricas continuará a buscar dados da fonte de dados, mas não acionará nenhum alerta. Se nenhum dado tiver sido ingerido após o período de carência, um alerta de *feed de dados não disponível* será disparado.

### <a name="snooze-alerts-in"></a>Adiar alertas em

Quando essa opção for definida como zero, cada carimbo de data/hora com *não disponível* disparará um alerta. Quando definido com um valor diferente de zero, o número especificado de alertas *não disponíveis* será adiado se nenhum dado for obtido.

## <a name="data-feed-permissions"></a>Permissões de feed de dados

Há duas funções para gerenciar permissões de feed de dados: *administrador* e *Visualizador* . 

* Um *administrador* tem controle total sobre o feed de dados e as métricas dentro dele. Eles podem ativar, pausar, excluir o feed de dados e fazer atualizações em feeds e configurações. Normalmente, um *administrador* é o proprietário das métricas.

* Um *Visualizador* é capaz de exibir o feed de dados ou as métricas, mas não é capaz de fazer alterações. 

## <a name="next-steps"></a>Próximas etapas
- [Visão geral do Assistente de Métricas](overview.md)
- [Usar o portal da Web](quickstarts/web-portal.md)