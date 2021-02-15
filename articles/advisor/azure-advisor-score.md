---
title: Otimizar cargas de trabalho do Azure usando a Pontuação do supervisor
description: Use a Pontuação do supervisor do Azure para obter o máximo do Azure.
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: 11b20bc3b4d604d3a7ff4608cd1c21f129c1cb6d
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630115"
---
# <a name="optimize-azure-workloads-by-using-advisor-score"></a>Otimizar cargas de trabalho do Azure usando a Pontuação do supervisor

## <a name="introduction-to-advisor-score"></a>Introdução à Pontuação do supervisor

O Azure Advisor fornece recomendações de práticas recomendadas para suas cargas de trabalho. Essas recomendações são personalizadas e acionáveis para ajudá-lo a:

* Melhore a postura de suas cargas de trabalho e Otimize suas implantações do Azure.
* Impeça proativamente os principais problemas seguindo as práticas recomendadas.
* Avalie suas cargas de trabalho do Azure em relação aos cinco pilares do [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/).

Como um recurso principal do Advisor, a Pontuação do supervisor pode ajudá-lo a atingir essas metas com eficiência e eficiência.

Para obter o máximo do Azure, é crucial entender onde você está em sua jornada de otimização da carga de trabalho. Você precisa saber quais serviços ou recursos são consumidos bem e quais não são. Além disso, você desejará saber como priorizar suas ações, com base nas recomendações, para maximizar o resultado.

Também é importante acompanhar e relatar o progresso que você está fazendo nesta jornada de otimização. Com a Pontuação do supervisor, você pode facilmente fazer todas essas coisas com a nova experiência de Gamificação.

Como seu consultor de nuvem personalizado, o Azure Advisor avalia continuamente a telemetria de uso e a configuração de recursos para verificar as práticas recomendadas do setor. Em seguida, o Advisor agrega suas descobertas em uma única Pontuação. Com essa pontuação, você pode perceber rapidamente se está realizando as etapas necessárias para criar soluções confiáveis, seguras e econômicas.

A Pontuação do supervisor consiste em uma pontuação geral, que pode ser dividida em cinco pontuações de categoria. Uma pontuação para cada categoria de supervisor representa os cinco pilares da estrutura de Well-Architected.

Você pode acompanhar o progresso feito ao longo do tempo exibindo a pontuação geral e a pontuação da categoria com as tendências diária, semanal e mensal. Você também pode definir parâmetros de comparação para ajudá-lo a atingir suas metas.

 ![Captura de tela que mostra a página de Pontuação do supervisor.](./media/advisor-score-1.png)

## <a name="interpret-an-advisor-score"></a>Interpretar uma pontuação do supervisor

O Advisor exibe a pontuação geral do consultor e uma divisão para categorias do supervisor, em percentuais. Uma pontuação de 100% em qualquer categoria significa que todos os seus recursos avaliados pelo Advisor seguem as práticas recomendadas que o Advisor recomenda. Na outra extremidade do espectro, uma pontuação de 0% significa que nenhum dos seus recursos avaliados pelo Advisor seguem as recomendações do Advisor. Usando esses detalhamentos de pontuação, você pode facilmente obter o seguinte fluxo:

* A **Pontuação do supervisor** ajuda você a fazer a linha de base de como sua carga de trabalho ou assinaturas estão fazendo com base em uma pontuação do supervisor. Você também pode ver as tendências históricas para entender o que é sua tendência.
* A **pontuação por categoria** para cada recomendação informa quais recomendações pendentes melhorarão a sua pontuação mais. Esses valores refletem o peso da recomendação e a facilidade de implementação prevista. Esses fatores ajudam a garantir que você possa obter o máximo de valor com seu tempo. Eles também ajudam com a priorização.
* O **impacto da Pontuação de categoria** para cada recomendação ajuda a priorizar suas ações de correção para cada categoria.

A contribuição de cada recomendação para sua pontuação de categoria é mostrada claramente na página de **Pontuação do supervisor** na portal do Azure. Você pode aumentar cada Pontuação de categoria pelo ponto de porcentagem listado na coluna **Pontuação potencial de aumento** . Esse valor reflete o peso da recomendação dentro da categoria e a facilidade de implementação prevista para resolver as tarefas potencialmente mais fáceis. Concentrar-se nas recomendações com o maior impacto de Pontuação ajudará você a aumentar o progresso com o tempo.

![Captura de tela que mostra o impacto da Pontuação do supervisor.](./media/advisor-score-2.png)

Se qualquer recomendação do Advisor não for relevante para um recurso individual, você poderá adiar ou ignorar essas recomendações. Eles serão excluídos do cálculo de pontuação com a próxima atualização. O Advisor também usará essa entrada como comentários adicionais para melhorar o modelo.

## <a name="how-is-an-advisor-score-calculated"></a>Como uma pontuação do Advisor é calculada?

O Advisor exibe as pontuações de categoria e sua pontuação geral do consultor como porcentagens. Uma pontuação de 100% em qualquer categoria significa todos os seus recursos, *avaliados pelo Advisor*, siga as práticas recomendadas que o Advisor recomenda. Na outra extremidade do espectro, uma pontuação de 0% significa que nenhum dos seus recursos, avaliados pelo Advisor, segue as recomendações do Advisor.

**Cada uma das cinco categorias tem uma pontuação potencial mais alta de 100.** A pontuação geral do consultor é calculada como uma soma de cada Pontuação de categoria aplicável, dividida pela soma da Pontuação potencial mais alta de todas as categorias aplicáveis. Para a maioria das assinaturas, isso significa que o Advisor soma a pontuação de cada categoria e divide por 500. Mas *cada Pontuação de categoria é calculada somente se você usar recursos que são avaliados pelo Advisor*.

### <a name="advisor-score-calculation-example"></a>Exemplo de cálculo de Pontuação do Advisor

* **Pontuação de assinatura única:** Este exemplo é a média simples de todas as pontuações de categoria do Advisor para sua assinatura. Se as pontuações de categoria do supervisor forem- **cost** = 73, **fiabilidade** = 85, **excelência operacional** = 77 e **desempenho** = 100, a Pontuação do supervisor será (73 + 85 + 77 + 100)/(4x100) = 0,84% ou 84%.
* **Pontuação de várias assinaturas:** Quando várias assinaturas são selecionadas, as pontuações gerais do consultor geradas são pontuações de categoria agregadas ponderadas. Aqui, cada Pontuação de categoria do supervisor é agregada com base nos recursos consumidos pelas assinaturas. Depois que o supervisor tem as pontuações de categoria agregadas ponderadas, o Advisor faz um simples cálculo médio para fornecer uma pontuação geral para assinaturas.

### <a name="scoring-methodology"></a>Metodologia de Pontuação

O cálculo da Pontuação do supervisor pode ser resumido em quatro etapas:

1. O Advisor calcula o *custo de varejo dos recursos afetados*. Esses recursos são aqueles em suas assinaturas que têm pelo menos uma recomendação no Advisor.
1. O Advisor calcula o *custo de varejo dos recursos avaliados*. Esses recursos são aqueles monitorados pelo Advisor, independentemente de terem ou não recomendações.
1. Para cada tipo de recomendação, o Advisor calcula a *taxa de recursos íntegros*. Essa taxa é o custo de varejo dos recursos afetados divididos pelo custo de varejo dos recursos avaliados.
1. O Advisor aplica três pesos adicionais à taxa de recursos íntegros em cada categoria:

   * As recomendações com maior impacto são ponderadas mais pesadas do que as recomendações com menor impacto.
   * Os recursos com recomendações duradouras conterão mais em relação à sua pontuação.
   * Os recursos que você adiar ou ignorar no Advisor serão removidos inteiramente do seu cálculo de pontuação.

O Advisor aplica esse modelo em um nível de categoria do Advisor para fornecer uma pontuação de supervisor para cada categoria. A **segurança** usa um modelo de [Pontuação seguro](../security-center/secure-score-security-controls.md#introduction-to-secure-score) . Uma média simples produz a pontuação final do consultor.

## <a name="advisor-score-faqs"></a>Perguntas frequentes sobre o supervisor

### <a name="how-often-is-my-score-refreshed"></a>Com que frequência a minha pontuação é atualizada?

Sua pontuação é atualizada pelo menos uma vez por dia.

### <a name="why-do-some-recommendations-have-the-empty---value-in-the-category-score-impact-column"></a>Por que algumas recomendações têm o valor "-" vazio na coluna impacto da pontuação da categoria?

O Advisor não inclui imediatamente novas recomendações ou recomendações com alterações recentes no modelo de pontuação. Após um curto período de avaliação, normalmente algumas semanas, elas são incluídas na pontuação.

### <a name="why-is-the-cost-score-impact-greater-for-some-recommendations-even-if-they-have-lower-potential-savings"></a>Por que o impacto da Pontuação de custo é maior para algumas recomendações, mesmo que elas tenham menor economia potencial?

Sua pontuação de **custo** reflete a economia potencial de recursos subutilizados e a facilidade prevista de implementar essas recomendações. Por exemplo, o peso extra é aplicado aos recursos afetados que ficam ociosos por um tempo maior, mesmo que a economia potencial seja menor.

### <a name="why-dont-i-have-a-score-for-one-or-more-categories-or-subscriptions"></a>Por que não tenho uma pontuação para uma ou mais categorias ou assinaturas?

O Advisor gera uma pontuação apenas para as categorias e assinaturas que têm recursos que são avaliados pelo Advisor.

### <a name="what-if-a-recommendation-isnt-relevant"></a>E se uma recomendação não for relevante?

Se você ignorar uma recomendação do Advisor, ele será omitido do cálculo da sua pontuação. Ignorar recomendações também ajuda o Advisor a melhorar a qualidade das recomendações.

### <a name="why-did-my-score-change"></a>Por que minha pontuação foi alterada?

A classificação pode ser alterada se você corrigir recursos impactados adotando as práticas recomendadas que o Advisor recomenda. Se você ou qualquer pessoa com permissões em sua assinatura tiver modificado ou criado novos recursos, você também poderá ver flutuações na sua pontuação. Sua pontuação se baseia em uma taxa dos recursos com impacto no custo em relação ao custo total de todos os recursos.

### <a name="how-does-advisor-calculate-the-retail-cost-of-resources-on-a-subscription"></a>Como o Advisor calcula o custo de varejo dos recursos em uma assinatura?

O Advisor usa as tarifas pagas conforme o uso publicadas no [preço do Azure](https://azure.microsoft.com/pricing/). Essas tarifas não refletem nenhum desconto aplicável. As tarifas são então multiplicadas pela quantidade de uso no último dia em que o recurso foi alocado. Omitir os descontos do cálculo do custo de recursos torna as pontuações do supervisor comparáveis entre assinaturas, locatários e registros onde os descontos podem variar.

### <a name="do-i-need-to-view-the-recommendations-in-advisor-to-get-points-for-my-score"></a>É necessário exibir as recomendações no Advisor para obter pontos para minha Pontuação?

Não. Sua pontuação reflete se você adotar práticas recomendadas que o Advisor recomenda, mesmo se você adotar essas práticas recomendadas proativamente e nunca exibir suas recomendações no Advisor.

### <a name="does-the-scoring-methodology-differentiate-between-production-and-dev-test-workloads"></a>A metodologia de Pontuação diferencia as cargas de trabalho de produção e desenvolvimento/teste?

Não, não por enquanto. Mas você pode descartar as recomendações de recursos individuais se esses recursos forem usados para desenvolvimento e teste e as recomendações não se aplicarem.

### <a name="can-i-compare-scores-between-a-subscription-with-100-resources-and-a-subscription-with-100000-resources"></a>Posso comparar pontuações entre uma assinatura com recursos 100 e uma assinatura com recursos de 100.000?

A metodologia de pontuação foi projetada para controlar o número de recursos em uma combinação de assinatura e de serviço. As assinaturas com menos recursos podem ter pontuações maiores ou menores do que as assinaturas com mais recursos.

### <a name="what-does-it-mean-when-i-see-coming-soon-in-the-score-impact-column"></a>O que isso significa quando vejo "em breve" na coluna de impacto de Pontuação?

Essa mensagem significa que a recomendação é nova e estamos trabalhando para colocá-la no modelo de Pontuação do supervisor. Depois que essa nova recomendação for considerada em um cálculo de pontuação, você verá o valor de impacto de Pontuação para sua recomendação.

### <a name="does-my-score-depend-on-how-much-i-spend-on-azure"></a>Minha pontuação depende de quanto eu gasto no Azure?

Não. Sua pontuação não é necessariamente uma reflexão do quanto você gasta. Os gastos desnecessários resultarão em uma pontuação de **custo** menor.

## <a name="access-advisor-score"></a>Pontuação do supervisor de acesso

A Pontuação do supervisor está em visualização pública no portal do Azure. No painel esquerdo, na seção **supervisor** , consulte Pontuação do **supervisor**.

![Captura de tela que mostra o ponto de entrada de Pontuação do supervisor.](./media/advisor-score-3.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre as recomendações do Assistente, consulte:

* [Introdução ao Advisor](advisor-overview.md)
* [Introdução ao Assistente](advisor-get-started.md)
* [Recomendações de custo do Advisor](advisor-cost-recommendations.md)
* [Recomendações de desempenho do Advisor](advisor-performance-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
* [Recomendações de excelência operacional do Advisor](advisor-operational-excellence-recommendations.md)
