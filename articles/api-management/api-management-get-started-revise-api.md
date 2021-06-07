---
title: Tutorial – Usar revisões para fazer alterações sem interrupções no Gerenciamento de API com segurança
titleSuffix: Azure API Management
description: Execute as etapas deste tutorial para aprender a fazer alterações sem interrupção usando revisões no Gerenciamento de API.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.custom: mvc, devx-track-azurecli
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: 1d99d6f876e4896bb4321afb8dc4d8e7c3a404e7
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483547"
---
# <a name="tutorial-use-revisions-to-make-non-breaking-api-changes-safely"></a>Tutorial: Usar revisões para fazer alterações sem interrupções na API com segurança
Quando sua API estiver pronta e começar a ser usada por desenvolvedores, e algum momento será necessário fazer alterações nessa API e, ao mesmo tempo, não interromper os chamadores de sua API. Também é útil permitir que os desenvolvedores saibam sobre as alterações feitas por você. 

No Gerenciamento de API do Azure, use *revisões* para fazer alterações sem interrupção na API a fim de modelar e testar as alterações com segurança. Quando estiver pronto, você pode tornar uma revisão vigente e substituir a API atual. 

Para saber mais, confira [Versões e revisões](https://azure.microsoft.com/blog/versions-revisions/) e [Controle de versão de API com o Gerenciamento de API do Azure](https://azure.microsoft.com/blog/api-versioning-with-azure-api-management/).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Adicionar uma nova revisão
> * Fazer alterações sem interrupções em sua revisão
> * Torne sua revisão a atual e adicione uma entrada ao log de alterações
> * Procurar no portal do desenvolvedor para ver as alterações e o log de alterações

:::image type="content" source="media/api-management-getstarted-revise-api/azure-portal.png" alt-text="Revisões de API no portal do Azure":::

## <a name="prerequisites"></a>Pré-requisitos

+ Conheça a [terminologia do Gerenciamento de API do Azure](api-management-terminology.md).
+ Conclua o seguinte guia de início rápido: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).
+ Além disso, conclua o seguinte tutorial: [Importar e publicar sua primeira API](import-and-publish.md).

## <a name="add-a-new-revision"></a>Adicionar uma nova revisão

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até a sua instância do Gerenciamento de API.
1. Selecione **APIs**.
2. Selecione **Demonstração da API de Conferência** na lista de APIs (ou outra API à qual você deseja adicionar revisões).
3. Selecione a guia **Revisões**.
4. Selecione **+ Adicionar revisão**.

   :::image type="content" source="media/api-management-getstarted-revise-api/07-add-revisions-01-add-new-revision.png" alt-text="Adicionar revisão de API":::

    > [!TIP]
    > Também é possível selecionar **Adicionar revisão** no menu de contexto ( **...** ) da API.

5. Forneça uma descrição para a nova revisão, para ajudar você a se lembrar de sua função.
6. Selecione **Criar**.
7. Sua nova revisão foi criada.

    > [!NOTE]
    > Sua API original permanece na **Revisão 1**. Essa é a revisão que seus usuários continuarão a chamar até que você opte por tornar uma revisão diferente a atual.

## <a name="make-non-breaking-changes-to-your-revision"></a>Fazer alterações sem interrupções em sua revisão

1. Selecione **Demonstração de API de Conferência** na lista de APIs.
1. Selecione a guia **Design** na parte superior da tela.
1. Observe que o **seletor de revisão** (diretamente acima da guia de design) mostra a revisão atual como **Revisão 2**.

    > [!TIP]
    > Use o seletor de revisão para alternar entre as revisões nas quais você deseja trabalhar.
1. Selecione **+ Adicionar Operação**.
1. Defina a nova operação como **POST** e o Nome, Nome de Exibição e URL da operação como **teste**.
1. **Salve** sua nova operação.

   :::image type="content" source="media/api-management-getstarted-revise-api/07-add-revisions-02-make-changes.png" alt-text="Modificar revisão":::
1. Agora você fez uma alteração em **Revisão 2**. Use o **seletor de revisão** na parte superior da página para alternar novamente para **Revisão 1**.
1. Observe que a nova operação não aparece em **Revisão 1**. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>Torne sua revisão a atual e adicione uma entrada ao log de alterações

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecione a guia **Revisões** no menu próximo à parte superior da página.
1. Abra o menu de contexto (**...**) para a **Revisão 2**.
1. Selecione **Tornar atual**.
1. Marque **Postar no log de alterações públicas para esta API** se quiser postar observações sobre essa alteração. Forneça uma descrição para a alteração que os desenvolvedores vejam, por exemplo: **Teste de revisões. Adicionada uma nova operação de "teste".**
1. Agora, a **Revisão 2** é a atual.

    :::image type="content" source="media/api-management-getstarted-revise-api/revisions-menu.png" alt-text="Menu de revisão na janela &quot;Revisões&quot;":::

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para começar a usar a CLI do Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Use este procedimento para criar e atualizar uma versão.

1. Execute o comando [az apim api list](/cli/azure/apim/api#az_apim_api_list) para ver as IDs da API:

   ```azurecli
   az apim api list --resource-group apim-hello-word-resource-group \
       --service-name apim-hello-world --output table
   ```

   A ID da API a ser usada no próximo comando é o valor `Name`. A revisão da API está na coluna `ApiRevision`.

1. Para criar a versão, com uma nota sobre a versão, execute o comando [az apim api release create](/cli/azure/apim/api/release#az_apim_api_release_create):

   ```azurecli
   az apim api release create --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --api-revision 2 --service-name apim-hello-world \
       --notes 'Testing revisions. Added new "test" operation.'
   ```

   A revisão liberada se torna a revisão atual.

1. Para ver as versões, use o comando [az apim api release list](/cli/azure/apim/api/release#az_apim_api_release_list):

   ```azurecli
   az apim api release list --resource-group apim-hello-word-resource-group \
       --api-id echo-api --service-name apim-hello-world --output table
   ```

   As notas especificadas são exibidas no log de mudanças. Você poderá vê-las na saída do comando anterior.

1. Quando você cria uma versão, o parâmetro `--notes` é opcional. Você pode adicionar ou alterar as notas posteriormente usando o comando [az apim api release update](/cli/azure/apim/api/release#az_apim_api_release_update):

   ```azurecli
   az apim api release update --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --release-id 00000000000000000000000000000000 \
       --service-name apim-hello-world --notes "Revised notes."
   ```

   Use o valor da coluna `Name` para a ID da versão.

Remova qualquer versão executando o comando [az apim api release delete ](/cli/azure/apim/api/release#az_apim_api_release_delete):

```azurecli
az apim api release delete --resource-group apim-hello-word-resource-group \
    --api-id demo-conference-api --release-id 00000000000000000000000000000000 
    --service-name apim-hello-world
```

---

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>Procurar no portal do desenvolvedor para ver as alterações e o log de alterações

Se você tiver tentado o [portal do desenvolvedor](api-management-howto-developer-portal-customize.md), poderá revisar as alterações da API e o log de alterações lá.

1. No portal do Azure, selecione **APIs**.
1. Selecione **Portal do desenvolvedor** no menu superior.
1. No portal do desenvolvedor, selecione **APIs** e, depois, selecione **Demonstração da API de Conferência**.
1. Observe que sua nova operação de **teste** já está disponível.
1. Clique em **Log de alterações** ao lado do nome da API.
1. Observe que a entrada no log de alterações aparece nessa lista.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Adicionar uma nova revisão
> * Fazer alterações sem interrupções em sua revisão
> * Torne sua revisão a atual e adicione uma entrada ao log de alterações
> * Procurar no portal do desenvolvedor para ver as alterações e o log de alterações

Prosseguir para o próximo tutorial:

> [!div class="nextstepaction"]
> [Publicar várias versões da sua API](api-management-get-started-publish-versions.md)
