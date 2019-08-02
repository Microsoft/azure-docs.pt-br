---
title: Entidade simples, lista de frases
titleSuffix: Azure Cognitive Services
description: Neste tutorial, extraia dados de aprendizado de máquina de um nome de cargo de um enunciado usando a entidade Simples. Para aumentar a precisão da extração, adicione uma lista de frases de termos específicos para a entidade simples.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: a03a1224451411617f38c8cecafbef008aa08916
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68276188"
---
# <a name="tutorial-extract-names-with-simple-entity-and-a-phrase-list"></a>Tutorial: Extrair nomes com uma entidade simples e uma lista de frases

Neste tutorial, extraia dados de aprendizado de máquina de um nome de cargo de um enunciado usando a entidade **Simples**. Para aumentar a precisão da extração, adicione uma lista de frases de termos específicos para a entidade simples.

A entidade simples detecta um conceito de dados único contido em palavras ou frases.

**Neste tutorial, você aprenderá a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importar o aplicativo de exemplo
> * Adicionar entidade simples 
> * Adicionar lista de frases para melhorar as palavras de sinal
> * Treinar 
> * Publicar 
> * Obter intenções e entidades do ponto de extremidade

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]


## <a name="simple-entity"></a>Entidade simples

Este tutorial adiciona uma nova entidade simples para extrair o nome do cargo. A finalidade da entidade simples no aplicativo LUIS é ensinar ao LUIS o que é um nome trabalho e onde um enunciado pode ser encontrado. A parte do enunciado que é o nome do cargo pode mudar de acordo com o enunciado e com base no tamanho do enunciado e na escolha de palavras. O LUIS precisa de exemplos de nomes de cargo em todas as intenções que usam nomes de trabalho.  

A entidade simples é uma boa escolha para esse tipo de dados quando:

* Os dados são um conceito único.
* Os dados não foram bem formatados como uma expressão regular.
* Os dados não são comuns como uma entidade predefinida de dados ou número de telefone.
* Os dados não correspondem exatamente à lista de palavras conhecidas como uma entidade de lista.
* Os dados não contêm outros itens de dados, como uma entidade de composição ou funções contextuais.

Considere os seguintes enunciados de um chatbot:

|Enunciado|Nome do trabalho extraível|
|:--|:--|
|Desejo me candidatar para o novo trabalho de contabilidade.|contabilidade|
|Enviar meu currículo para o cargo de engenharia.|engenharia|
|Preencher o aplicativo para o trabalho 123456|123456|

É difícil determinar o nome do trabalho porque um nome pode ser um substantivo, um verbo ou uma frase com várias palavras. Por exemplo:

|Trabalhos|
|--|
|engenheiro|
|engenheiro de software|
|engenheiro de software sênior|
|líder de equipe de engenharia |
|controlador de tráfego aéreo|
|operador de veículo motorizado|
|motorista de ambulância|
|vigia|
|extrusor|
|construtor de moinhos|

Esse aplicativo LUIS tem nomes de trabalho em várias intenções. Ao rotular essas palavras em todos os enunciados de intenções, o LUIS aprende mais sobre o que é um nome de cargo e em que local ele é encontrado em enunciados.

Após as entidades serem marcadas nos enunciados de exemplo, é importante adicionar uma lista de frases para ampliar o sinal da entidade simples. Uma lista de frases **não** é usada como uma correspondência exata e não precisa ser todos os valores possíveis que você espera. 

## <a name="import-example-app"></a>Importar o aplicativo de exemplo

1.  Baixe e salve o [arquivo JSON do aplicativo](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/intentonly.json) do tutorial sobre Intenções.

2. Importe o JSON em um novo aplicativo.

3. Na seção **Gerenciar**, na guia **Versões**, clone a versão e nomeie-a como `simple`. A clonagem é uma ótima maneira de testar vários recursos de LUIS sem afetar a versão original. Como o nome da versão é usado como parte da rota de URL, o nome não pode conter nenhum caractere que não seja válido em uma URL.

## <a name="mark-entities-in-example-utterances-of-an-intent"></a>Marque entidades em enunciados de exemplo de uma intenção

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Na página **Intenções**, selecione a intenção **ApplyForJob**. 

1. No enunciado, `I want to apply for the new accounting job`, selecione `accounting`, digite `Job` no campo superior do menu pop-up, depois selecione **Criar nova entidade** no menu pop-up. 

    [![Captura de tela do LUIS com a intenção “ApplyForJob” com as etapas para criar a entidade realçadas](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "Captura de tela do LUIS com a intenção “ApplyForJob” com as etapas para criar a entidade realçadas")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

1. Na janela pop-up, verifique o nome e o tipo da entidade e selecione **Concluído**.

    ![Criar diálogo modal pop-up da entidade simple com o nome do Job e tipo de simple](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

1. Nos enunciados restantes, marque as palavras relacionadas ao trabalho com a entidade **Trabalho** selecionando a palavra ou frase e, em seguida, selecionando **Trabalho** no menu pop-up. 

    [![Captura de tela da entidade de trabalho de rotulagem do LUIS realçada](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png "Captura de tela da entidade de trabalho de rotulagem do LUIS realçada")](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png#lightbox)


## <a name="add-more-example-utterances-and-mark-entity"></a>Adicionar mais enunciados de exemplo e marcar a entidade

As entidades simples precisam de muitos exemplos para ter uma alta confiança de previsão. 
 
1. Adicione mais enunciados e marque as palavras ou frases de trabalho como entidade **Job**. 

    |Enunciado|Entidade Job|
    |:--|:--|
    |Estou me candidatando para o suporte do Gerente de Programas em P&D|Gerente de Programas|
    |Aqui está minha candidatura para cozinheiro de linha.|cozinheiro de linha|
    |Meu currículo para o monitor de campo está anexado.|monitor de acampamento|
    |Este é o meu CV para assistente administrativo.|assistente administrativo|
    |Desejo me candidatar para o trabalho de gerenciamento em vendas.|gerenciamento, vendas|
    |Este é o meu currículo para o novo cargo de contabilidade.|contabilidade|
    |Minha candidatura para assistente de bar está inclusa.|assistente de bar|
    |Estou enviando minha candidatura para carpinteiro e armador.|carpinteiro, armador|
    |Meu CV para motorista de ônibus está aqui.|motorista de ônibus|
    |Sou enfermeiro registrado. Aqui está o meu currículo.|enfermeiro registrado|
    |Gostaria de enviar minha documentação para o cargo de professor que vi no jornal.|professor|
    |Este é o meu CV para o cargo de estoquista de frutas e legumes.|estoquista|
    |Candidatar-se ao trabalho de azulejista.|bloco|
    |CV anexo para arquiteto paisagista.|arquiteto paisagista|
    |Meu currículo para professor de biologia está anexado.|professor de biologia|
    |Gostaria de me candidatar para o cargo em fotografia.|fotografia|

## <a name="mark-job-entity-in-other-intents"></a>Marcar entidade de trabalho em outras intenções

1. Selecione **Intents** no menu esquerdo.

1. Selecione **GetJobInformation** na lista de intenções. 

1. Rotular os trabalhos em enunciados de exemplo

    Se houver mais enunciados de exemplo em uma intenção do que outra intenção, essa intenção terá uma alta probabilidade de ser a intenção mais alta prevista. 

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Treinar o aplicativo para que as alterações à intenção possam ser testadas 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publicar o aplicativo para que o modelo em que foi feito o treinamento possa ser consultado por meio do ponto de extremidade

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obter a previsão de intenção e de entidade do ponto de extremidade 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Vá até o final da URL no endereço e insira `Here is my c.v. for the engineering job`. O último parâmetro de querystring é `q`, o enunciado **consulta**. Esse enunciado não é igual a nenhum dos enunciados rotulados, portanto, é um bom teste e deve retornar os enunciados `ApplyForJob`.

    ```json
    {
      "query": "Here is my c.v. for the engineering job",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.98052007
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.98052007
        },
        {
          "intent": "GetJobInformation",
          "score": 0.03424581
        },
        {
          "intent": "None",
          "score": 0.0015820954
        }
      ],
      "entities": [
        {
          "entity": "engineering",
          "type": "Job",
          "startIndex": 24,
          "endIndex": 34,
          "score": 0.668959737
        }
      ]
    }
    ```
    
    O LUIS encontrou a intenção correta, **ApplyForJob**, e extraiu a entidade correta, **Job**, com um valor de `engineering`.


## <a name="names-are-tricky"></a>Nomes são complicados
O aplicativo LUIS encontrou a intenção correta com alta confiança e extraiu o nome do trabalho, mas nomes são complicados. Tente usar o enunciado `This is the lead welder paperwork`.  

No JSON a seguir, o LUIS responde com a intenção correta, `ApplyForJob`, mas não extrai o nome de trabalho `lead welder`. 

```json
{
  "query": "This is the lead welder paperwork",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.860295951
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.860295951
    },
    {
      "intent": "GetJobInformation",
      "score": 0.07265678
    },
    {
      "intent": "None",
      "score": 0.00482481951
    }
  ],
  "entities": []
}
```

Como um nome pode ser qualquer coisa, o LUIS prevê as intenções mais precisamente caso tenha uma lista de frase de palavras para melhorar o sinal.

## <a name="to-boost-signal-of-the-job-related-words-add-a-phrase-list-of-job-related-words"></a>Para melhorar o sinal das palavras relacionados ao trabalho, adicione uma lista de frases de palavras relacionados ao trabalho

Abra o [jobs-phrase-list.csv](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/job-phrase-list.csv) do repositório Azure Samples (Exemplos do Azure) do GitHub. A lista tem mais de mil palavras e frases de trabalho. Examine a lista de palavras em busca de palavras de trabalho que sejam significativas para você. Se suas palavras ou frases não estiverem na lista, adicione suas próprias.

1. Na seção **Compilar** do aplicativo LUIS, selecione **Listas de frase**, encontrada no menu **Melhorar o desempenho do aplicativo**.

1. Selecione **Criar nova lista de frase**. 

1. Nomeie a nova lista de frase como `JobNames` e copie a lista de jobs-phrase-list.csv para a caixa de texto **Valores**.

    [![Captura de tela da caixa de diálogo pop-up Criar nova lista de frases](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "Captura de tela da caixa de diálogo pop-up Criar nova lista de frases")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    Se você quiser adicionar mais palavras à lista de frases, selecione **Recommand** e, em seguida, examine os novos **Valores Relacionados**, adicionando os que forem relevantes. 

    Mantenha **Esses valores são intercambiáveis** marcada porque esses valores devem ser tratados como sinônimos para trabalhos. Saiba mais sobre [conceitos de lista de frases](luis-concept-feature.md#how-to-use-phrase-lists) intercambiáveis e não intercambiáveis.

1. Selecione **Concluído** para ativar a lista de frases.

    [![Captura de tela da caixa de diálogo pop-up Criar nova lista de frases com palavras na caixa de valores da lista de frases](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "Captura de tela da caixa de diálogo pop-up Criar nova lista de frases com palavras na caixa de valores da lista de frases")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

1. Treine e publique o aplicativo novamente para usar a lista de frases.

1. Repita a consulta no ponto de extremidade com o mesmo enunciado: `This is the lead welder paperwork.`

    A resposta JSON inclui a entidade extraída:

    ```json
      {
      "query": "This is the lead welder paperwork.",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.983076453
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.983076453
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0120766377
        },
        {
          "intent": "None",
          "score": 0.00248388131
        }
      ],
      "entities": [
        {
          "entity": "lead welder",
          "type": "Job",
          "startIndex": 12,
          "endIndex": 22,
          "score": 0.8373154
        }
      ]
    }
    ```

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* [Tutorial de intenções sem entidades](luis-quickstart-intents-only.md)
* Informações conceituais de [entidade simples](luis-concept-entity-types.md)
* Informações conceituais de [lista de frases](luis-concept-feature.md)
* [Como treinar](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal do LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, o aplicativo de Recursos Humanos usa uma entidade simples de aprendizado de máquina para encontrar nomes de cargo em enunciados. Como os nomes de trabalho podem ser uma ampla variedade de palavras e frases, o aplicativo precisava de uma lista de frases para aumentar as palavras relacionadas a nomes de cargo. 

> [!div class="nextstepaction"]
> [Adicionar uma entidade de keyphrase predefinida](luis-quickstart-intent-and-key-phrase.md)
