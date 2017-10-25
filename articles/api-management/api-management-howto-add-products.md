---
title: Como criar e publicar um produto no Gerenciamento de API do Azure
description: Aprenda a criar e publicar produtos no Gerenciamento de API do Azure.
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 31de55cb-9384-490b-a2f2-6dfcf83da764
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 73bf4451ba1b71807e22440beecc73a7e8045c5e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-and-publish-a-product-in-azure-api-management"></a>Como criar e publicar um produto no Gerenciamento de API do Azure
No Gerenciamento de API, um produto contém uma ou mais APIs, bem como uma quota de uso e os termos de uso. Uma vez publicado o produto, os desenvolvedores podem assinar o produto e começar a usar as APIs dele. Este tópico fornece um guia para criar um produto, adicionar uma API e publicá-la para os desenvolvedores.

## <a name="create-product"> </a>Criar um produto
Operações são adicionadas e configuradas em uma API no Portal do editor. Para acessar o portal do editor, clique em **Portal do editor** no Portal do Azure para acessar o serviço Gerenciamento de API.

![Portal do editor][api-management-management-console]

> Se ainda não criou uma instância de serviço de Gerenciamento de API, confira [Criar uma instância de serviço de Gerenciamento de API][Create an API Management service instance] no tutorial [Introdução ao Gerenciamento de API do Azure][Get started with Azure API Management].
> 
> 

Clique em **Produtos** no menu à esquerda para exibir a página **Produtos** e clique em **Adicionar Produto**.

![Produtos][api-management-products]

![Novo produto][api-management-add-new-product]

Insira um nome descritivo para o produto no campo **Nome** e uma descrição do produto no campo **Descrição**.

Produtos de Gerenciamento de API podem ser **Livres** ou **Protegidos**. Produtos protegidos devem ser assinados antes que possam ser usados, enquanto produtos abertos podem ser usados sem uma assinatura. Marque **Exigir assinatura** para criar um produto protegido que requer uma assinatura. Esta é a configuração padrão.

Marque **Requerer aprovação de assinatura** se desejar que um administrador revise e aceite ou rejeite as tentativas de assinatura para o produto. Se a caixa não estiver marcada, as tentativas de assinatura serão aprovadas automaticamente. Para obter mais informações sobre assinaturas, consulte [Exibir os assinantes de um produto][View subscribers to a product].

Para permitir que contas de desenvolvedor assinem o produto várias vezes, marque a caixa de seleção **Permitir várias assinaturas** . Se essa caixa não estiver marcada, cada conta de desenvolvedor poderá assinar o produto uma única vez.

![Várias assinaturas ilimitadas][api-management-unlimited-multiple-subscriptions]

Para limitar a contagem de várias assinaturas simultâneas, marque a caixa de seleção **Limitar o número de assinaturas simultâneas a** e insira o limite de assinaturas. No exemplo a seguir, as assinaturas simultâneas são limitadas a quatro por conta de desenvolvedor.

![Quatro assinaturas][api-management-four-multiple-subscriptions]

Depois que todas as novas opções de produto forem configuradas, clique em **Salvar** para criar o novo produto.

![Produtos][api-management-products-page]

> Por padrão, novos produtos não são publicados e ficam visíveis somente para o grupo **Administradores** .
> 
> 

Para configurar um produto, clique o nome do produto na guia **Produtos** .

## <a name="add-apis"> </a>Adicionar APIs a um produto
A página **Produtos** contém quatro links para configuração: **Resumo**, **Configurações**, **Visibilidade** e **Assinantes**. A guia **Resumo** é onde você pode adicionar APIs e publicar ou cancelar a publicação de um produto.

![Resumo][api-management-new-product-summary]

Antes de publicar o produto, você precisa adicionar uma ou mais APIs. Para fazer isso, clique em **Adicionar API ao produto**.

![Adicionar APIs][api-management-add-apis-to-product]

Selecione as APIs desejadas e clique em **Salvar**.

## <a name="add-description"> </a>Adicionar informações descritivas a um produto
A guia **Configurações** permite que você forneça informações detalhadas sobre o produto, como sua finalidade, as APIs a que fornece acesso, entre outras informações úteis. O conteúdo é voltado aos desenvolvedores que chamarão a API e pode ser escrito em texto sem formatação ou em marcação HTML.

![Configurações do produto][api-management-product-settings]

Marque **Exigir assinatura** para criar um produto protegido que requer uma assinatura ou desmarque a caixa de seleção para criar um produto livre que pode ser chamado sem uma assinatura.

Selecione **Requerer aprovação de assinatura** se desejar aprovar manualmente todas as solicitações de assinatura do produto. Por padrão, todas as assinaturas de produto são aprovadas automaticamente.

Para permitir que contas de desenvolvedor assinem o produto várias vezes, marque a caixa de seleção **Permitir várias assinaturas** e, opcionalmente, especifique um limite. Se essa caixa não estiver marcada, cada conta de desenvolvedor poderá assinar o produto uma única vez.

Outra opção é preencher o campo **Termos de uso** descrevendo os termos de uso do produto, que os assinantes deverão aceitar para usar o produto.

## <a name="publish-product"> </a>Publicar um produto
Antes que as APIs de um produto possam ser chamadas, o produto precisa ser publicado. Na guia **Resumo** do produto, clique em **Publicar** e depois clique em **Sim, publicar** para confirmar. Para fazer com que um produto publicado volte a ser provado, clique em **Cancelar publicação**.

![Publicar produto][api-management-publish-product]

## <a name="make-visible"> </a>Tornar um produto visível para os desenvolvedores
A guia **Visibilidade** permite que você selecione quais funções deseja que possam ver o produto no portal do desenvolvedor e assinar o produto.

![Visibilidade do produto][api-management-product-visiblity]

Para habilitar e desabilitar a visibilidade de um produto para os desenvolvedores de um grupo, marque ou desmarque a caixa de seleção ao lado do grupo e clique em **Salvar**.

> Para obter mais informações, consulte [Como criar e usar grupos para gerenciar contas de desenvolvedor no Gerenciamento de API do Azure][How to create and use groups to manage developer accounts in Azure API Management].
> 
> 

## <a name="view-subscribers"> </a>Ver os assinantes de um produto
A guia **Desenvolvedores** lista os desenvolvedores que assinaram o produto. Os detalhes e configurações de cada desenvolvedor podem ser vistos clicando em seus respectivos nomes. Neste exemplo, nenhum desenvolvedor assinou o produto ainda.

![Desenvolvedores][api-management-developer-list]

## <a name="next-steps"> </a>Próximas etapas
Após as APIs desejadas serem adicionadas e o produto publicado, os desenvolvedores podem assinar o produto e começar a chamar as APIs. Para ver um tutorial que demonstra esses itens, bem como a configuração avançada do produto, consulte [Como criar e definir configurações avançadas no Gerenciamento de API do Azure][How create and configure advanced product settings in Azure API Management].

Para obter mais informações sobre como trabalhar com produtos, consulte o vídeo a seguir.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Using-Products/player]
> 
> 

[Create a product]: #create-product
[Add APIs to a product]: #add-apis
[Add descriptive information to a product]: #add-description
[Publish a product]: #publish-product
[Make a product visible to developers]: #make-visible
[View subscribers to a product]: #view-subscribers
[Next steps]: #next-steps

[api-management-management-console]: ./media/api-management-howto-add-products/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-add-products/api-management-add-product.png
[api-management-add-new-product]: ./media/api-management-howto-add-products/api-management-add-new-product.png
[api-management-unlimited-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-unlimited-multiple-subscriptions.png
[api-management-four-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-four-multiple-subscriptions.png
[api-management-products-page]: ./media/api-management-howto-add-products/api-management-products-page.png
[api-management-new-product-summary]: ./media/api-management-howto-add-products/api-management-new-product-summary.png
[api-management-add-apis-to-product]: ./media/api-management-howto-add-products/api-management-add-apis-to-product.png
[api-management-product-settings]: ./media/api-management-howto-add-products/api-management-product-settings.png
[api-management-publish-product]: ./media/api-management-howto-add-products/api-management-publish-product.png
[api-management-product-visiblity]: ./media/api-management-howto-add-products/api-management-product-visibility.png
[api-management-developer-list]: ./media/api-management-howto-add-products/api-management-developer-list.png



[api-management-products]: ./media/api-management-howto-add-products/api-management-products.png
[api-management-]: ./media/api-management-howto-add-products/
[api-management-]: ./media/api-management-howto-add-products/


[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps
[How to create and use groups to manage developer accounts in Azure API Management]: api-management-howto-create-groups.md
[How create and configure advanced product settings in Azure API Management]: api-management-howto-product-with-rules.md 
