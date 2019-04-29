---
title: Definir fluxos de trabalho de moderação com o console de API REST - Content Moderator
titlesuffix: Azure Cognitive Services
description: Você pode usar as APIs de revisão do moderador de conteúdo do Azure para definir limites com base em suas políticas de conteúdo e fluxos de trabalho personalizados.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: e150b1321f2fbd348e737222c752203281503643
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60605860"
---
# <a name="define-and-use-moderation-workflows-rest"></a>Definir e usar fluxos de trabalho de moderação (REST)

Fluxos de trabalho são filtros personalizados baseados em nuvem que você pode usar para manipular o conteúdo com mais eficiência. Fluxos de trabalho podem se conectar a uma variedade de serviços para filtrar o conteúdo de maneiras diferentes e, em seguida, execute a ação apropriada. Este guia mostra como usar o APIs REST, do fluxo de trabalho, por meio do console de API, para criar e usar fluxos de trabalho. Depois de compreender a estrutura das APIs, você poderá transportar facilmente essas chamadas para qualquer plataforma compatível com REST.

## <a name="prerequisites"></a>Pré-requisitos

- Entrar ou criar uma conta no Content Moderator [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) site.

## <a name="create-a-workflow"></a>Criar um fluxo de trabalho

Para criar ou atualizar um fluxo de trabalho, vá para o **[fluxo de trabalho - Criar ou atualizar](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** API página de referência e selecione o botão para a sua região de chave (você pode encontrá-lo na URL do ponto de extremidade no **credenciais**  página do [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/)). Isso inicia o console de API, onde você pode facilmente construir e executar chamadas à API REST.

![Seleção de região da página Fluxo de trabalho - Criar ou atualizar](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Insira os parâmetros da chamada REST

Insira valores para **team**, **workflowname**, e **Ocp-Apim-Subscription-Key**:

- **team**: A ID da equipe que você criou quando você configurar seu [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) conta (encontrada na **Id** campo na tela de credenciais da sua ferramenta de revisão).
- **workflowname**: O nome de um novo fluxo de trabalho para adicionar (ou um nome existente, se você quiser atualizar o fluxo de trabalho existente).
- **Ocp-Apim-Subscription-Key**: Sua chave de Content Moderator. Você pode encontrá-lo na **as configurações** guia da [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com).

![Cabeçalhos e parâmetros de consulta do console Fluxo de trabalho - Criar ou atualizar](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>Insira uma definição de fluxo de trabalho

1. Editar o **corpo da solicitação** caixa para inserir a solicitação JSON com detalhes para **descrição** e **tipo** (tanto `Image` ou `Text`).
2. Para **expressão**, copie o expressão de JSON do fluxo de trabalho do padrão. Sua cadeia de caracteres JSON final deve ter esta aparência:

```json
{
  "Description":"<A description for the Workflow>",
  "Type":"Text",
  "Expression":{
    "Type":"Logic",
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isAdult",
      "Operator":"eq",
      "Value":"true",
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    }
  }
}
```

> [!NOTE]
> Você pode definir expressões simples, complexas e aninhadas, mesmo para seus fluxos de trabalho usando essa API. O [fluxo de trabalho - Criar ou atualizar](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) documentação tem exemplos de uma lógica mais complexa.

### <a name="submit-your-request"></a>Enviar a solicitação
  
Selecione **Enviar**. Se a operação for bem sucedida, o**Status da resposta** será `200 OK` e a caixa **Conteúdo da resposta** exibirá `true`.

### <a name="examine-the-new-workflow"></a>Examinar o novo fluxo de trabalho

No [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/), selecione **configurações** > **fluxos de trabalho**. Seu novo fluxo de trabalho deve aparecer na lista.

![Revisar lista de ferramentas de fluxos de trabalho](images/workflow-console-new-workflow.PNG)

Selecione o **edite** opção para seu fluxo de trabalho e vá para o **Designer** guia. Aqui, você pode ver uma representação intuitiva da lógica do JSON.

![Guia Designer para um fluxo de trabalho selecionado](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>Obter detalhes do fluxo de trabalho

Para recuperar detalhes sobre o fluxo de trabalho existente, vá para o **[fluxo de trabalho - Get](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** página de referência de API e selecione o botão para a sua região (a região na qual a chave é administrada).

![Seleção de região de Fluxo de trabalho - Get](images/test-drive-region.png)

Insira os parâmetros da chamada REST como na seção acima. Certifique-se de que esse tempo, **workflowname** é o nome do fluxo de trabalho existente.

![Obter cabeçalhos e parâmetros de consulta](images/workflow-get-default.PNG)

Selecione **Enviar**. Se a operação for bem-sucedida, o **status de resposta** é `200 OK`e o **conteúdo da resposta** caixa exibe o fluxo de trabalho no formato JSON, semelhante ao seguinte:

```json
{
  "Name":"default",
  "Description":"Default",
  "Type":"Image",
  "Expression":{
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isadult",
      "Operator":"eq",
      "Value":"true",
      "AlternateInput":null,
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "Subteam":null,
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    },
    "Else":null,
    "Type":"Logic"
  }
}
```

## <a name="next-steps"></a>Próximas etapas

- Saiba como usar fluxos de trabalho com [trabalhos moderação de conteúdo](try-review-api-job.md).