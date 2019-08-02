---
title: Funções de padrão
titleSuffix: Azure Cognitive Services
description: Padrões extraem dados de enunciados de modelo bem formatados. O enunciado de modelo usa uma entidade simples e funções para extrair dados relacionados, como o local de origem e o local de destino.
ms.custom: seodec18
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: c0e3ac1d53cda2afb2184b92b0fd0afd662101bb
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277511"
---
# <a name="tutorial-extract-contextually-related-patterns-using-roles"></a>Tutorial: Extrair padrões relacionados contextualmente usando funções

Neste tutorial, use um padrão para extrair dados de um enunciado de modelo bem formatado. O enunciado de modelo usa uma [entidade simples](luis-concept-entity-types.md#simple-entity) e [funções](luis-concept-roles.md) para extrair dados relacionados, como o local de origem e o local de destino.  Ao usar padrões, menos declarações de exemplo são necessárias para a intenção.


**Neste tutorial, você aprenderá a:**

> [!div class="checklist"]
> * Importar o aplicativo de exemplo
> * Criar novas entidades
> * Criar nova tentativa
> * Treinar
> * Publicar
> * Obter intenções e entidades do ponto de extremidade
> * Criar padrão com funções
> * Criar lista de expressões de cidades
> * Obter intenções e entidades do ponto de extremidade

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="using-roles-in-patterns"></a>Usando funções em padrões

O propósito das funções é extrair entidades relacionadas contextualmente em um enunciado. No enunciado, `Move new employee Robert Williams from Sacramento and San Francisco`, a cidade de origem e os valores da cidade de destino estão relacionados entre si e usam uma linguagem comum para denotar cada local. 


O nome do novo funcionário, Billy Patterson, não faz parte da entidade lista **funcionário** ainda. O nome do novo funcionário é extraído primeiro, para enviar o nome a um sistema externo para criar as credenciais da empresa. Depois que as credenciais da empresa são criadas, as credenciais dos funcionários são adicionadas à entidade da lista **Funcionários**.

O novo funcionário e a família precisam ser transferidos da cidade atual para uma cidade onde a empresa fictícia esteja localizada. Como um novo funcionário pode vir de qualquer cidade, os locais precisam ser descobertos. Uma lista de configurações, como uma entidade de lista, não funcionaria porque apenas as cidades da lista seriam extraídas.

Os nomes de funções associados às cidades de origem e de destino precisam ser exclusivos em todas as entidades. Uma maneira fácil de garantir que as funções sejam exclusivas é vinculá-las à entidade que as contém por meio de uma estratégia de nomenclatura. A entidade **NewEmployeeRelocation** é uma entidade simples com duas funções: **NewEmployeeReloOrigin** e **NewEmployeeReloDestination**. Relo é a abreviação de realocação.

Porque a expressão de exemplo `Move new employee Robert Williams from Sacramento and San Francisco` tem somente entidades computador aprendeu, é importante fornecer suficiente declarações de exemplo à intenção, de modo que as entidades são detectadas.  

**Embora os padrões permitem que você forneça declarações menos de exemplo, se as entidades não são detectadas, o padrão não coincide.**

Se você tiver dificuldades com a detecção de entidade simples, porque é um nome como uma cidade, considere adicionar uma lista de frases de valores semelhantes. Isso ajuda na detecção do nome da cidade, dando LUIS um sinal adicional sobre esse tipo de palavra ou frase. As listas de frases só ajudam o padrão ajudando na detecção de entidade, que é necessária para que o padrão corresponda. 

## <a name="import-example-app"></a>Importar o aplicativo de exemplo
Continue com o aplicativo criado no último tutorial, denominado **HumanResources**. 

Use as seguintes etapas:

1.  Baixe e salve o [arquivo JSON do aplicativo](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-patterns-HumanResources-v2.json).

2. Importe o JSON em um novo aplicativo.

3. Na seção **Gerenciar**, na guia **Versões**, clone a versão e nomeie-a como `roles`. A clonagem é uma ótima maneira de testar vários recursos de LUIS sem afetar a versão original. Como o nome da versão é usado como parte da rota de URL, o nome não pode conter nenhum caractere que não seja válido em uma URL.

## <a name="create-new-entities"></a>Criar novas entidades

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Selecione **entidades** na navegação à esquerda. 

3. Selecione **Criar nova entidade**.

4. Na janela pop-up, insira `NewEmployee` como um **simples** entidade.

5. Selecione **Criar nova entidade**.

6. Na janela pop-up, insira `NewEmployeeRelocation` como um **simples** entidade.

7. Selecione **NewEmployeeRelocation** na lista de entidades. 

8. Digite a primeira função como `NewEmployeeReloOrigin` e selecione enter.

9. Digite a segunda função como `NewEmployeeReloDestination` e selecione enter.

## <a name="create-new-intent"></a>Criar nova tentativa
Rotular as entidades nessas etapas pode ser mais fácil se a entidade keyPhrase pré-construída for removida antes de começar e depois incluída novamente depois que você concluir as etapas desta seção. 

1. Selecione **Intenções** no painel de navegação esquerdo.

2. Selecione **Criar nova intenção**. 

3. Insira `NewEmployeeRelocationProcess` como o nome intencional na caixa de diálogo pop-up.

4. Insira as seguintes declarações de exemplo, rotulação novas entidades. Os valores de entidade e função estão em negrito. Lembre-se de mudar para o **Tokens View** se achar mais fácil rotular o texto. 

    Você não especifica o papel da entidade ao rotular a intenção. Você faz isso mais tarde ao criar o padrão. 

    |Enunciado|NewEmployee|NewEmployeeRelocation|
    |--|--|--|
    |Mover **Bob Jones** partir **Seattle** para **Los Colinas**|Bob Jones|Seattle, Los Colinas|
    |Mover **Dave C. Cooper** partir **Redmond** para **cidade de Nova York**|Dave C. Cooper|Cidade de Redmond, Nova York|
    |Mover **Jim Paul Smith** partir **Toronto** para **Vancouver Oeste**|Jim Paul Smith|Toronto, Vancouver Oeste|
    |Mover **J. Mello** partir **Boston** para **Staines após Tâmisa**|J. Benson|Boston, Staines após Tâmisa|
    |Mover **Travis "Trav" Hinton** partir **Castelo Branco** para **Orlando**|Travis "Trav" Hinton|Castelo Branco, Orlando|
    |Mover **Trevor Nottington III** partir **Aranda de Duero** para **Boise**|Trevor Nottington III|Aranda de Duero, Boise|
    |Mover **Dr. Greg Williams** partir **Orlando** para **Ellicott cidade**|Dr. Greg Williams|Orlando, cidade de Ellicott|
    |Mover **Robert "Bobby" Gregson** de **Cidade de Kansas** para **San Juan Capistrano**|Robert "Bobby" Gregson|Kansas City, San Juan Capistrano|
    |Mover **Patti Owens** partir **Bellevue** para **Rockford**|Patti Owens|Bellevue, Rockford|
    |Mover **Janet Bartlet** partir **Tuscan** para **Santa fé**|Janet Bartlet|Tuscan, Santa fé|

    O nome do funcionário tem uma variedade de prefixo, contagem de palavras, sintaxe e sufixo. Isso é importante para o LUIS entender as variações de um novo nome de funcionário. Os nomes das cidades também têm uma variedade de palavras e sintaxe. Essa variedade é importante para ensinar ao LUIS como essas entidades podem aparecer no enunciado do usuário. 
    
    Se uma das entidades tivesse a mesma contagem de palavras e nenhuma outra variação, você ensinaria a LUIS que essa entidade tem apenas a contagem de palavras e nenhuma outra variação. LUIS não seria capaz de prever corretamente um conjunto mais amplo de variações porque não foi mostrado nenhum. 

    Se você removeu a entidade keyPhrase, adicione-a novamente ao aplicativo agora.

## <a name="train"></a>Treinar

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publicar

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Obter a intenção e as entidades do ponto de extremidade

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. Vá até o final da URL no endereço e insira `Move Wayne Berry from Miami to Mount Vernon`. O último parâmetro de querystring é `q`, o enunciado **consulta**. 

    ```json
    {
      "query": "Move Wayne Berry from Newark to Columbus",
      "topScoringIntent": {
        "intent": "NewEmployeeRelocationProcess",
        "score": 0.514479756
      },
      "intents": [
        {
          "intent": "NewEmployeeRelocationProcess",
          "score": 0.514479756
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.017118983
        },
        {
          "intent": "MoveEmployee",
          "score": 0.009982505
        },
        {
          "intent": "GetJobInformation",
          "score": 0.008637771
        },
        {
          "intent": "ApplyForJob",
          "score": 0.007115978
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.006120186
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00452428637
        },
        {
          "intent": "None",
          "score": 0.00400899537
        },
        {
          "intent": "OrgChart-Reports",
          "score": 0.00240071164
        },
        {
          "intent": "Utilities.Help",
          "score": 0.001770991
        },
        {
          "intent": "EmployeeFeedback",
          "score": 0.001697356
        },
        {
          "intent": "OrgChart-Manager",
          "score": 0.00168116146
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00163952739
        },
        {
          "intent": "FindForm",
          "score": 0.00112958835
        }
      ],
      "entities": [
        {
          "entity": "wayne berry",
          "type": "NewEmployee",
          "startIndex": 5,
          "endIndex": 15,
          "score": 0.629158735
        },
        {
          "entity": "newark",
          "type": "NewEmployeeRelocation",
          "startIndex": 22,
          "endIndex": 27,
          "score": 0.638941
        }
      ]
    }  
    ```

A pontuação de predição de intenção é apenas cerca de 50%. Se o seu aplicativo cliente requer um número maior, isso precisa ser corrigido. As entidades não estavam previstos qualquer um.

Um dos locais foi extraído, mas o outro não. 

Os padrões ajudarão a pontuação de previsão, no entanto, as entidades devem ser corretamente previstas antes que o padrão corresponda à expressão. 

## <a name="pattern-with-roles"></a>Padrão com funções

1. Selecione **Build** no painel de navegação superior.

2. Selecione **padrões** no painel de navegação à esquerda.

3. Selecione **NewEmployeeRelocationProcess** da **selecionar uma intenção** lista suspensa. 

4. Insira o seguinte padrão: `move {NewEmployee} from {NewEmployeeRelocation:NewEmployeeReloOrigin} to {NewEmployeeRelocation:NewEmployeeReloDestination}[.]`

    Se você treina, publica e consulta o endpoint, pode ficar desapontado ao ver que as entidades não são encontradas, portanto, o padrão não corresponde, portanto, a previsão não melhorou. Essa é uma consequência de não haver exemplos de declarações suficientes com entidades rotuladas. Em vez de adicionar mais exemplos, adicione uma lista de frases para corrigir esse problema.

## <a name="cities-phrase-list"></a>Lista de expressões de cidades
As cidades, como os nomes das pessoas, são complicadas, pois podem ser qualquer mistura de palavras e pontuação. As cidades da região e do mundo são conhecidas, de modo que o LUIS precisa de uma lista de expressões de cidades para começar a aprender. 

1. Selecione **lista de frases** da **melhorar o desempenho do aplicativo** seção do menu à esquerda. 

2. A lista de nomes `Cities` e adicione o seguinte `values` para obter a lista:

    |Valores da lista de frases|
    |--|
    |Seattle|
    |San Diego|
    |Cidade de Nova York|
    |Los Angeles|
    |Portland|
    |Philadephia|
    |Miami|
    |Dallas|

    Não adicione todas as cidades do mundo ou mesmo todas as cidades da região. LUIS precisa ser capaz de generalizar o que uma cidade é da lista. Certifique-se de manter **esses valores são intercambiáveis** selecionado. Essa configuração significa que as palavras na lista em tratado como sinônimos. 

3. Treine e publique o aplicativo.

## <a name="get-intent-and-entities-from-endpoint"></a>Obter a intenção e as entidades do ponto de extremidade

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Vá até o final da URL no endereço e insira `Move wayne berry from miami to mount vernon`. O último parâmetro de querystring é `q`, o enunciado **consulta**. 

    ```json
    {
      "query": "Move Wayne Berry from Miami to Mount Vernon",
      "topScoringIntent": {
        "intent": "NewEmployeeRelocationProcess",
        "score": 0.9999999
      },
      "intents": [
        {
          "intent": "NewEmployeeRelocationProcess",
          "score": 0.9999999
        },
        {
          "intent": "Utilities.Confirm",
          "score": 1.49678385E-06
        },
        {
          "intent": "MoveEmployee",
          "score": 8.240291E-07
        },
        {
          "intent": "GetJobInformation",
          "score": 6.3131273E-07
        },
        {
          "intent": "None",
          "score": 4.25E-09
        },
        {
          "intent": "OrgChart-Manager",
          "score": 2.8E-09
        },
        {
          "intent": "OrgChart-Reports",
          "score": 2.8E-09
        },
        {
          "intent": "EmployeeFeedback",
          "score": 1.64E-09
        },
        {
          "intent": "Utilities.StartOver",
          "score": 1.64E-09
        },
        {
          "intent": "Utilities.Help",
          "score": 1.48181822E-09
        },
        {
          "intent": "Utilities.Stop",
          "score": 1.48181822E-09
        },
        {
          "intent": "Utilities.Cancel",
          "score": 1.35E-09
        },
        {
          "intent": "FindForm",
          "score": 1.23846156E-09
        },
        {
          "intent": "ApplyForJob",
          "score": 5.692308E-10
        }
      ],
      "entities": [
        {
          "entity": "wayne berry",
          "type": "builtin.keyPhrase",
          "startIndex": 5,
          "endIndex": 15
        },
        {
          "entity": "miami",
          "type": "builtin.keyPhrase",
          "startIndex": 22,
          "endIndex": 26
        },
        {
          "entity": "wayne berry",
          "type": "NewEmployee",
          "startIndex": 5,
          "endIndex": 15,
          "score": 0.9410646,
          "role": ""
        },
        {
          "entity": "miami",
          "type": "NewEmployeeRelocation",
          "startIndex": 22,
          "endIndex": 26,
          "score": 0.9853915,
          "role": "NewEmployeeReloOrigin"
        },
        {
          "entity": "mount vernon",
          "type": "NewEmployeeRelocation",
          "startIndex": 31,
          "endIndex": 42,
          "score": 0.986044347,
          "role": "NewEmployeeReloDestination"
        }
      ],
      "sentimentAnalysis": {
        "label": "neutral",
        "score": 0.5
      }
   }
    ```

A pontuação da intenção agora é muito maior e os nomes das funções são parte da resposta da entidade.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Este tutorial adicionou uma entidade com funções e uma intenção com enunciados de exemplo. A primeira previsão de ponto de extremidade que usa a entidade previu corretamente a intenção, mas com uma baixa pontuação de confiança. Apenas uma das duas entidades foi detectada. Em seguida, o tutorial adicionou um padrão que usava as funções de entidade e uma lista de expressões para aumentar o valor dos nomes de cidades nos enunciados. A segunda previsão de ponto de extremidade retornou uma pontuação de alta confiança e encontrou as duas funções de entidade. 

> [!div class="nextstepaction"]
> [Conheça as práticas recomendadas para aplicativos do LUIS](luis-concept-best-practices.md)
