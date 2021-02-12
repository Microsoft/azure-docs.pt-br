---
title: Perguntas frequentes do assistente de métricas
titleSuffix: Azure Cognitive Services
description: Perguntas frequentes sobre o serviço do Orientador de métricas.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: mbullwin
ms.openlocfilehash: 0c4c296cb1454ed89eef102732533589b1c8ca0d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420947"
---
# <a name="metrics-advisor-frequently-asked-questions"></a>Perguntas frequentes do assistente de métricas

### <a name="what-is-the-cost-of-my-instance"></a>Qual é o custo da minha instância?

Atualmente, não há um custo para usar sua instância durante a visualização.

### <a name="why-cant-i-create-the-resource-the-pricing-tier-is-unavailable-and-it-says-you-have-already-created-1-s0-for-this-subscription"></a>Por que não posso criar o recurso? O "tipo de preço" não está disponível e diz "você já criou 1 S0 para esta assinatura"?

:::image type="content" source="media/pricing.png" alt-text="Mensagem quando um recurso F0 já existe":::

Durante a visualização pública, somente uma instância do Orientador de métricas pode ser criada por região em uma assinatura.

Se você já tiver uma instância criada na mesma região usando a mesma assinatura, poderá tentar uma região diferente ou uma assinatura diferente para criar uma nova instância. Você também pode excluir uma instância existente para criar uma nova.

Se você já tiver excluído a instância existente, mas ainda vir o erro, aguarde cerca de 20 minutos após a exclusão do recurso antes de criar uma nova instância.

## <a name="basic-concepts"></a>Conceitos básicos

### <a name="what-is-multi-dimensional-time-series-data"></a>O que são dados multidimensionais de série temporal?

Consulte a definição de [métrica multidimensional](glossary.md#multi-dimensional-metric)  no Glossário.

### <a name="how-much-data-is-needed-for-metrics-advisor-to-start-anomaly-detection"></a>Quantos dados são necessários para o revisor de métricas iniciar a detecção de anomalias?

No mínimo, um ponto de dados pode disparar a detecção de anomalias. No entanto, isso não traz a melhor precisão. O serviço assumirá uma janela de pontos de dados anteriores usando o valor especificado como a regra de "lacuna de preenchimento" durante a criação do feed de dados.

É recomendável ter alguns dados antes do carimbo de data/hora em que você deseja a detecção.
Com base na granularidade de seus dados, a quantidade de dados recomendada varia conforme a seguir.

| Granularidade | Valor de dados recomendado para detecção |
| ----------- | ------------------------------------- |
| Menos de 5 minutos | 4 dias de dados |
| 5 minutos a 1 dia | 28 dias de dados |
| Mais de 1 dia, para 31 dias | 4 anos de dados |
| Mais de 31 dias | 48 anos de dados |

### <a name="why-metrics-advisor-doesnt-detect-anomalies-from-historical-data"></a>Por que o Metrics Advisor não detecta anomalias de dados históricos?

O assistente de métricas foi projetado para detectar dados de transmissão ao vivo. Há uma limitação do comprimento máximo dos dados históricos que o serviço examinará e executará a detecção de anomalias. Isso significa que somente os pontos de dados após um determinado carimbo de data/hora terão resultados de detecção de anomalias. Esse carimbo de data/hora mais antigo depende da granularidade dos seus dados.

Com base na granularidade dos seus dados, os comprimentos dos dados históricos que terão resultados de detecção de anomalias são os seguintes.

| Granularidade | Comprimento máximo de dados históricos para detecção de anomalias |
| ----------- | ------------------------------------- |
| Menos de 5 minutos | Tempo integrado-13 horas |
| 5 minutos a menos de 1 hora | Tempo integrado-4 dias  |
| 1 hora para menos de 1 dia | Tempo integrado-14 dias  |
| 1 dia | Tempo integrado-28 dias  |
| Mais de 1 dia, menos de 31 dias | Tempo integrado-2 anos  |
| Mais de 31 dias | Tempo integrado-24 anos   |

### <a name="more-concepts-and-technical-terms"></a>Mais conceitos e termos técnicos

Consulte também o [Glossário](glossary.md) para obter mais informações.

###  <a name="how-do-i-write-a-valid-query-for-ingesting-my-data"></a>Como fazer gravar uma consulta válida para ingerir meus dados?  

Para o assistente de métricas ingerir seus dados, você precisará criar uma consulta que retorne as dimensões de seus dados em um único carimbo de data/hora. O Orientador de métricas executará essa consulta várias vezes para obter os dados de cada carimbo de data/hora. 

Observe que a consulta deve retornar no máximo um registro para cada combinação de dimensão, em um determinado carimbo de data/hora. Todos os registros retornados devem ter o mesmo carimbo de data/hora. Não deve haver nenhum registro duplicado retornado pela consulta.

Por exemplo, suponha que você crie a consulta abaixo, para uma métrica diária: 
 
`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(DAY, 1, @StartTime)`

Certifique-se de usar a granularidade correta para sua série temporal. Para uma métrica por hora, você usaria: 

`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(hour, 1, @StartTime)`

Observe que essas consultas retornam apenas dados em um único carimbo de data/hora e contêm todas as combinações de dimensões a serem ingeridas pelo Orientador de métricas. 

:::image type="content" source="media/query-result.png" alt-text="Um resultado de consulta com um carimbo de data/hora" lightbox="media/query-result.png":::


### <a name="how-do-i-detect-spikes--dips-as-anomalies"></a>Como fazer detectar picos & DIPs como anomalias?

Se você tiver limites rígidos predefinidos, poderá, na verdade, definir manualmente "limite rígido" nas [configurações de detecção de anomalias](how-tos/configure-metrics.md#anomaly-detection-methods).
Se não houver limites, você poderá usar a "detecção inteligente", que é alimentada pelo ia. Veja [ajustar a configuração de detecção](how-tos/configure-metrics.md#tune-the-detecting-configuration) para obter detalhes.

### <a name="how-do-i-detect-inconformity-with-regular-seasonal-patterns-as-anomalies"></a>Como fazer detectar a inconformidade com padrões regulares (sazonais) como anomalias?

"Detecção inteligente" é capaz de aprender o padrão de seus dados, incluindo padrões sazonais. Em seguida, ele detecta os pontos de dados que não estão em conformidade com os padrões regulares como anomalias. Veja [ajustar a configuração de detecção](how-tos/configure-metrics.md#tune-the-detecting-configuration) para obter detalhes.

### <a name="how-do-i-detect-flat-lines-as-anomalies"></a>Como fazer detectar linhas simples como anomalias?

Se seus dados normalmente estiverem muito instáveis e flutuar muito e você quiser ser alertado quando ficar muito estável ou se se tornar uma linha simples, o "limite de alteração" poderá ser configurado para detectar esses pontos de dados quando a alteração for muito pequena.
Veja [as configurações de detecção de anomalias](how-tos/configure-metrics.md#anomaly-detection-methods) para obter detalhes.

### <a name="how-to-set-up-email-settings-and-enable-alerting-by-email"></a>Como definir configurações de email e habilitar alertas por email?

1.  Um usuário com privilégios de administrador de assinatura ou grupo de recursos precisa navegar até o recurso do supervisor de métricas criado no portal do Azure e selecionar a guia **controle de acesso (iam)** . 
2.  Selecione **Adicionar atribuições de função**
3.  Escolha uma função de **administrador do Orientador de métricas de serviços cognitivas** , selecione sua conta como na imagem abaixo.
4.  Clique no botão **salvar** e você foi adicionado com êxito como administrador do recurso do assistente de métricas. Observe que todas as ações acima precisam ser executadas pelo administrador da assinatura ou pelo administrador do grupo de recursos. 

:::image type="content" source="media/access-control.png" alt-text="Página de menu de controle de acesso (IAM) com adicionar uma atribuição de função selecionada, seguida por uma caixa com atribuir acesso ao usuário selecionado exibido com uma função de acesso de administrador de métricas de serviços cognitivas, seguido pelo botão salvar da interface do usuário que está sendo selecionada para ilustrar as etapas de pesquisa de um usuário e adição de um nível específico de permissões de acesso." lightbox="media/access-control.png":::


5.  Pode levar até um minuto para que as permissões se propaguem. Em seguida, selecione o espaço de trabalho do supervisor de métricas e selecione a opção de **configuração email** no painel de navegação esquerdo. Preencha os itens necessários, especialmente as informações relacionadas ao SMTP. 
6.  Selecione **salvar** e, em seguida, você estará pronto para a configuração de email. Você pode criar novos ganchos e assinar anomalias de métricas para alertas quase em tempo real. 

## <a name="advanced-concepts"></a>Conceitos avançados

### <a name="how-does-metric-advisor-build-an-incident-tree-for-multi-dimensional-metrics"></a>Como o Orientador de métrica cria uma árvore de incidentes para métricas multidimensionais?

Uma métrica pode ser dividida em várias séries temporais por dimensões. Por exemplo, a métrica `Response latency` é monitorada para todos os serviços pertencentes à equipe. A `Service` categoria pode ser usada como uma dimensão para enriquecer a métrica, portanto, podemos `Response latency` dividir por `Service1` , `Service2` e assim por diante. Cada serviço pode ser implantado em computadores diferentes em vários data centers, portanto, a métrica pode ser dividida por `Machine` e `Data center` .

|Serviço| Data center| Computador  | 
|----|------|----------------   |
| S1 |  DC1 |   M1 |
| S1 |  DC1 |   M2 |
| S1 |  DC2 |   M3 |
| S1 |  DC2 |   M4 |
| S2 |  DC1 |   M1 |
| S2 |  DC1 |   M2 |
| S2 |  DC2 |   M5 |
| S2 |  DC2 |   M6 |
| ...|      |      |

A partir do total `Response latency` , podemos fazer uma busca detalhada na métrica por `Service` `Data center` e `Machine` . No entanto, talvez faça mais sentido para os proprietários de serviço usarem o caminho `Service`  ->  `Data center`  ->  `Machine` ou talvez faça mais sentido para os engenheiros de infraestrutura usarem o caminho `Data Center`  ->  `Machine`  ->  `Service` . Tudo depende dos requisitos de negócios individuais de seus usuários. 

No Orientador de métrica, os usuários podem especificar qualquer caminho que desejem fazer uma busca detalhada ou ROLLUP de um nó da topologia hierárquica. Mais precisamente, a topologia hierárquica é um grafo acíclico direcionado em vez de uma estrutura de árvore. Há uma topologia hierárquica completa que consiste em todas as combinações de dimensão em potencial, como esta: 

:::image type="content" source="media/dimension-combinations-view.png" alt-text="diagrama de topologia hierárquica que consiste em vários vértices e bordas de interconexão com várias dimensões rotuladas S, DC e M com números correspondentes, variando de 1 a 6" lightbox="media/dimension-combinations-view.png":::

Em teoria, se a dimensão `Service` tiver `Ls` valores distintos, a dimensão `Data center` tiver `Ldc` valores distintos e a dimensão `Machine` tiver `Lm` valores distintos, poderá haver `(Ls + 1) * (Ldc + 1) * (Lm + 1)` combinações de dimensões na topologia hierárquica. 

Mas geralmente nem todas as combinações de dimensão são válidas, o que pode reduzir significativamente a complexidade. Atualmente, se os usuários agregarem a própria métrica, não limitaremos o número de dimensões. Se você precisar usar a funcionalidade de acúmulo fornecida pelo assistente de métricas, o número de dimensões não deverá ser maior que 6. No entanto, limitamos o número de séries temporais expandidas pelas dimensões de uma métrica para menos de 10.000.

A ferramenta de **árvore de incidentes** na página de diagnóstico mostra apenas nós em que uma anomalia foi detectada, em vez de toda a topologia. Isso é para ajudá-lo a se concentrar no problema atual. Ele também pode não mostrar todas as anomalias dentro da métrica e, em vez disso, exibirá as principais anomalias com base na contribuição. Dessa forma, podemos descobrir rapidamente o impacto, o escopo e o caminho de propagação dos dados anormais. Que reduz significativamente o número de anomalias que precisamos focar e ajuda os usuários a entender e localizar seus principais problemas. 
 
Por exemplo, quando ocorre uma anomalia `Service = S2 | Data Center = DC2 | Machine = M5` , o desvio da anomalia afeta o nó pai `Service= S2` que também detectou a anomalia, mas a anomalia não afeta toda a data center em `DC2` todos os serviços `M5` . A árvore de incidentes seria criada como na captura de tela abaixo, a principal anomalia é capturada em `Service = S2` , e a causa raiz pode ser analisada em dois caminhos que resultam `Service = S2 | Data Center = DC2 | Machine = M5` .

 :::image type="content" source="media/root-cause-paths.png" alt-text="5 os vértices rotulados com dois caminhos distintos conectados por bordas com um nó comum rotulado S2. A principal anomalia é capturada em Service = S2, e a causa raiz pode ser analisada pelos dois caminhos que levam ao serviço = S2 | Data Center = DC2 | Machine = M5" lightbox="media/root-cause-paths.png":::

## <a name="next-steps"></a>Próximas etapas
- [Visão geral do Assistente de Métricas](overview.md)
- [Usar o portal da Web](quickstarts/web-portal.md)