---
title: Tutorial – Criar e publicar um produto no Gerenciamento de API do Azure
description: Neste tutorial, você cria e publica um produto no Gerenciamento de API do Azure. Depois que ele é publicado, os desenvolvedores podem começar a usar as APIs do produto.
author: mikebudzynski
ms.service: api-management
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: d0420b92fc94e0a1a9c8a4057f419a57a9909223
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545149"
---
# <a name="tutorial-create-and-publish-a-product"></a>Tutorial: Criar e publicar um produto  

No Gerenciamento de API do Azure, um [*produto*](api-management-terminology.md#term-definitions) contém uma ou mais APIs, bem como uma cota de uso e os termos de uso. Uma vez publicado o produto, os desenvolvedores podem assinar o produto e começar a usar as APIs dele.  

Neste tutorial, você aprende a:

> [!div class="checklist"]
> * Criar e publicar um produto
> * Adicionar uma API ao produto

:::image type="content" source="media/api-management-howto-add-products/added-product.png" alt-text="Produtos do Gerenciamento de API no portal":::


## <a name="prerequisites"></a>Pré-requisitos

+ Conheça a [terminologia do Gerenciamento de API do Azure](api-management-terminology.md).
+ Conclua o seguinte guia de início rápido: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).
+ Além disso, conclua o seguinte tutorial: [Importar e publicar sua primeira API](import-and-publish.md).

## <a name="create-and-publish-a-product"></a>Criar e publicar um produto

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Entre no portal do Azure e navegue até sua instância do Gerenciamento de API.
1. No painel de navegação esquerdo, selecione **Produtos** >  **+ Adicionar**.
1.  Na janela **Adicionar produto**, insira os valores descritos na tabela a seguir para criar seu produto.

    :::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-01.png" alt-text="Adicionar produto no portal":::

    | Nome                     | Descrição                                                                                                                                                                                                                                                                                                             |
    |--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Nome de exibição             | O nome que você deseja que seja mostrado no [portal do desenvolvedor](api-management-howto-developer-portal.md).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Descrição              | Forneça informações sobre o produto, como sua finalidade, as APIs a que ele fornece acesso, entre outros detalhes.                                                                                                                                               |
    | Estado                    | Selecione **Publicado** se você deseja publicar o produto. Antes que as APIs de um produto possam ser chamadas, o produto precisa ser publicado. Por padrão, novos produtos não são publicados e ficam visíveis somente para o grupo **Administradores**.                                                                                      |
    | Exige assinatura    | Selecione se um usuário precisa assinar para usar o produto.                                                                                                                                                                                                                                   |
    | Requer aprovação        | Selecione se desejar que um administrador analise e aceite ou rejeite as tentativas de assinatura para o produto. Se não for selecionado, as tentativas de assinatura serão aprovadas automaticamente.                                                                                                                         |
    | Limite de contagem de assinaturas | Opcionalmente, limite a contagem de várias assinaturas simultâneas.                                                                                                                                                                                                                                |
    | Termos legais              | Você pode incluir os termos de uso para o produto que os assinantes devem aceitar a fim de usá-lo.                                                                                                                                                                                                             |
    | APIs                     | Selecione uma ou mais APIs. Você também pode adicionar APIs após criar o produto. Para obter mais informações, confira [Adicionar APIs a um produto](#add-apis-to-a-product) mais adiante neste artigo. |

3. Selecione **Criar** para criar o produto.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para começar a usar a CLI do Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Para criar um produto, execute o comando [az apim product create](/cli/azure/apim/product#az_apim_product_create):

```azurecli
az apim product create --resource-group apim-hello-word-resource-group \
    --product-name "Contoso product" --product-id contoso-product \
    --service-name apim-hello-world --subscription-required true \
    --state published --description "This is a test."
```

Você pode especificar vários valores para o seu produto:

   | Parâmetro | Descrição |
   |-----------|-------------|
   | `--product-name` | O nome que você deseja que seja mostrado no [portal do desenvolvedor](api-management-howto-developer-portal.md). |
   | `--description`  | Forneça informações sobre o produto, como sua finalidade, as APIs a que ele fornece acesso, entre outros detalhes. |
   | `--state`        | Selecione **publicado** se você deseja publicar o produto. Antes que as APIs de um produto possam ser chamadas, o produto precisa ser publicado. Por padrão, novos produtos não são publicados e ficam visíveis somente para o grupo **Administradores**. |
   | `--subscription-required` | Selecione se um usuário precisa assinar para usar o produto. |
   | `--approval-required` | Selecione se desejar que um administrador analise e aceite ou rejeite as tentativas de assinatura para o produto. Se não for selecionado, as tentativas de assinatura serão aprovadas automaticamente. |
   | `--subscriptions-limit` | Opcionalmente, limite a contagem de várias assinaturas simultâneas.|
   | `--legal-terms`         | Você pode incluir os termos de uso para o produto que os assinantes devem aceitar a fim de usá-lo. |

Para ver seus produtos atuais, use o comando [az apim product list](/cli/azure/apim/product#az_apim_product_list):

```azurecli
az apim product list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

Você pode excluir um produto usando o comando [az apim product delete](/cli/azure/apim/product#az_apim_product_delete):

```azurecli
az apim product delete --product-id contoso-product \
    --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --delete-subscriptions true
```

---

### <a name="add-more-configurations"></a>Adicionar mais configurações

Continue configurando o produto depois de salvá-lo. Na instância do Gerenciamento de API, selecione o produto na janela **Produtos**. Adicione ou atualize:

|Item   |Descrição  |
|---------|---------|
|Configurações     |    Metadados e estado do produto     |
|APIs     |  APIs associadas ao produto       |
|[Políticas](api-management-howto-policies.md)     |  Políticas aplicadas às APIs do produto      |
|Controle de acesso     |  Visibilidade do produto para desenvolvedores ou convidados       |
|[Assinaturas](api-management-subscriptions.md)    |    Assinantes do produto     |

## <a name="add-apis-to-a-product"></a>Adicionar APIs a um produto

Os produtos são associações de uma ou mais APIs. Você pode incluir várias APIs e oferecê-las aos desenvolvedores por meio do portal do desenvolvedor. Durante a criação do produto, você pode adicionar uma ou mais APIs existentes. Você também pode adicionar uma API ao produto mais tarde, seja na página **Configurações** do produto ou durante a criação de uma API.

Os Desenvolvedores devem primeiro se inscrever em um produto para obter acesso à API. Com a assinatura, eles obtêm uma chave de assinatura que funciona para qualquer API no produto. Se você criou a instância do APIM, já é um administrador e, portanto, está inscrito em cada produto por padrão.

### <a name="add-an-api-to-an-existing-product"></a>Adicionar uma API a um produto existente

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. No painel de navegação esquerdo da sua instância do Gerenciamento de API, selecione **Produtos**.
1. Selecione um produto e escolha **APIs**.
1. Selecione **+ Adicionar**.
1. Selecione uma ou mais APIs e escolha **Selecionar**.

:::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-02.png" alt-text="Adicionar API a um produto existente":::

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Para ver suas APIs gerenciadas, use o comando [az apim api list](/cli/azure/apim/api#az_apim_api_list):

   ```azurecli
   az apim api list --resource-group apim-hello-word-resource-group \
       --service-name apim-hello-world --output table
   ```

1. Para adicionar uma API ao seu produto, execute o comando [az apim product api add](/cli/azure/apim/product/api#az_apim_product_api_add):

   ```azurecli
   az apim product api add --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --product-id contoso-product \
       --service-name apim-hello-world
   ```

1. Verifique a adição usando o comando [az apim product api list](/cli/azure/apim/product/api#az_apim_product_api_list):

   ```azurecli
   az apim product api list --resource-group apim-hello-word-resource-group \
       --product-id contoso-product --service-name apim-hello-world --output table
   ```

Você pode remover uma API de um produto usando o comando [az apim product api delete](/cli/azure/apim/product/api#az_apim_product_api_delete):

```azurecli
az apim product api delete --resource-group apim-hello-word-resource-group \
    --api-id demo-conference-api --product-id contoso-product \
    --service-name apim-hello-world
```

---

> [!TIP]
> Você pode criar ou atualizar a assinatura de um usuário para um produto com chaves de assinatura personalizadas por meio de uma [API REST](/rest/api/apimanagement/2019-12-01/subscription/createorupdate) ou de um comando do PowerShell.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar e publicar um produto
> * Adicionar uma API ao produto

Prosseguir para o próximo tutorial:

> [!div class="nextstepaction"]
> [Criar API em branco e simular respostas de API](mock-api-responses.md)
