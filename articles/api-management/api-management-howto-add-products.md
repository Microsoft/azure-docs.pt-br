---
title: Tutorial – Criar e publicar um produto no Gerenciamento de API do Azure
description: Neste tutorial, você cria e publica um produto no Gerenciamento de API do Azure. Depois que ele é publicado, os desenvolvedores podem começar a usar as APIs do produto.
author: mikebudzynski
ms.service: api-management
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: apimpm
ms.openlocfilehash: 2f298f240d8aa7a38b42a8c78ee3c90fe3423d10
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993543"
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
+ Conclua o início rápido a seguir: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).
+ Além disso, conclua o seguinte tutorial: [Importar e publicar sua primeira API](import-and-publish.md).

## <a name="create-and-publish-a-product"></a>Criar e publicar um produto

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


1. No painel de navegação esquerdo da sua instância do Gerenciamento de API, selecione **Produtos**.
1. Selecione um produto e escolha **APIs**.
1. Selecione **+ Adicionar**.
1. Selecione uma ou mais APIs e escolha **Selecionar**.

:::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-02.png" alt-text="Adicionar API a um produto existente":::

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
