---
title: Corrigir palavras incorretas-LUIS
titleSuffix: Azure Cognitive Services
description: Corrija palavras grafadas incorretamente na declaração adicionando a API de Verificação Ortográfica do Bing V7 para consultas de ponto de extremidade do LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: bf507fcb52d0c6a57453bd5f038530640e62ba9c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563347"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Corrigir palavras grafas incorretamente com a Verificação Ortográfica do Bing

Você pode integrar seu aplicativo LUIS com a [API de Verificação Ortográfica do Bing V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) para corrigir a ortografia de palavras na declaração antes que o LUIS preveja a pontuação e as entidades da declaração. 

## <a name="create-first-key-for-bing-spell-check-v7"></a>Criar a primeira chave para a Verificação Ortográfica do Bing V7
A [primeira chave da API de Verificação Ortográfica do Bing v7](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) é gratuita. 

![Criar uma chave gratuita](./media/luis-tutorial-bing-spellcheck/free-key.png)

<a name"create-subscription-key"></a>
## <a name="create-endpoint-key"></a>Criar chave de ponto de extremidade
Se a chave gratuita expirou, crie uma chave de ponto de extremidade.

1. Faça logon no [Portal do Azure](https://portal.azure.com). 

2. No canto superior esquerdo, selecione **Criar um recurso**.

3. Na caixa de pesquisa, insira `Bing Spell Check API V7`.

    ![Pesquise por API de Verificação Ortográfica do Bing V7](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Selecione o serviço. 

5. Um painel de informações aparece à direita contendo informações, incluindo o Aviso Legal. Selecione **Criar** para começar o processo de criação da assinatura. 

6. No painel seguinte, insira as configurações do seu serviço. Aguarde a conclusão do processo de criação do serviço.

    ![Inserir configurações do serviço](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. Selecione **Todos os recursos** sob o título **Favoritos** na navegação à esquerda.

8. Selecione o novo serviço. Seu tipo é **Serviços Cognitivos** e o local é **global**. 

9. No painel principal, selecione **Chaves** para ver suas novas chaves.

    ![Pegar as chaves](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Copie a primeira chave. Você precisa apenas de uma das duas chaves. 

## <a name="using-the-key-in-luis-test-panel"></a>Usar a chave no painel de teste do LUIS
Há dois locais no LUIS para usar a chave. A primeira é no [painel de teste](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). A chave não é salva no LUIS, em vez disso, é uma variável de sessão. Você precisa definir a chave toda vez que quiser que o painel de teste aplique o serviço da API de Verificação Ortográfica do Bing v7 na declaração. Veja as [instruções](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) no painel de teste para configurar a chave.

## <a name="adding-the-key-to-the-endpoint-url"></a>Adicionar a chave à URL do ponto de extremidade
A consulta do ponto de extremidade precisa que a chave seja passada nos parâmetros da cadeia de consulta de cada consulta na qual você quer aplicar a correção ortográfica. Você pode ter um chatbot que chama o LUIS ou você pode chamar diretamente a API do ponto de extremidade do LUIS. Independentemente de como o ponto de extremidade é chamado, toda chamada deve incluir as informações necessárias para que a correção ortográfica funcione corretamente.

A URL do ponto de extremidade tem vários valores que precisam ser passados corretamente. A API de Verificação Ortográfica do Bing v7 é apenas mais um. Você deve definir o parâmetro **spellCheck** como true e o valor de **bing-spell-check-subscription-key** para o valor da chave:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=**true**&bing-spell-check-subscription-key=**{bingKey}**&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>Enviar declaração incorreta para o LUIS
1. Em um navegador da Web, copie a cadeia de caracteres anterior e substitua `region`, `appId`, `luisKey` e `bingKey` por seus próprios valores. Use a região do ponto de extremidade, se for diferente da sua [região](luis-reference-regions.md) de publicação.

2. Adicione uma declaração incorreta, como "A que distância fica a montannha?". Em português, `mountain`, com um `n`, é a ortografia correta. 

3. Selecione Enter para enviar a consulta ao LUIS.

4. O LUIS responde com um resultado JSON para `How far is the mountain?`. Se a API de Verificação Ortográfica do Bing v7 detectar um erro de ortografia, o campo `query` na resposta JSON do aplicativo LUIS conterá a consulta original, e o campo `alteredQuery` conterá a consulta corrigida enviada ao LUIS.

```json
{
  "query": "How far is the mountainn?",
  "alteredQuery": "How far is the mountain?",
  "topScoringIntent": {
    "intent": "Concierge",
    "score": 0.183866
  },
  "entities": []
}
```

## <a name="ignore-spelling-mistakes"></a>Ignorar erros de ortografia
Se você não quiser usar o serviço da API de Verificação Ortográfica do Bing v7, poderá rotular as declarações que apresentam erros de ortografia para que o LUIS possa aprender a ortografia correta, bem como erros de digitação. Essa opção exige mais esforço de aplicação de rótulos do que o uso de um verificador ortográfico.

## <a name="publishing-page"></a>Página de publicação
A página de [publicação](luis-how-to-publish-app.md) tem uma caixa de seleção **Habilitar o verificador ortográfico do Bing**. Isso é uma conveniência para criar a chave e entender como a URL de ponto de extremidade é alterada. Você ainda precisará usar os parâmetros de ponto de extremidade corretos para que a ortografia seja corrigida em cada declaração. 

> [!div class="nextstepaction"]
> [Saiba mais sobre declarações de exemplo](luis-how-to-add-example-utterances.md)
