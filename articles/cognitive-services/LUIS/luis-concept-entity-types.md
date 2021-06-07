---
title: Tipos de entidade-LUIS
description: Uma entidade extrai dados de um usuário expressão no tempo de execução de previsão. Uma finalidade _opcional_ e secundária é impulsionar a previsão da intenção ou de outras entidades usando a entidade como um recurso.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 398d18642052726af4d4920443bad515ec0b5bef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91316555"
---
# <a name="extract-data-with-entities"></a>Extrair dados com entidades

Uma entidade extrai dados de um usuário expressão no tempo de execução de previsão. Uma finalidade _opcional_ e secundária é impulsionar a previsão da intenção ou de outras entidades usando a entidade como um recurso.

Há vários tipos de entidades:

* [entidade de aprendizado de máquina](reference-entity-machine-learned-entity.md) -esta é a entidade principal. Você deve projetar seu esquema com esse tipo de entidade antes de usar outras entidades.
* Não Machine-Learning usado como um [recurso](luis-concept-feature.md) necessário – para correspondências exatas de texto, correspondências de padrões ou detecção por entidades predefinidas
* [Padrão. any](#patternany-entity) -para extrair texto de forma livre, como títulos de livros, de um [padrão](reference-entity-pattern-any.md)

as entidades de aprendizado de máquina fornecem a mais ampla variedade de opções de extração de dados. As entidades que não são de aprendizado de máquina funcionam por correspondência de texto e são usadas como um [recurso necessário](#design-entities-for-decomposition) para uma entidade ou intenção de aprendizado de máquina.

## <a name="entities-represent-data"></a>Entidades representam dados

As entidades são dados que você deseja extrair do expressão, como nomes, datas, nomes de produtos ou qualquer grupo significativo de palavras. Uma declaração pode incluir muitas entidades ou nenhuma. Um aplicativo cliente _pode_ precisar dos dados para executar sua tarefa.

As entidades precisam ser rotuladas consistentemente em todos os declarações de treinamento para cada tentativa em um modelo.

 Você pode definir suas próprias entidades ou usar entidades predefinidas para economizar tempo para conceitos comuns, como [datetimeV2](luis-reference-prebuilt-datetimev2.md), [ordinal](luis-reference-prebuilt-ordinal.md), [email](luis-reference-prebuilt-email.md)e [número de telefone](luis-reference-prebuilt-phonenumber.md).

|Enunciado|Entidade|Dados|
|--|--|--|
|Comprar três passagens para Nova York|Número predefinido<br>Destino|3<br>Nova York|


### <a name="entities-are-optional-but-recommended"></a>As entidades são opcionais, mas recomendadas

Embora as [intenções](luis-concept-intent.md) sejam obrigatórias, as entidades são opcionais. Você não precisa criar entidades para todos os conceitos em seu aplicativo, mas apenas para aqueles em que o aplicativo cliente precisa dos dados ou a entidade atua como uma dica ou sinal para outra entidade ou intenção.

Conforme o aplicativo é reparado e uma nova necessidade de dados é identificada, você pode adicionar entidades apropriadas ao modelo LUIS mais tarde.

<a name="entity-compared-to-intent"></a>

## <a name="entity-represents-data-extraction"></a>A entidade representa a extração de dados

A entidade representa um conceito de dados _dentro do expressão_. Uma intenção classifica _todo o expressão_.

Considere os quatro declarações a seguir:

|Enunciado|Intenção prevista|Entidades extraídas|Explicação|
|--|--|--|--|
|Ajuda|ajuda|-|Nada para extrair.|
|Enviar algo|sendSomething|-|Nada para extrair. O modelo não tem um recurso necessário para extrair neste `something` contexto, e não há nenhum destinatário declarado.|
|Enviar Bob a presente|sendSomething|`Bob`, `present`|O modelo extrai `Bob` adicionando um recurso necessário da entidade predefinida `personName` . Uma entidade de aprendizado de máquina foi usada para extrair `present` .|
|Enviar Bob a caixa de chocolates|sendSomething|`Bob`, `box of chocolates`|As duas partes importantes dos dados `Bob` e as foram `box of chocolates` extraídas pelas entidades de aprendizado de máquina.|

## <a name="label-entities-in-all-intents"></a>Rotular entidades em todas as intenções

As entidades extraem dados independentemente da intenção prevista. Certifique-se de rotular _todos os_ exemplos de declarações em todas as intenções. A `None` tentativa de rotulação de entidade ausente causa confusão, mesmo se houvesse muito mais declarações de treinamento para as outras intenções.

## <a name="design-entities-for-decomposition"></a>Projetar entidades para decomposição

as entidades de aprendizado de máquina permitem que você projete seu esquema de aplicativo para decomposição, dividindo um grande conceito em subentidades.

A criação de decomposição permite que o LUIS retorne um nível profundo de resolução de entidade para seu aplicativo cliente. Isso permite que o aplicativo cliente se concentre em regras de negócio e deixe a resolução de dados em LUIS.

Uma entidade de aprendizado de máquina dispara com base no contexto aprendido por meio de exemplo declarações.

as [**entidades de aprendizado de máquina**](tutorial-machine-learned-entity.md) são os extratores de nível superior. As subentidades são entidades filhas de entidades de aprendizado de máquina.

## <a name="effective-machine-learned-entities"></a>Entidades aprendidas da máquina efetivas

Para criar com eficiência as entidades aprendidas pelo computador:

* Seu rotulamento deve ser consistente entre as intenções. Isso inclui até mesmo declarações que você fornece na intenção **None** que inclui essa entidade. Caso contrário, o modelo não será capaz de determinar as sequências com eficiência.
* Se você tiver uma entidade aprendida por máquina com subentidades, certifique-se de que as diferentes ordens e variantes da entidade e das subentidades sejam apresentadas no declarações rotulado. O exemplo rotulado declarações deve incluir todos os formulários válidos e incluir entidades que aparecem e que estão ausentes e também reordenadas dentro do expressão.
* Evite sobreajustar as entidades a um conjunto muito fixo. O **superajuste** acontece quando o modelo não é generalizado e é um problema comum em modelos de aprendizado de máquina. Isso implica que o aplicativo não funcionaria em novos dados adequadamente. Por sua vez, você deve variar o exemplo rotulado declarações para que o aplicativo possa generalizar além dos exemplos limitados que você fornecer. Você deve variar as diferentes subentidades com alteração suficiente para o modelo considerar mais o conceito, em vez de apenas os exemplos mostrados.

## <a name="effective-prebuilt-entities"></a>Entidades predefinidas efetivas

Para criar entidades efetivas que extraem dados comuns, como aqueles fornecidos pelas [entidades predefinidas](luis-reference-prebuilt-entities.md), recomendamos o processo a seguir.

Melhore a extração de dados colocando seus próprios dados em uma entidade como um recurso. Dessa forma, todos os rótulos adicionais de seus dados aprenderão o contexto de onde existem nomes de pessoas em seu aplicativo.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Tipos de entidades

Uma subentidade para um pai deve ser uma entidade de aprendizado de máquina. A subentidade pode usar uma entidade de aprendizagem que não seja de máquina como um [recurso](luis-concept-feature.md).

Escolha a entidade com base em como os dados devem ser extraídos e como devem ser representados após extraídos.

|Tipo de entidade|Finalidade|
|--|--|
|[**Aprendido com o computador**](tutorial-machine-learned-entity.md)|Extraia dados aninhados e complexos aprendidos de exemplos rotulados. |
|[**Lista**](reference-entity-list.md)|Lista de itens e seus sinônimos extraídos com **correspondência exata de texto**.|
|[**Padrão. any**](#patternany-entity)|A entidade em que encontrar o final da entidade é difícil de determinar porque a entidade é de forma livre. Disponível somente em [padrões](luis-concept-patterns.md).|
|[**Predefinida**](luis-reference-prebuilt-entities.md)|Já treinado para extrair um tipo específico de dados, como URL ou email. Algumas dessas entidades predefinidas são definidas no projeto [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) de software livre. Se não houver suporte à sua cultura ou entidade específica, contribua para o projeto.|
|[**Expressão regular**](reference-entity-regular-expression.md)|Usa uma expressão regular para **correspondência exata de texto**.|


## <a name="extraction-versus-resolution"></a>Extração versus resolução

As entidades extraem dados conforme os dados aparecem no expressão. As entidades não alteram nem resolvem os dados. A entidade não fornecerá nenhuma resolução se o texto for um valor válido para a entidade ou não.

Há maneiras de trazer a resolução para a extração, mas você deve estar ciente de que isso limita a capacidade do aplicativo de ficar imune a variações e erros.

As entidades listar entidades e expressões regulares (correspondência de texto) podem ser usadas como [recursos necessários](luis-concept-feature.md#required-features) para uma subentidade e atuam como um filtro para a extração. Você deve usar isso com cuidado para não atrapalhar a capacidade do aplicativo de prever.

## <a name="extracting-contextually-related-data"></a>Extraindo dados relacionados contextualmente

Um expressão pode conter duas ou mais ocorrências de uma entidade em que o significado dos dados é baseado no contexto dentro do expressão. Um exemplo é um expressão para reservar um vôo que tem duas localizações geográficas, origem e destino.

`Book a flight from Seattle to Cairo`

Os dois locais precisam ser extraídos de forma que o aplicativo cliente saiba o tipo de cada local para concluir a compra do tíquete.

Para extrair a origem e o destino, crie duas subentidades como parte da entidade de aprendizado de máquina de pedido de tíquete. Para cada uma das subentidades, crie um recurso necessário que use geographyV2.

<a name="using-component-constraints-to-help-define-entity"></a>
<a name="using-subentity-constraints-to-help-define-entity"></a>

### <a name="using-required-features-to-constrain-entities"></a>Usando os recursos necessários para restringir entidades

Saiba mais sobre os [recursos necessários](luis-concept-feature.md)

## <a name="patternany-entity"></a>Entidade pattern.any

Um padrão. Any está disponível apenas em um [padrão](luis-concept-patterns.md).

<a name="if-you-need-more-than-the-maximum-number-of-entities"></a>
## <a name="exceeding-app-limits-for-entities"></a>Excedendo os limites de aplicativo para entidades

Se você precisar de mais do que o [limite](luis-limits.md#model-limits), entre em contato com o suporte. Para fazer isso, colete informações detalhadas sobre seu sistema, acesse o site do [LUIS](luis-reference-regions.md#luis-website) e selecione **Suporte**. Se a assinatura do Azure incluir serviços de suporte, contate o [suporte técnico do Azure](https://azure.microsoft.com/support/options/).

## <a name="entity-prediction-status-and-errors"></a>Status e erros de previsão de entidade

O portal do LUIS mostra quando a entidade tem uma previsão de entidade diferente da entidade selecionada para um exemplo de expressão. Essa pontuação diferente é baseada no modelo treinado atual. 

:::image type="content" source="./media/luis-concept-entities/portal-entity-prediction-error.png" alt-text="O portal do LUIS mostra quando a entidade tem uma previsão de entidade diferente da entidade selecionada para um exemplo de expressão.":::

O texto de erro é realçado no exemplo expressão e a linha expressão de exemplo tem um indicador de erro à direita, mostrado como um triângulo vermelho. 

Use essas informações para resolver erros de entidade usando um ou mais dos seguintes itens:
* O texto realçado é rotulado com nome. Para corrigir, revisar, corrigir e treinar novamente. 
* Criar um [recurso](luis-concept-feature.md) para a entidade para ajudar a identificar o conceito da entidade
* Adicionar mais [exemplos de declarações](luis-concept-utterance.md) e rótulo com a entidade
* [Examine as sugestões de aprendizado ativo](luis-concept-review-endpoint-utterances.md) para qualquer declarações recebido no ponto de extremidade de previsão que possa ajudar a identificar o conceito da entidade.

## <a name="next-steps"></a>Próximas etapas

Aprenda os conceitos sobre [declarações](luis-concept-utterance.md) boas.

Confira [Adicionar entidades](luis-how-to-add-entities.md) para saber como adicionar entidades ao seu aplicativo LUIS.

Consulte [tutorial: extrair dados estruturados do expressão do usuário com entidades de aprendizado de máquina no reconhecimento vocal (Luis)](tutorial-machine-learned-entity.md) para saber como extrair dados estruturados de um expressão usando a entidade de aprendizado de máquina.

