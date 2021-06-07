---
title: 'Início Rápido: Implantar um aplicativo usando o portal do LUIS'
description: Este início rápido mostra como implantar um aplicativo criando um recurso de ponto de extremidade de previsão, atribuindo o recurso, treinando e publicando o aplicativo.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/29/2021
ms.openlocfilehash: f7d5d473b8fe24d8dd5018a718ea96f286b1c030
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106275831"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Início Rápido: Implantar um aplicativo no portal do LUIS

Depois que o aplicativo estiver pronto para retornar previsões de enunciado para um aplicativo cliente, como um chat bot, você precisará implantar o aplicativo no ponto de extremidade de previsão.

Neste início rápido, você aprenderá a implantar um aplicativo. Crie um recurso de ponto de extremidade de previsão, atribua o recurso ao aplicativo, treine e publique o aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

* Obtenha uma [assinatura do Azure](https://azure.microsoft.com/free).
* Conclua o [início rápido do portal anterior](get-started-portal-build-app.md) ou [baixe e importe o aplicativo](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/portal-build-app.json).
* Se você tiver aplicativos anteriores à autenticação de recursos do Azure, [migre para um recurso do Azure](luis-migration-authoring.md). Algumas páginas do portal parecem diferentes quando a autenticação de email está em vigor.

<a name="create-the-endpoint-resource"></a>

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Atribuir a chave de recurso para o aplicativo LUIS no portal do LUIS

Sempre que você criar um recurso de previsão de criação ou consulta para LUIS, será necessário atribuir o recurso para o aplicativo LUIS. Após a atribuição, você não precisará realizar esta etapa novamente, a menos que crie um novo recurso. Você pode criar um novo recurso para expandir as regiões do seu aplicativo ou para dar suporte a um número maior de consultas de previsão.

1. Entre no [portal do LUIS](https://www.luis.ai) e escolha o aplicativo **myEnglishApp** na lista de aplicativos.

1. Selecione **Gerenciar** no menu superior direito e, em seguida, selecione **Recursos do Azure**.

1. Para adicionar o LUIS, selecione **Adicionar recurso de previsão**.

    ![Para adicionar o recurso de previsão do LUIS, selecione Adicionar recurso de previsão](./media/get-started-portal-deploy-app/azure-resources-add-prediction-resource.png)

1. Selecione o locatário, a assinatura e o nome do recurso. Selecione **Atribuir recurso**.

   > [!div class="mx-imgBorder"]
   > ![Atribuir um recurso ao seu aplicativo](./media/get-started-portal-deploy-app/assign-resource.png)

1. Conclua as mesmas etapas para adicionar a chave de criação ao seu aplicativo.

1. Localize a nova linha na tabela do novo recurso de previsão e copie a URL do ponto de extremidade. Ela é construída corretamente para criar uma solicitação `HTTP GET` para o ponto de extremidade da API do LUIS para uma previsão.

> [!TIP]
> Se pretender usar o aprendizado ativo para aprimorar seu aplicativo LUIS, selecione **Alterar parâmetros de consulta** e selecione **Salvar logs**. Essa ação altera a URL de exemplo adicionando o parâmetro querystring `log=true`. Copie e use a URL de consulta de exemplo alterada ao fazer consultas de previsão para o ponto de extremidade do runtime.

## <a name="train-the-app"></a>Treinar o aplicativo

Se você alterou o aplicativo desde que o treinou pela última vez, [treine](get-started-portal-build-app.md) o aplicativo.

## <a name="publish-the-app-to-the-prediction-endpoint"></a>Publicar o aplicativo no ponto de extremidade de previsão

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="prediction-endpoint-request"></a>Solicitação de ponto de extremidade de previsão

No portal, `query=` no final da URL é onde o enunciado do usuário é anexado à solicitação GET. Após `query=`, insira a mesma expressão de usuário usado no final do guia de início rápido anterior:

```Is there a form named hrf-234098```

Verifique se a cadeia de caracteres de consulta inclui os seguintes pares:

* `show-all-intents=true`
* `verbose=true`

O navegador mostra a resposta:

```JSON
{
    "query": "Is there a form named hrf-234098",
    "prediction": {
        "topIntent": "FindForm",
        "intents": {
            "FindForm": {
                "score": 0.9768753
            },
            "None": {
                "score": 0.0216071177
            }
        },
        "entities": {
            "Human Resources Form Number": [
                "hrf-234098"
            ],
            "$instance": {
                "Human Resources Form Number": [
                    {
                        "type": "Human Resources Form Number",
                        "text": "hrf-234098",
                        "startIndex": 22,
                        "length": 10,
                        "modelTypeId": 8,
                        "modelType": "Regex Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

Para ver este mesmo nível de informações no painel de teste, publique o aplicativo. Depois que o aplicativo for publicado, selecione **Comparar ao publicado** no painel de teste. Use **Mostrar exibição JSON** no painel de teste publicado para ver o mesmo JSON da etapa anterior. Dessa forma, você poderá comparar as alterações no aplicativo atual em que está trabalhando com um aplicativo publicado no ponto de extremidade.

[![Compare editando no momento versus a versão publicada do aplicativo](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você concluir este guia de início rápido, selecione **Meus aplicativos** no menu de navegação superior. Marque a caixa de seleção do aplicativo na lista e selecione **Excluir** na barra de ferramentas de contexto acima da lista.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Desenvolvimento de aplicativo iterativo para LUIS](./luis-concept-app-iteration.md)