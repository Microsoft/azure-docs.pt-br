---
title: Prever intenções
titleSuffix: Azure Cognitive Services
description: Neste tutorial, crie um aplicativo personalizado que preveja a intenção do usuário. Este aplicativo é o tipo mais simples de aplicativo LUIS porque ele não extrai vários elementos de dados do texto do enunciado, como endereços de email ou datas.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 534aa678df69a4ef71296005922fa59bbe7bbcfa
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277572"
---
# <a name="tutorial-build-luis-app-to-determine-user-intentions"></a>Tutorial: Criar aplicativo LUIS para determinar as intenções dos usuários

Neste tutorial, você criará um aplicativo personalizado de RH (Recursos Humanos) que prevê a intenção de um usuário com base no enunciado (texto). 

**Neste tutorial, você aprenderá a:**

> [!div class="checklist"]
> * Criar um novo aplicativo 
> * Criar intenções
> * Adicionar enunciados de exemplo
> * Treinar o aplicativo
> * Publicar aplicativo
> * Obter a intenção do ponto de extremidade


[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="user-intentions-as-intents"></a>Intenções do usuário

A finalidade do aplicativo é determinar a intenção do texto em linguagem natural da conversa: 

`Are there any new positions in the Seattle office?`

Essas intenções são categorizadas em **Intenções**. 

Este aplicativo possui algumas intenções. 

|Intenção|Finalidade|
|--|--|
|ApplyForJob|Determinar se o usuário está se candidatando para um trabalho.|
|GetJobInformation|Determinar se o usuário está procurando informações sobre trabalhos em geral ou sobre um trabalho específico.|
|Nenhum|Determinar se o usuário está perguntando algo que o aplicativo não deve responder. Essa intenção é fornecida como parte da criação do aplicativo e não pode ser excluída. |

## <a name="create-a-new-app"></a>Criar um novo aplicativo

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-job-information"></a>Criar intenção para informações de trabalho

1. Selecione **Criar nova intenção**. Insira o nome da nova intenção `GetJobInformation`. A intenção é prevista quando um usuário deseja informações sobre trabalhos em aberto da empresa. 

    ![Captura de tela da caixa de diálogo de Nova intenção do LUIS (Reconhecimento Vocal)](media/luis-quickstart-intents-only/create-intent.png "Captura de tela da caixa de diálogo de Nova intenção do LUIS (Reconhecimento Vocal)")

1. Selecione **Concluído**.

2. Adicione vários enunciados de exemplo para esta intenção que se espera que o usuário solicite:

    | Exemplo de enunciados|
    |--|
    |Nenhuma nova vaga publicada hoje?|
    |Existe alguma nova vaga no escritório de Seattle?|
    |Há algum trabalho aberto de trabalhador remoto ou de telecomutação para engenheiros?|
    |Há algum trabalho com bancos de dados?|
    |Estou procurando uma situação de colegas de trabalho no escritório de Tampa.|
    |Há algum estágio no escritório de São Francisco?|
    |Há algum trabalho de meio período para as pessoas na faculdade?|
    |Procurando uma nova vaga com responsabilidades de contabilidade|
    |Procurando por um trabalho em Nova York para bilíngues.|
    |Procurando uma nova situação com responsabilidades em contabilidade.|
    |Novas vagas?|
    |Mostre-me todos os trabalhos para os engenheiros que foram adicionados nos últimos dois dias.|
    |Postagens de empregos de hoje?|
    |Quais cargos de contabilidade estão abertas no escritório de Londres?|
    |Quais cargos estão disponíveis para engenheiros seniores?|
    |Onde estão as listagens de vagas|

    [![Captura de tela da inserção de novas declarações para intenção de MyStore](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "Captura de tela da inserção de novas declarações para intenção de MyStore")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

    Fornecendo _enunciados de exemplo_, você está treinando o LUIS sobre quais tipos de enunciados devem ser previstos para essa intenção. 

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]    

## <a name="add-example-utterances-to-the-none-intent"></a>Adicionar enunciados de exemplo à intenção None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-before-testing-or-publishing"></a>Treinar o aplicativo antes do teste ou da publicação

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publicar o aplicativo para consulta do ponto de extremidade

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-from-the-endpoint"></a>Obter previsão de intenção do ponto de extremidade

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Vá até o final da URL na barra de endereços e insira `I'm looking for a job with Natural Language Processing`. O último parâmetro da cadeia de consulta é `q`, a declaração **query**. Esse enunciado não é o mesmo de nenhum dos enunciados de exemplo. Ele é um bom teste e deve retornar a intenção `GetJobInformation` como a intenção com a maior pontuação. 

    ```JSON
    {
      "query": "I'm looking for a job with Natural Language Processing",
      "topScoringIntent": {
        "intent": "GetJobInformation",
        "score": 0.9923871
      },
      "intents": [
        {
          "intent": "GetJobInformation",
          "score": 0.9923871
        },
        {
          "intent": "None",
          "score": 0.007810574
        }
      ],
      "entities": []
    }
    ```

    O parâmetro `verbose=true` querystring significa incluir **todas as intenções** nos resultados da consulta do aplicativo. A matriz de entidades está vazia porque esse aplicativo no momento não tem nenhuma entidade. 

    O resultado em JSON identifica a intenção com a maior pontuação como a propriedade **`topScoringIntent`** . Todas as pontuações estão entre 1 e 0, com a melhor pontuação mais próxima a 1. 

## <a name="create-intent-for-job-applications"></a>Criar intenção para aplicativos de trabalho

Retorne ao portal do LUIS e crie uma intenção para determinar se o enunciado do usuário é sobre a candidatura a um trabalho.

1. Selecione **Compilar** no menu no canto superior direito para retornar  parte superior, com o botão direito menu para retornar à compilação de aplicativos.

1. Selecione **Intenções** no menu à esquerda para obter a lista de intenções.

1. Selecione **Criar nova intenção** e insira o nome `ApplyForJob`. 

    ![Caixa de diálogo LUIS para criar a nova intenção](./media/luis-quickstart-intents-only/create-applyforjob-intent.png)

1. Adicione vários enunciados a essa intenção a qual você espera que um usuário solicite, por exemplo:

    | Exemplo de enunciados|
    |--|
    |Preencher o aplicativo para a vaga 123456|
    |Este é o meu CV para a vaga 654234|
    |Aqui está meu currículo para a postagem de recepcionista meio período.|
    |Estou me candidatando para o trabalho de técnico em arte com esta documentação.|
    |Estou me candidatando para o estágio de faculdade em pesquisa e desenvolvimento em San Diego|
    |Estou solicitando o envio do meu currículo para o cargo temporário na lanchonete.|
    |Estou enviando meu currículo para a nova equipe Autocar em Columbus, OH|
    |Desejo me candidatar para o novo trabalho de contabilidade|
    |A documentação do estágio em contabilidade do trabalho 456789 está aqui|
    |Vaga 567890 e minha papelada|
    |Meus documentos para o estágio de contabilidade em Tulsa estão anexados.|
    |Minha documentação para o cargo de entrega em feriados|
    |Envie meu currículo para o novo trabalho de contabilidade em Seattle|
    |Enviar currículo para o cargo de engenharia|
    |Este é o meu CV para a postagem 234123 em Tampa.|

<!--

    [![Screenshot of entering new utterances for ApplyForJob intent](media/luis-quickstart-intents-only/utterance-applyforjob.png "Screenshot of entering new utterances for ApplyForJob intent")](media/luis-quickstart-intents-only/utterance-applyforjob.png#lightbox)

    The labeled intent is outlined in red because LUIS is currently uncertain the intent is correct. Training the app tells LUIS the utterances are on the correct intent. 

-->

## <a name="train-again"></a>Treinar novamente

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-again"></a>Publicar novamente

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-again"></a>Obter previsão de intenção novamente

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Na nova janela do navegador, digite `Can I submit my resume for job 235986` no final da URL. 

    ```json
    {
      "query": "Can I submit my resume for job 235986",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9634406
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9634406
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0171300638
        },
        {
          "intent": "None",
          "score": 0.00670867041
        }
      ],
      "entities": []
    }
    ```

    Os resultados incluem a nova intenção **ApplyForJob**, bem como as intenções existentes. 

## <a name="client-application-next-steps"></a>Próximas etapas do aplicativo cliente

Depois que o LUIS retornar a resposta JSON, ele terá concluído esta solicitação. O LUIS não fornece respostas para os enunciados do usuário; só identifica qual tipo de informação está sendo solicitado em idioma natural. O acompanhamento de conversação é fornecido pelo aplicativo cliente como um Bot do Azure. 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* [Tipos de entidades](luis-concept-entity-types.md)
* [Como treinar](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal do LUIS](luis-interactive-test.md)
* [Bot do Azure](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>Próximas etapas

Este tutorial criou o aplicativo de RH (Recursos Humanos), criou duas intenções, adicionou enunciados de exemplo a cada intenção, adicionou enunciados de exemplo à intenção Nenhuma, além de treiná-la, publicá-la e testá-la no ponto de extremidade. Essas são as etapas básicas da criação de um modelo LUIS. 

Continuar com este aplicativo, [adicionando uma lista simples de entidades e de frases](luis-quickstart-primary-and-secondary-data.md).

> [!div class="nextstepaction"]
> [Adicionar intenções e entidades predefinidas a este aplicativo](luis-tutorial-prebuilt-intents-entities.md)
