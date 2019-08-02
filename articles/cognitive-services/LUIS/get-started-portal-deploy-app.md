---
title: 'Início Rápido: Implantar um aplicativo usando o portal do LUIS'
titleSuffix: Azure Cognitive Services
description: Saiba como implantar seu aplicativo LUIS no ponto de extremidade de previsão depois que ele estiver pronto para devolver previsões de enunciado a um aplicativo cliente, como um chatbot. Este início rápido mostra o passo a passo da implantação de um aplicativo ao criar um recurso de ponto de extremidade de previsão, atribuindo-o ao aplicativo, treinando e publicando o aplicativo.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 5c310c1943eaf23423be873c6172e27c621fe109
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564065"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Início Rápido: Implantar um aplicativo no portal do LUIS

Depois que o aplicativo estiver pronto para retornar previsões de enunciado para um aplicativo cliente, como um chat bot, você precisará implantar o aplicativo no ponto de extremidade de previsão.

Neste início rápido, você aprenderá a implantar um aplicativo. Crie um recurso de ponto de extremidade de previsão, atribua o recurso ao aplicativo, treine e publique o aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

* Obtenha uma [assinatura do Azure](https://azure.microsoft.com/free).
* Conclua o [início rápido do portal anterior](get-started-portal-build-app.md) ou [baixe e importe o aplicativo](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).

## <a name="create-the-endpoint-resource"></a>Criar o recurso de ponto de extremidade

Você cria o recurso de ponto de extremidade de previsão no portal do Azure. Esse recurso deve ser usado somente para consultas de previsão de ponto de extremidade. Não use esse recurso para criação de alterações para o aplicativo.

1. Entre no [Portal do Azure](https://ms.portal.azure.com/).

1. Selecione o sinal **+** verde no painel superior esquerdo. Pesquise `Cognitive Services` no marketplace e selecione-o.

1. Configure a assinatura com as seguintes definições:

   |Configuração|Valor|Finalidade|
   |--|--|--|
   |NOME|`my-cognitive-service-resource`|O nome do recurso do Azure. Você precisa desse nome ao atribuir o recurso ao aplicativo no portal do LUIS.|
   |Subscription|Sua assinatura|Selecione uma das assinaturas associadas à sua conta.|
   |Location|**Oeste dos EUA**|A região do Azure para esse recurso.|
   |Tipo de preço|**S0**|O padrão de camada de preços para esse recurso.|
   |Resource group|`my-cognitive-service-resource-group`|Crie um novo grupo de recursos para todos os recursos de serviço cognitivo. Quando você terminar os recursos, poderá excluir o grupo de recursos para limpar sua assinatura. |
   | | | |

   ![Opção de API do Azure](./media/get-started-portal-deploy-app/create-cognitive-services-resource.png)

1. Selecione **Criar** para criar o recurso do Azure.

   Na próxima seção, você aprenderá como conectar esse novo recurso a um aplicativo do LUIS no portal do LUIS.

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Atribuir a chave de recurso para o aplicativo LUIS no portal do LUIS

Sempre que você criar um novo recurso para LUIS, será necessário atribuir o recurso para o aplicativo LUIS. Após a atribuição, você não precisará realizar esta etapa novamente, a menos que crie um novo recurso. Você pode criar um novo recurso para expandir as regiões do seu aplicativo ou para dar suporte a um número maior de consultas de previsão.

1. Entre no [portal do LUIS](https://www.luis.ai) e escolha o aplicativo **myEnglishApp** na lista de aplicativos.

1. Selecione **Gerenciar** no menu superior direito e, em seguida, selecione **Chaves e pontos de extremidade**.

1. Para adicionar o LUIS, selecione **Atribuir Recurso +** .

   [![Atribuir um recurso ao seu aplicativo](./media/get-started-portal-deploy-app/assign-resource-button.png)](./media/get-started-portal-deploy-app/assign-resource-button.png#lightbox)

1. Selecione o locatário, a assinatura e o nome do recurso. Selecione **Atribuir recurso**.

   ![Atribuir um recurso ao seu aplicativo](./media/get-started-portal-deploy-app/assign-resource.png)

1. Localize a nova linha na tabela e copie a URL de ponto de extremidade. Ela é construída corretamente para criar uma solicitação `HTTP GET` para o ponto de extremidade da API do LUIS para uma previsão.

## <a name="train-and-publish-the-app"></a>Treinar e publicar o aplicativo

Treine o aplicativo quando estiver pronto para testá-lo. Publique o aplicativo sempre que quiser que a versão atualmente treinada esteja disponível para aplicativos cliente provenientes do tempo de execução de ponto de extremidade de previsão.

1. Se o aplicativo não estiver treinado, selecione **Treinar** no menu do canto superior direito.

1. Selecione **Publicar** no menu superior. Aceite as configurações de ambiente padrão e, em seguida, selecione **Publicar**.

1. Quando a barra de notificação de êxito verde aparecer na parte superior da janela do navegador, selecione **Consultar a lista dos pontos de extremidade**.

   ![Barra de notificação do aplicativo publicado com êxito no navegador](./media/get-started-portal-deploy-app/successfully-published-notification.png)

1. Na página **Configurações de chaves e de ponto de extremidade**, localize a lista de recursos atribuídos e as URLs de ponto de extremidade correspondentes na parte inferior.

1. Selecione a URL do ponto de extremidade associada ao seu novo nome de recurso. Esta ação abre um navegador da Web com uma URL corretamente construída para fazer uma solicitação `GET` ao tempo de execução do ponto de extremidade de previsão.

1. O `q=` no final da URL é a abreviação de **consulta** e é onde a expressão do usuário é anexada à solicitação GET. Após `q=`, insira a mesma expressão de usuário usado no final do guia de início rápido anterior:

    ```Is there a form named hrf-234098```

    O navegador mostra a resposta, que é o mesmo JSON que o seu aplicativo cliente receberá:

    ```JSON
    {
    "query": "Is there a form named hrf-234098",
    "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9768753
    },
    "intents": [
        {
        "intent": "FindForm",
        "score": 0.9768753
        },
        {
        "intent": "None",
        "score": 0.0216071066
        }
    ],
    "entities": [
        {
        "entity": "hrf-234098",
        "type": "Human Resources Form Number",
        "startIndex": 22,
        "endIndex": 31
        }
      ]
    }
    ```

    Essa resposta fornece mais informações do que o painel de teste padrão do tutorial anterior. Para ver este mesmo nível de informações no painel de teste, publique o aplicativo. Depois que o aplicativo for publicado, selecione **Comparar ao publicado** no painel de teste. Use **Mostrar exibição JSON** no painel de teste publicado para ver o mesmo JSON da etapa anterior. Dessa forma, você pode comparar o aplicativo atual, que está trabalhando com um aplicativo que é publicado no ponto de extremidade.

    [![Compare editando no momento versus a versão publicada do aplicativo](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Limpar recursos

Quando você concluir este guia de início rápido, selecione **Meus aplicativos** no menu de navegação superior. Marque a caixa de seleção do aplicativo na lista e selecione **Excluir** na barra de ferramentas de contexto acima da lista.

[![Excluir aplicativo da lista Meus aplicativos](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Identificar intenções e entidades comuns](luis-tutorial-prebuilt-intents-entities.md)
