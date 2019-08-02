---
title: Pontuação de recompensa – Personalizador
titleSuffix: Azure Cognitive Services
description: A pontuação da recompensa indica como foi o resultado da escolha de personalização, RewardActionID, para o usuário. O valor da pontuação de recompensa é determinado pela sua lógica de negócios, com base em observações do comportamento do usuário. O personalizador treina seus modelos de machine learning avaliando as recompensas.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: diberry
ms.openlocfilehash: 72c425a1ec9fb83cc2e9dd1bae2c4f521109f162
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663386"
---
# <a name="reward-scores-indicate-success-of-personalization"></a>Pontuações de recompensa indicam o sucesso da personalização

A pontuação da recompensa indica como foi o resultado da escolha de personalização, [RewardActionID](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/rank/rank#response), para o usuário. O valor da pontuação de recompensa é determinado pela sua lógica de negócios, com base em observações do comportamento do usuário.

O personalizador treina seus modelos de machine learning avaliando as recompensas. 

## <a name="use-reward-api-to-send-reward-score-to-personalizer"></a>Usar a API de Recompensa para enviar pontuação de recompensa ao Personalizador

As recompensas são enviadas ao Personalizador pela [API de Recompensa](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/events/reward). Uma recompensa é um número de -1 a 1. O Personalizador treina o modelo para obter a soma mais alta possível de recompensas ao longo do tempo.

As recompensas são enviadas depois que o comportamento do usuário ocorre, o que pode ser dias mais tarde. O tempo máximo que o Personalizador aguardará até que um evento seja considerado para receber nenhuma recompensa ou uma recompensa padrão está configurado no [Tempo de espera de recompensa](#reward-wait-time) no portal do Azure.

Se a pontuação de recompensa para um evento ainda não tiver sido recebida dentro do **Tempo de espera de recompensa**, a **Recompensa padrão** será aplicada. Normalmente, a **[Recompensa padrão](how-to-settings.md#configure-reward-settings-for-the-feedback-loop-based-on-use-case)** está configurada como zero.


## <a name="behaviors-and-data-to-consider-for-rewards"></a>Comportamentos e dados a serem considerados para recompensas

Considere esses sinais e comportamentos para o contexto da pontuação de recompensa:

* Entrada direta do usuário para sugestões quando há opções ("Você quis dizer X?").
* Duração da sessão.
* Tempo entre sessões.
* Análise de sentimento das interações do usuário.
* Dúvidas diretas e pequenas pesquisas em que o bot pede ao usuário para enviar comentários sobre a utilidade e a precisão.
* Resposta a alertas ou atraso na resposta aos alertas.

## <a name="composing-reward-scores"></a>Como elaborar as pontuações de recompensa

Uma pontuação de recompensa deve ser calculada em sua lógica de negócios. A pontuação pode ser representada como:

* Um único número enviado uma vez 
* Uma pontuação enviada imediatamente (por exemplo, 0,8) e uma pontuação adicional enviada mais tarde (normalmente, 0,2).

## <a name="default-rewards"></a>Recompensas padrão

Se nenhuma recompensa for recebida dentro do [Tempo de espera de recompensa](#reward-wait-time), a duração desde a chamada de classificação, o Personalizador aplicará implicitamente a **recompensa padrão** a esse evento de classificação.

## <a name="building-up-rewards-with-multiple-factors"></a>Como criar remunerações com vários fatores  

Para uma personalização efetiva, é possível criar a pontuação de recompensa (qualquer número de -1 a 1) com base em vários fatores. 

Por exemplo, você pode aplicar estas regras para personalizar uma lista de conteúdo de vídeo:

|Comportamento do usuário|Valor parcial da pontuação|
|--|--|
|O usuário clicou no item principal.|Recompensa de +0,5|
|O usuário abriu o conteúdo real desse item.|Recompensa de +0,3|
|O usuário assistiu 5 minutos de conteúdo ou 30%, o que for maior.|Recompensa de +0,2|
|||

Em seguida, você pode enviar a recompensa total para a API.

## <a name="calling-the-reward-api-multiple-times"></a>Como chamar a API de Recompensa várias vezes

Também é possível chamar a API de Recompensa usando a mesma ID de evento, enviando diferentes pontuações de recompensa. Quando o Personalizador obtém essas recompensas, determina a recompensa final para o evento agregando-as conforme especificado nas configurações do Personalizador.

Configurações de agregação:

*  **Primeira**: usa a primeira pontuação de recompensa recebida para o evento e descarta o restante.
* **Soma**: usa todas as pontuações de recompensa coletadas para a ID do Evento e as soma.

Todas as recompensas para um evento recebidas após o **Tempo de espera de recompensa** são descartadas e não afetam o treinamento de modelos.

Com a soma das pontuações de recompensa, a recompensa final pode ser maior que 1 ou menor que -1. Isso não causa falha no serviço.

<!--
@edjez - is the number ignored if it is outside the acceptable range?
-->

## <a name="best-practices-for-calculating-reward-score"></a>Melhores práticas para calcular a pontuação de recompensa

* **Leve em conta indicadores reais de uma personalização bem-sucedida**: é fácil pensar em termos de cliques, mas uma boa recompensa baseia-se naquilo que você deseja que os usuários *alcancem*, em vez do que você quer que as pessoas *façam*.  Por exemplo, a recompensa por cliques pode levar à seleção de conteúdo sujeito a clickbait.

* **Use uma pontuação de recompensa que reflita o desempenho da personalização**: espera-se que a personalização de uma sugestão de filme faça com que o usuário assista ao filme e dê uma classificação alta. Como a classificação do filme provavelmente depende de muitos fatores (qualidade de atuação, humor do usuário), não é um bom sinal de recompensa para o desempenho da *personalização*. O usuário assistir aos primeiros minutos do filme, no entanto, pode ser um sinal melhor da eficácia da personalização, e enviar uma recompensa de 1 após 5 minutos será um sinal melhor.

* **Recompensas se aplicam somente a RewardActionID**: o Personalizador aplica as recompensas para entender a eficácia da ação especificada em RewardActionID. Se você optar por exibir outras ações e o usuário clicar nelas, a recompensa deverá ser zero.

* **Considere as consequências não intencionais**: crie funções de recompensa que levem a resultados responsáveis, com [ética e uso responsável](ethics-responsible-use.md).

* **Use recompensas incrementais**: a adição de recompensas parciais para comportamentos menores do usuário ajuda o Personalizador a alcançar melhor recompensas. Essa recompensa incremental permite que o algoritmo saiba que está ficando mais perto de envolver o usuário no comportamento final desejado.
    * Se você estiver exibindo uma lista de filmes e o usuário passar o mouse sobre o primeiro por um tempo para ver mais informações, você poderá determinar que ocorreu algum envolvimento do usuário. O comportamento pode contar com uma pontuação de recompensa de 0,1. 
    * Se o usuário abriu a página e, em seguida, saiu, a pontuação de recompensa pode ser 0,2. 

## <a name="reward-wait-time"></a>Tempo de espera de recompensa

O Personalizador correlacionará as informações de uma chamada de classificação com as recompensas enviadas em chamadas de recompensa para treinar o modelo. Elas podem ocorrer em diferentes momentos. O Personalizador aguarda um período limitado, a partir de quando ocorreu a chamada de classificação, mesmo se essa chamada tiver sido feita como um evento inativo e ativada mais tarde.

Se o **Tempo de espera de recompensa** expirar e não houver nenhuma informação de recompensa, uma recompensa padrão será aplicado a esse evento para treinamento. A duração de espera máxima é de 6 dias.

## <a name="best-practices-for-setting-reward-wait-time"></a>Melhores práticas para definir o tempo de espera de recompensa

Siga estas recomendações para obter melhores resultados.

* Defina o menor tempo de espera de recompensa possível, deixando tempo suficiente para obter comentários dos usuários. 

<!--@Edjez - storage quota? -->

* Não escolha uma duração menor do que o tempo necessário para receber comentários. Por exemplo, se alguma das suas recompensas ocorrer depois que um usuário tiver assistido a um minuto de um vídeo, a duração do experimento deverá ter pelo menos o dobro disso.

## <a name="next-steps"></a>Próximas etapas

* [Aprendizado de reforço](concepts-reinforcement-learning.md) 
* [Testar a API de Classificação](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank/console)
* [Testar a API de Recompensa](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)
