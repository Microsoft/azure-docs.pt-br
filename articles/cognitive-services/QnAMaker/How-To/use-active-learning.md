---
title: Usar o aprendizado ativo com a base de dados de conhecimento-QnA Maker
description: Aprenda a melhorar a qualidade de sua base de dados de conhecimento com o aprendizado ativo. Examine, aceite ou rejeite, adicione sem remover ou altere as perguntas existentes.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 87dde7662050794a24cf976a0bae6237b91d29b2
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102213701"
---
# <a name="active-learning"></a>Aprendizado ativo

O recurso de _sugestões de aprendizado ativo_ permite que você aprimore a qualidade de sua base de dados de conhecimento sugerindo perguntas alternativas, com base em envios de usuários, para seu par de perguntas e respostas. Você examina essas sugestões, adicionando-as às perguntas existentes ou rejeitando-as.

Sua base de dados de conhecimento não é alterada automaticamente. Para que qualquer alteração entre em vigor, você deve aceitar as sugestões. Essas sugestões adicionam perguntas, mas não alteram nem removem perguntas existentes.

## <a name="what-is-active-learning"></a>O que é o aprendizado ativo?

O QnA Maker aprende novas variações de pergunta com comentários implícitos e explícitos.

* [Comentários implícitos](#how-qna-makers-implicit-feedback-works) – o classificador entende quando uma pergunta de usuário tem várias respostas com pontuações que são muito próximas e considera isso como comentários. Você não precisa fazer nada para que isso aconteça.
* [Comentários explícitos](#how-you-give-explicit-feedback-with-the-train-api) – quando várias respostas com pouca variação nas pontuações são retornadas da base de dados de conhecimento, o aplicativo cliente pergunta ao usuário qual pergunta é a pergunta correta. Os comentários explícitos do usuário são enviados para QnA Maker com a [API de treinamento](../How-To/improve-knowledge-base.md#train-api).

Ambos os métodos fornecem o classificador com consultas semelhantes que são clusterizadas.

## <a name="how-active-learning-works"></a>Como o aprendizado ativo funciona

O aprendizado ativo é disparado com base nas pontuações das primeiras respostas retornadas pelo QnA Maker. Se as diferenças de pontuação entre os pares de QnA que correspondem à consulta estiverem dentro de um pequeno intervalo, a consulta será considerada uma possível sugestão (como uma pergunta alternativa) para cada um dos pares de QnA possíveis. Depois de aceitar a pergunta sugerida para um par QnA específico, ele será rejeitado para os outros pares. Você precisa se lembrar de salvar e treinar, depois de aceitar sugestões.

O aprendizado ativo fornece as melhores sugestões possíveis em casos em que os pontos de extremidade estão obtendo uma quantidade e variedade razoáveis de consultas de uso. Quando 5 ou mais consultas semelhantes são clusterizadas, a cada 30 minutos, QnA Maker sugere as perguntas baseadas no usuário para que o designer da base de dados de conhecimento aceite ou rejeite. Todas as sugestões são clusterizadas por similaridade e as principais sugestões de perguntas alternativas são exibidas com base na frequência das consultas específicas por usuários finais.

Quando as perguntas são sugeridas no portal de QnA Maker, você precisa revisar e aceitar ou rejeitar essas sugestões. Não há uma API para gerenciar sugestões.

## <a name="how-qna-makers-implicit-feedback-works"></a>Como o comentário implícito do QnA Maker funciona

Os comentários implícitos do QnA Maker usam um algoritmo para determinar a proximidade da pontuação e, em seguida, cria sugestões de aprendizado ativo. O algoritmo para determinar a proximidade não é um cálculo simples. Os intervalos no exemplo a seguir não devem ser corrigidos, mas devem ser usados como um guia para entender o impacto apenas do algoritmo.

Quando a pontuação de uma pergunta tem um alto grau de confiabilidade, tal como 80%, o intervalo de pontuações que são consideradas para aprendizado ativo é amplo, dentro de aproximadamente 10%. Conforme a pontuação de confiança diminui, por exemplo, para 40%, o intervalo de pontuações diminui também, para dentro de aproximadamente 4%.

Na resposta JSON a seguir de uma consulta para QnA Maker generateAnswer, as pontuações de A, B e C estão perto e seriam consideradas como sugestões.

```json
{
  "activeLearningEnabled": true,
  "answers": [
    {
      "questions": [
        "Q1"
      ],
      "answer": "A1",
      "score": 80,
      "id": 15,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q2"
      ],
      "answer": "A2",
      "score": 78,
      "id": 16,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q3"
      ],
      "answer": "A3",
      "score": 75,
      "id": 17,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q4"
      ],
      "answer": "A4",
      "score": 50,
      "id": 18,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    }
  ]
}
```

QnA Maker não saberá qual resposta é a melhor resposta. Use a lista de sugestões do portal de QnA Maker para selecionar a melhor resposta e treine novamente.


## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Como você dá comentários explícitos com a API de treinamento

QnA Maker precisa de comentários explícitos sobre quais das respostas foi a melhor resposta. Como a melhor resposta é determinada, você pode incluir:

* Comentários do usuário, selecionando uma das respostas.
* Lógica de negócios, como determinar um intervalo de Pontuação aceitável.
* Uma combinação dos comentários do usuário e da lógica de negócios.

Use a [API de treinamento](/rest/api/cognitiveservices/qnamaker4.0/runtime/train) para enviar a resposta correta para QnA Maker, depois que o usuário a selecionar.

## <a name="upgrade-runtime-version-to-use-active-learning"></a>Atualizar a versão de tempo de execução para usar o aprendizado ativo

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

O aprendizado ativo é compatível com a versão de runtime 4.4.0 e superior. Se sua base de dados de conhecimento foi criada em uma versão anterior, [atualize seu runtime](configure-QnA-Maker-resources.md#get-the-latest-runtime-updates) para usar esse recurso.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

No QnA Maker gerenciado (versão prévia), como o tempo de execução é hospedado pelo próprio serviço de QnA Maker, não há necessidade de atualizar o tempo de execução manualmente.

---

## <a name="turn-on-active-learning-for-alternate-questions"></a>Ativar o aprendizado ativo para perguntas alternativas

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

O aprendizado ativo está desativado por padrão. Ative-o para ver sugestões de perguntas. Depois de ativar o aprendizado ativo, você precisa enviar informações do aplicativo cliente para QnA Maker. Para obter mais informações, consulte [o fluxo arquitetônico para usar GenerateAnswer e treinar APIs de um bot](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Selecione **publicar** para publicar a base de dados de conhecimento. Consultas de aprendizado ativo são coletadas somente do ponto de extremidade de previsão da API GenerateAnswer. As consultas ao painel de teste no portal de QnA Maker não afetam o aprendizado ativo.

1. Para ativar o aprendizado ativo no portal de QnA Maker, vá para o canto superior direito, selecione seu **nome**, vá para [**configurações de serviço**](https://www.qnamaker.ai/UserSettings).

    ![Ative as alternativas de perguntas sugeridas do aprendizado ativo na página Configurações de serviço. Selecione seu nome de usuário no menu superior direito e, em seguida, selecione configurações de serviço.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Localize o serviço QnA Maker e, em seguida, ative o **aprendizado ativo**.

    > [!div class="mx-imgBorder"]
    > [![Na página Configurações de serviço, alterne o recurso de aprendizado ativo. Se não for possível alternar o recurso, talvez seja necessário atualizar seu serviço.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > A versão exata na imagem anterior é mostrada apenas como um exemplo. Sua versão pode ser diferente.

    Quando o **aprendizado ativo** é habilitado, a base de dados de conhecimento sugere novas perguntas em intervalos regulares com base em perguntas enviadas pelo usuário. Você pode desabilitar o **aprendizado ativo** desativando a configuração novamente.
    
# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

Por padrão, o aprendizado ativo está **ativado** no QnA Maker gerenciado (versão prévia). Para ver as perguntas alternativas sugeridas, [use as opções de exibição](../How-To/improve-knowledge-base.md#view-suggested-questions) na página Editar.

---

## <a name="review-suggested-alternate-questions"></a>Revisar as perguntas alternativas sugeridas

[Examine as perguntas sugeridas alternativas](improve-knowledge-base.md) na página de **edição** de cada base de dados de conhecimento.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar uma base de dados de conhecimento](./manage-knowledge-bases.md)
