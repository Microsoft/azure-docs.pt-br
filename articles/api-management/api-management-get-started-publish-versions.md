---
title: Tutorial – Publicar versões da sua API usando o Gerenciamento de API do Azure
description: Execute as etapas deste tutorial para saber como publicar várias versões de API no Gerenciamento de API.
author: vladvino
ms.service: api-management
ms.custom: mvc, devx-track-azurecli
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: apimpm
ms.openlocfilehash: 5930979258372e4269c4d453ffbc5e0d46258088
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483597"
---
# <a name="tutorial-publish-multiple-versions-of-your-api"></a>Tutorial: Publicar várias versões da sua API 

Algumas vezes, é impraticável fazer com que todos os chamadores de sua API usem exatamente a mesma versão. Quando os chamadores desejam atualizar para uma versão mais recente, eles querem uma abordagem fácil de entender. Conforme mostrado neste tutorial, é possível fornecer várias *versões* no Gerenciamento de API do Azure. 

Para obter informações, confira [Versões e revisões](https://azure.microsoft.com/blog/versions-revisions/).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Adicionar uma nova versão a uma API existente
> * Escolher um esquema de versão
> * Adicionar a versão a um produto
> * Procurar a versão no portal do desenvolvedor

:::image type="content" source="media/api-management-getstarted-publish-versions/azure-portal.png" alt-text="Versão mostrada no portal do Azure":::

## <a name="prerequisites"></a>Prerequisites

+ Conheça a [terminologia do Gerenciamento de API do Azure](api-management-terminology.md).
+ Conclua o seguinte guia de início rápido: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).
+ Além disso, conclua o seguinte tutorial: [Importar e publicar sua primeira API](import-and-publish.md).

## <a name="add-a-new-version"></a>Adicionar uma nova versão

1. No [portal do Azure](https://portal.azure.com), navegue até a instância do Gerenciamento de API.
1. Selecione **APIs**.
1. Selecione **Demonstração de API de Conferência** na lista de APIs. 
1. Selecione o menu de contexto ( **...** ) ao lado de **API de Conferência de Demonstração**.
1. Selecione **Adicionar versão**.

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version-menu.png" alt-text="Menu de contexto da API – adicionar versão":::


> [!TIP]
> As versões também podem ser habilitadas quando você cria uma API. Na tela **Adicionar API**, selecione **Versão desta API?** .

## <a name="choose-a-versioning-scheme"></a>Escolher um esquema de controle de versão

No Gerenciamento de API do Azure, escolha como os chamadores especificam a versão da API selecionando um *esquema de controle de versão*: **caminho, cabeçalho** ou **cadeia de caracteres de consulta**. No exemplo a seguir, o *caminho* é usado como o esquema de controle de versão.

Insira os valores da tabela a seguir. Depois, selecione **Criar** para criar sua versão.

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version.png" alt-text="Janela Adicionar versão":::



|Configuração   |Valor  |Descrição  |
|---------|---------|---------|
|**Nome**     |  *demo-conference-api-v1*       |  Nome exclusivo em sua instância do Gerenciamento de API.<br/><br/>Como uma versão é, na verdade, uma nova API baseada na [revisão](api-management-get-started-revise-api.md) de uma API, essa configuração é o nome da nova API.   |
|**Esquema de controle de versão**     |  **Caminho**       |  A maneira como os chamadores especificam a versão da API.     |
|**Identificador de versão**     |  *v1*       |  Indicador específico do esquema da versão. Para **Caminho**, o sufixo para o caminho da URL da API. <br/><br/> Se **Cabeçalho** ou **Cadeia de caracteres de consulta** estiver selecionado, insira um valor adicional: o nome do parâmetro de cadeia de caracteres de cabeçalho ou de consulta.<br/><br/> Um exemplo de uso é exibido.        |
|**Produtos**     |  **Ilimitado**       |  Opcionalmente, um ou mais produtos aos quais a versão da API está associada. Para publicar a API, você deve associá-la a um produto. Você também pode [adicionar a versão a um produto](#add-the-version-to-a-product) mais tarde.      |

Depois de criar a versão, ela aparecerá abaixo de **API de Conferência de Demonstração** na lista de APIs. Agora você verá duas APIs: **Original** e **v1**.

![Versões listadas em uma API no portal do Azure](media/api-management-getstarted-publish-versions/version-list.png)

Agora você pode editar e configurar **v1** como uma API separada da **Original**. As alterações em uma versão não afetam a outra.

> [!Note]
> Se você adicionar uma versão a uma API sem controle de versão, uma **Original** também será criada automaticamente. Esta versão responde na URL padrão. A criação de uma versão Original garante que os chamadores existentes não sejam interrompidos pelo processo de adição de uma versão. Se você criar uma API com versões habilitadas no início, uma Original não será criada.

## <a name="add-the-version-to-a-product"></a>Adicionar a versão a um produto

Para os chamadores verem a nova versão, ela deve ser adicionada a um *produto*. Se ainda não adicionou a versão a um produto, você pode adicioná-la a qualquer momento.

Por exemplo, para adicionar a versão ao produto **Ilimitado**:
1. No portal do Azure, navegue até a instância do Gerenciamento de API.
1. Selecione **Produtos** > **Ilimitado** > **APIs** >  **+ Adicionar**.
1. Selecione **API de Conferência de Demonstração**, versão **v1**.
1. Clique em **Selecionar**.

:::image type="content" source="media/api-management-getstarted-publish-versions/08-add-multiple-versions-03-add-version-product.png" alt-text="Adicionar versão ao produto":::

## <a name="use-version-sets"></a>Usar conjuntos de versões

Quando você cria várias versões, o portal do Azure cria um *conjunto de versões*, que representa um conjunto de versões de uma API lógica individual. Selecione o nome de uma API que tenha várias versões. O portal do Azure exibirá o **conjunto de versões** dela. Você pode personalizar o **Nome** e a **Descrição** de um conjunto virtual.

Interaja diretamente com os conjuntos de versões usando a CLI do Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Para ver todos os conjuntos de versões, execute o comando [az apim api versionset list](/cli/azure/apim/api/versionset#az_apim_api_versionset_list):

```azurecli
az apim api versionset list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

Quando o portal do Azure cria um conjunto de versões para você, ele atribui um nome alfanumérico, que é exibido na coluna **Nome** da lista. Use esse nome em outros comandos da CLI do Azure.

Para ver detalhes sobre um conjunto de versões, execute o comando [az apim api versionset show](/cli/azure/apim/api/versionset#az_apim_api_versionset_show):

```azurecli
az apim api versionset show --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --version-set-id 00000000000000000000000
```

Para obter mais informações sobre os conjuntos de versões, confira [Versões no Gerenciamento de API do Azure](api-management-versions.md#how-versions-are-represented).

## <a name="browse-the-developer-portal-to-see-the-version"></a>Procurar a versão no portal do desenvolvedor

Se você experimentou o [portal do desenvolvedor](api-management-howto-developer-portal-customize.md), poderá ver as versões da API nele.

1. Selecione **Portal do Desenvolvedor** no menu superior.
2. Selecione **APIs** e, depois, selecione **Demonstração de API de Conferência**.
3. Você deverá ver uma lista suspensa com várias versões ao lado do nome da API.
4. Selecione **v1**.
5. Observe a **URL Se solicitação** da primeira operação na lista. Ela mostra que o caminho da URL da API inclui **v1**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Adicionar uma nova versão a uma API existente
> * Escolher um esquema de versão 
> * Adicionar a versão a um produto
> * Procurar a versão no portal do desenvolvedor

Prosseguir para o próximo tutorial:

> [!div class="nextstepaction"]
> [Personalizar o estilo das páginas do Portal do desenvolvedor](api-management-howto-developer-portal-customize.md)
