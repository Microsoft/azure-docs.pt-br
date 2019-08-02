---
title: O que é QnA Maker?
titleSuffix: Azure Cognitive Services
description: O QnA Maker é um serviço de API baseado em nuvem que aplica inteligência de aprendizado de máquina personalizada a uma pergunta do usuário em linguagem natural para fornecer a melhor resposta.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 04/05/2019
ms.author: diberry
ms.openlocfilehash: bfb6c5b7cc5a4bd1717fdd96f6d232cc269e702d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439584"
---
# <a name="what-is-qna-maker"></a>O que é QnA Maker?

O QnA Maker é um serviço de API baseado em nuvem que cria uma camada de perguntas e respostas em formato de conversação sobre seus dados. 

O QnA Maker permite que você crie uma base de conhecimento (KB) do seu conteúdo semiestruturado, como URLs de perguntas frequentes (FAQ), manuais do produto, documentos de suporte e perguntas e respostas personalizadas. O serviço QnA Maker responde a perguntas em linguagem natural dos seus usuários combinando-o com a melhor resposta possível de QnAs em sua base de dados de Conhecimento.

O [portal da Web](https://qnamaker.ai) fácil de usar permite criar, gerenciar, treinar e publicar seu serviço sem nenhuma experiência do desenvolvedor. Após o serviço ser publicado em um ponto de extremidade, um aplicativo cliente como um chat bot pode gerenciar a conversa com um usuário para receber perguntas e fornecer as respostas. 

![Visão geral](../media/qnamaker-overview-learnabout/overview.png)

## <a name="key-qna-maker-processes"></a>Principais processos do QnA Maker

O QnA Maker fornece dois serviços essenciais para seus dados:

* **Extração**: os dados estruturados de perguntas e respostas são extraídos de [fontes de dados](../Concepts/data-sources-supported.md) estruturadas e semiestruturadas, como perguntas frequentes e manuais de produtos. Essa extração pode ser feita como parte da [criação](https://aka.ms/qnamaker-docs-createkb) da base de dados de conhecimento ou mais tarde, como parte do processo de edição.

* **Correspondente**: depois de sua base de dados de conhecimento ter sido [treinada e testada](https://aka.ms/qnamaker-docs-trainkb), você a [publica](https://aka.ms/qnamaker-docs-publishkb). Isso habilita um ponto de extremidade para sua base de dados de conhecimento do QnA Maker, que você pode usar em seu aplicativo cliente ou bot. Esse ponto de extremidade aceita uma pergunta do usuário e fornece com a melhor resposta na base de dados de conhecimento, em conjunto com uma pontuação de confiança para a correspondência.

```JSON
{
    "answers": [
        {
            "questions": [
                "How do I share a knowledge base with other?"
            ],
            "answer": "Sharing works at the level of a QnA Maker service, i.e. all knowledge bases in the services will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base)how to collaborate on a knowledge base.",
            "score": 70.95,
            "id": 4,
            "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
            "metadata": []
        }
    ]
}

```

## <a name="qna-maker-architecture"></a>Arquitetura do QnA Maker

A arquitetura do QnA Maker consiste nos dois componentes a seguir:

1. **Serviços de gerenciamento do QnA Maker**: a experiência de gerenciamento para uma base de dados de conhecimento do QnA Maker, que inclui a criação inicial, a atualização, o treinamento e a publicação. Essas atividades podem ser feitas por meio do [portal](https://qnamaker.ai) ou das [APIs de gerenciamento](https://go.microsoft.com/fwlink/?linkid=2092179). 

2. **Tempo de execução e dados do QnA Maker**: isso é implantado em sua assinatura do Azure em sua região especificada. O conteúdo da sua base de dados de conhecimento do cliente é armazenado no [Azure Search](https://azure.microsoft.com/services/search/) e o ponto de extremidade é implantado como um [Serviço de aplicativo](https://azure.microsoft.com/services/app-service/). Você também pode optar por implantar um recurso do [Application Insights](https://azure.microsoft.com/services/application-insights/) para análise.

![Arquitetura](../media/qnamaker-overview-learnabout/architecture.png)


## <a name="service-highlights"></a>Destaques do serviço

- Uma experiência completa **sem código** para [criar um bot](../Quickstarts/create-publish-knowledge-base.md#create-a-bot) com o suporte de uma base de dados de conhecimento.
- **Não há limitação para previsões de rede**. Pague para hospedar o serviço e não pelo número de transações. Consulte a [página de preços](https://aka.ms/qnamaker-docs-pricing) para obter mais detalhes.
- **Dimensione conforme necessário**. Escolha os SKUs apropriados dos componentes individuais que atendam ao seu cenário. Veja como [escolher a capacidade](https://aka.ms/qnamaker-docs-capacity) para o seu serviço do QnA Maker.


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um serviço do QnA Maker](../how-to/set-up-qnamaker-service-azure.md)
