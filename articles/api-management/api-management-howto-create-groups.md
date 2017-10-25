---
title: Gerenciar contas de desenvolvedor usando grupos no Gerenciamento de API do Azure | Microsoft Docs
description: Aprenda a gerenciar contas de desenvolvedores usando grupos em Gerenciamento de API do Azure
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 33660b45-442f-44be-9a4a-1899d7f699b0
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: b4d71cdfbab535b02542fbb26c7555265e5f9c37
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Como criar e usar grupos para gerenciar contas de desenvolvedor no Gerenciamento de API do Azure
No Gerenciamento de API, os grupos são usados para gerenciar a visibilidade dos produtos para desenvolvedores. Os produtos ficam visíveis primeiro para os grupos, e depois os desenvolvedores nesses grupos podem ver e assinar os produtos associados aos grupos. 

O Gerenciamento de API tem os grupos de sistema imutáveis a seguir.

* **Administradores** - os administradores de assinatura do Azure são membros desse grupo. Os administradores gerenciam instâncias de serviço de Gerenciamento de API, criando as APIs, operações e produtos que são usados pelos desenvolvedores.
* **Desenvolvedores** - usuários autenticados do portal do desenvolvedor se enquadram nesse grupo. Os desenvolvedores são clientes que compilam aplicativos usando suas APIs. Os desenvolvedores têm acesso ao portal do desenvolvedor e criam aplicativos que chamam as operações de uma API.
* **Convidados** - os usuários não autenticados no portal do desenvolvedor, tais como potenciais clientes visitando o portal do desenvolvedor de uma instância de Gerenciamento de API, pertencem a esse grupo. Eles podem receber certos acessos somente leitura, como a capacidade de exibir APIs, mas não de chamá-las.

Além desses grupos de sistema, os administradores podem criar grupos personalizados ou [aproveitar grupos externos em locatários do Azure Active Directory][leverage external groups in associated Azure Active Directory tenants]. Grupos personalizados e externos podem ser usados juntamente com grupos de sistema oferecendo visibilidade aos desenvolvedores e acesso a produtos de API. Por exemplo, você poderia criar um grupo personalizado para os desenvolvedores associados a uma organização parceira específica e conceder acesso às APIs de um produto que contém apenas as APIs relevantes. Um usuário pode ser um membro de mais de um grupo.

Este guia mostra como administradores de uma instância do Gerenciamento de API podem adicionar novos grupos e associá-los a produtos e desenvolvedores.

> [!NOTE]
> Além de criar e gerenciar grupos no portal do editor, você pode criar e gerenciar seus grupos usando a entidade [Group](https://msdn.microsoft.com/library/azure/dn776329.aspx) da API REST do Gerenciamento de API.
> 
> 

## <a name="create-group"> </a>Criar um grupo
Para criar um novo grupo, clique no **portal do Editor** no Portal do Azure para seu serviço de Gerenciamento de API. Isso levará você ao portal do editor de Gerenciamento de API.

![Portal do editor][api-management-management-console]

> Se ainda não criou uma instância de serviço de Gerenciamento de API, confira [Criar uma instância de serviço de Gerenciamento de API][Create an API Management service instance] no tutorial [Introdução ao Gerenciamento de API do Azure][Get started with Azure API Management].
> 
> 

Clique em **Grupos** no menu **Gerenciamento de API** à esquerda e depois clique em **Adicionar grupo**.

![Adicione o novo grupo][api-management-add-group]

Insira um nome exclusivo e uma descrição opcional para o grupo e clique em **Salvar**.

![Adicione o novo grupo][api-management-add-group-window]

O novo grupo é exibido na guia de grupos. Para editar o **Nome** ou a **Descrição** do grupo, clique no nome do grupo na lista. Para excluir o grupo, clique em **Excluir**.

![Grupo adicionado][api-management-new-group]

Agora que foi criado, o grupo pode ser associado a produtos e desenvolvedores.

## <a name="associate-group-product"> </a>Associar um grupo a um produto
Para associar um grupo a um produto, clique em **Produtos** no menu **Gerenciamento de API** à esquerda e depois clique no nome do produto desejado.

![Configurar visibilidade][api-management-add-group-to-product]

Selecione a guia **Visibilidade** para adicionar e remover grupos e para ver os grupos atuais do produto. Para adicionar ou remover grupos, marque ou desmarque as caixas de seleção correspondentes aos grupos desejados e clique em **Salvar**.

![Configurar visibilidade][api-management-add-group-to-product-visibility]

> [!NOTE]
> Para adicionar grupos do Active Directory do Azure, consulte [Como autorizar contas de desenvolvedor usando o Active Directory do Azure no Gerenciamento de API do Azure](api-management-howto-aad.md).
> 
> Para configurar grupos a partir da guia **Visibilidade** de um produto, clique em **Gerenciar Grupos**.
> 
> 

Após um produto ser associado a um grupo, os desenvolvedores nesse grupo poderão ver e assinar o produto.

## <a name="associate-group-developer"> </a>Associar grupos a desenvolvedores
Para associar grupos a desenvolvedores, clique em **Usuários** no menu **Gerenciamento de API** à esquerda e marque a caixa de seleção ao lado do desenvolvedor que desejar associar a um grupo.

![Adicione o desenvolvedor a um grupo][api-management-add-group-to-developer]

Após os desenvolvedores desejados serem selecionados, clique no grupo desejado no menu suspenso **Adicionar ao grupo** . Os desenvolvedores podem ser removidos dos grupos usando o menu suspenso **Remover do grupo** . 

![Desenvolvedores][api-management-add-group-to-developer-saved]

Após fazer a associação entre o desenvolvedor e o grupo, você poderá vê-la na guia **Usuários** .

## <a name="next-steps"> </a>Próximas etapas
* Após um desenvolvedor ser associado a um grupo, ele poderá ver e assinar produtos associados ao grupo em questão. Para obter mais informações, consulte [Como criar e publicar um produto no Gerenciamento de API do Azure][How create and publish a product in Azure API Management],
* Além de criar e gerenciar grupos no portal do editor, você pode criar e gerenciar seus grupos usando a entidade [Group](https://msdn.microsoft.com/library/azure/dn776329.aspx) da API REST do Gerenciamento de API.

[api-management-management-console]: ./media/api-management-howto-create-groups/api-management-management-console.png
[api-management-add-group]: ./media/api-management-howto-create-groups/api-management-add-group.png
[api-management-add-group-window]: ./media/api-management-howto-create-groups/api-management-add-group-window.png
[api-management-new-group]: ./media/api-management-howto-create-groups/api-management-new-group.png
[api-management-add-group-to-product]: ./media/api-management-howto-create-groups/api-management-add-group-to-product.png
[api-management-add-group-to-product-visibility]: ./media/api-management-howto-create-groups/api-management-add-group-to-product-visibility.png
[api-management-add-group-to-developer]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer.png
[api-management-add-group-to-developer-saved]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer-saved.png

[api-management-]: ./media/api-management-howto-create-groups/api-management-.png

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md#how-to-add-an-external-azure-active-directory-group
