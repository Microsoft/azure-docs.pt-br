---
title: Definir fluxos de trabalho de moderação com o console da API REST-Content Moderator
titleSuffix: Azure Cognitive Services
description: Você pode usar as APIs de revisão de Content Moderator do Azure para definir fluxos de trabalho e limites personalizados com base em suas políticas de conteúdo.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: 71b7be74ca7b6ac072dfd7c9fa6b8efa72361dfa
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561197"
---
# <a name="define-and-use-moderation-workflows-rest"></a>Definir e usar os fluxos de trabalho de moderação (REST)

Os fluxos de trabalho são filtros personalizados baseados em nuvem que você pode usar para lidar com o conteúdo com mais eficiência. Os fluxos de trabalho podem se conectar a uma variedade de serviços para filtrar conteúdo de maneiras diferentes e, em seguida, executar a ação apropriada. Este guia mostra como usar as APIs REST do fluxo de trabalho, por meio do console de API, para criar e usar fluxos de trabalho. Depois de entender a estrutura das APIs, você pode facilmente portar essas chamadas para qualquer plataforma compatível com REST.

## <a name="prerequisites"></a>Pré-requisitos

- Entre ou crie uma conta no site da [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) de Content Moderator.

## <a name="create-a-workflow"></a>Criar um fluxo de trabalho

Para criar ou atualizar um fluxo de trabalho, vá para a página **[fluxo de trabalho – criar ou atualizar](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** referência de API e selecione o botão para a região de chave (você pode encontrá-lo na URL do ponto de extremidade na página **credenciais** da [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/)). Isso inicia o console de API, no qual você pode facilmente construir e executar chamadas à API REST.

![Seleção de região da página Fluxo de trabalho - Criar ou atualizar](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Inserir parâmetros de chamada REST

Insira valores para **Team**, **workflowname**e **OCP-APIM-Subscription-Key**:

- **team**: A ID da equipe que você criou quando configurou sua conta de [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) (encontrada no campo **ID** na tela de credenciais da ferramenta de revisão).
- **workflowname**: O nome de um novo fluxo de trabalho a ser adicionado (ou um nome existente, se você quiser atualizar um fluxo de trabalho existente).
- **Ocp-Apim-Subscription-Key**: Sua chave de Content Moderator. Você pode encontrá-lo na guia **configurações** da [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com).

![Cabeçalhos e parâmetros de consulta do console Fluxo de trabalho - Criar ou atualizar](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>Inserir uma definição de fluxo de trabalho

1. Edite a caixa **corpo da solicitação** para inserir a solicitação JSON com detalhes para **Descrição** e **tipo** ( `Text` `Image` ou).
2. Para **expressão**, copie a expressão JSON do fluxo de trabalho padrão. A cadeia de caracteres JSON final deve ter a seguinte aparência:

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
> Você pode definir expressões simples, complexas e até mesmo aninhadas para seus fluxos de trabalho usando essa API. A documentação [criar ou atualizar o fluxo de trabalho](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) tem exemplos de lógica mais complexa.

### <a name="submit-your-request"></a>Enviar a solicitação
  
Selecione **Enviar**. Se a operação for bem sucedida, o**Status da resposta** será `200 OK` e a caixa **Conteúdo da resposta** exibirá `true`.

### <a name="examine-the-new-workflow"></a>Examinar o novo fluxo de trabalho

Na [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/), selecione **configurações** > **fluxos de trabalho**. Seu novo fluxo de trabalho deve aparecer na lista.

![Revisar lista de ferramentas de fluxos de trabalho](images/workflow-console-new-workflow.PNG)

Selecione a opção **Editar** para seu fluxo de trabalho e vá para a guia **Designer** . Aqui, você pode ver uma representação intuitiva da lógica JSON.

![Guia Designer para um fluxo de trabalho selecionado](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>Obter detalhes do fluxo de trabalho

Para recuperar detalhes sobre um fluxo de trabalho existente, vá para a página de referência do **[fluxo de trabalho-obter](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** API e selecione o botão para sua região (a região em que sua chave é administrada).

![Seleção de região de Fluxo de trabalho - Get](images/test-drive-region.png)

Insira os parâmetros de chamada REST como na seção acima. Certifique-se de que,  desta vez, workflowname seja o nome de um fluxo de trabalho existente.

![Obter cabeçalhos e parâmetros de consulta](images/workflow-get-default.PNG)

Selecione **Enviar**. Se a operação for concluída com sucesso, o status `200 OK`da **resposta** será e a caixa **conteúdo da resposta** exibirá o fluxo de trabalho no formato JSON, como o seguinte:

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