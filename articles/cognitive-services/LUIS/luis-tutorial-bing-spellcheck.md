---
title: Corrigir palavras incorretas-LUIS
titleSuffix: Azure Cognitive Services
description: Corrija palavras grafadas incorretamente na declaração adicionando a API de Verificação Ortográfica do Bing V7 para consultas de ponto de extremidade do LUIS.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 01/12/2021
ms.openlocfilehash: 509d1dc0b94bdfa9be5185df0bad793f7702eb26
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101731027"
---
# <a name="correct-misspelled-words-with-bing-resource"></a>Corrigir palavras incorretas com o recurso do Bing

Agora, a API de previsão V3 dá suporte à [API de Verificação Ortográfica do Bing](/bing/search-apis/bing-spell-check/overview). Adicione a verificação ortográfica ao seu aplicativo, incluindo a chave para o recurso de pesquisa do Bing no cabeçalho de suas solicitações. Você pode usar um recurso existente do Bing se você já tiver um ou [criar um novo](https://portal.azure.com/#create/Microsoft.BingSearch) para usar esse recurso. 

Exemplo de saída de previsão para uma consulta grafada incorretamente:

```json
{
  "query": "bouk me a fliht to kayro",
  "prediction": {
    "alteredQuery": "book me a flight to cairo",
    "topIntent": "book a flight",
    "intents": {
      "book a flight": {
        "score": 0.9480589
      }
      "None": {
        "score": 0.0332136229
      }
    },
    "entities": {}
  }
}
```

As correções de ortografia são feitas antes da previsão de expressão do usuário LUIS. Você pode ver quaisquer alterações no expressão original, incluindo a ortografia, na resposta.

## <a name="create-bing-search-resource"></a>Criar Pesquisa do Bing recurso

Para criar um recurso de Pesquisa do Bing no portal do Azure, siga estas instruções:

1. Faça logon no [Portal do Azure](https://portal.azure.com).

2. No canto superior esquerdo, selecione **Criar um recurso**.

3. Na caixa de pesquisa, insira `Bing Search V7` e selecione o serviço.

4. Um painel de informações aparece à direita contendo informações, incluindo o Aviso Legal. Selecione **Criar** para começar o processo de criação da assinatura.

> [!div class="mx-imgBorder"]
> ![Verificação Ortográfica do Bing recurso v7 da API](./media/luis-tutorial-bing-spellcheck/bing-search-resource-portal.png)

5. No painel seguinte, insira as configurações do seu serviço. Aguarde a conclusão do processo de criação do serviço.

6. Depois que o recurso for criado, vá para a folha **chaves e ponto de extremidade** à esquerda. 

7. Copie uma das chaves a serem adicionadas ao cabeçalho da sua solicitação de previsão. Você precisará apenas de uma das duas chaves.

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="enable-spell-check-from-ui"></a>Habilitar verificação ortográfica da interface do usuário 
Você pode habilitar a verificação ortográfica para sua consulta de exemplo usando o [portal do Luis](https://www.luis.ai). Selecione **gerenciar** na parte superior da tela e **recursos do Azure** no painel de navegação esquerdo. Depois de associar um recurso de previsão ao seu aplicativo, você pode selecionar **alterar parâmetros de consulta** na parte inferior da página e colar a chave de recurso no campo **Habilitar verificação ortográfica** .
    
   > [!div class="mx-imgBorder"]
   > ![Habilitar verificação ortográfica](./media/luis-tutorial-bing-spellcheck/spellcheck-query-params.png)


## <a name="adding-the-key-to-the-endpoint-url"></a>Adicionar a chave à URL do ponto de extremidade
Para cada consulta na qual você deseja aplicar a correção ortográfica, a consulta de ponto de extremidade precisa da chave de recurso de verificação ortográfica do Bing passada no parâmetro de cabeçalho de consulta. Você pode ter um chatbot que chama o LUIS ou você pode chamar diretamente a API do ponto de extremidade do LUIS. Independentemente de como o ponto de extremidade é chamado, cada chamada deve incluir as informações necessárias na solicitação do cabeçalho para que as correções ortográficas funcionem corretamente. Você deve definir o valor com **MKT-Bing-corretor-check-Key** para o valor da chave.

|Chave de cabeçalho|Valor do cabeçalho|
|--|--|
|`mkt-bing-spell-check-key`|Chaves encontradas em **chaves e** folha de ponto de extremidade do recurso|

## <a name="send-misspelled-utterance-to-luis"></a>Enviar declaração incorreta para o LUIS
1. Adicione um expressão digitado incorretamente na consulta de previsão que você enviará como "até o momento é a montanha?". Em português, `mountain`, com um `n`, é a ortografia correta.

2. O LUIS responde com um resultado JSON para `How far is the mountain?`. Se a API de Verificação Ortográfica do Bing v7 detectar um erro de ortografia, o campo `query` na resposta JSON do aplicativo LUIS conterá a consulta original, e o campo `alteredQuery` conterá a consulta corrigida enviada ao LUIS.

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

Se você não quiser usar o serviço do Pesquisa do Bing API v7, será necessário adicionar a grafia correta e incorreta.

Duas soluções são:

* Exemplo de rótulo declarações que têm todas as diferentes ortografias para que o LUIS possa aprender a grafia adequada, bem como erros de digitação. Essa opção exige mais esforço de aplicação de rótulos do que o uso de um verificador ortográfico.
* Crie uma lista de frases com todas as variações da palavra. Com essa solução, você não precisa rotular as variações de palavras no exemplo declarações.


> [!div class="nextstepaction"]
> [Saiba mais sobre declarações de exemplo](./luis-how-to-add-entities.md)