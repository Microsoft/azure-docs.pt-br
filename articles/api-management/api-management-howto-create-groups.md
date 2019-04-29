---
title: Gerenciar contas de desenvolvedor usando grupos no Gerenciamento de API do Azure | Microsoft Docs
description: Aprenda a gerenciar contas de desenvolvedores usando grupos em Gerenciamento de API do Azure
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 5392cf5463dd0b11d1ce53856c8e4e2e788892b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60658369"
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Como criar e usar grupos para gerenciar contas de desenvolvedor no Gerenciamento de API do Azure

No Gerenciamento de API, os grupos são usados para gerenciar a visibilidade dos produtos para desenvolvedores. Os produtos ficam visíveis primeiro para os grupos, e depois os desenvolvedores nesses grupos podem ver e assinar os produtos associados aos grupos. 

O Gerenciamento de API tem os grupos de sistema imutáveis a seguir:

* **Administradores** - os administradores de assinatura do Azure são membros desse grupo. Os administradores gerenciam instâncias de serviço de Gerenciamento de API, criando as APIs, operações e produtos que são usados pelos desenvolvedores.
* **Desenvolvedores** - usuários autenticados do portal do desenvolvedor se enquadram nesse grupo. Os desenvolvedores são clientes que compilam aplicativos usando suas APIs. Os desenvolvedores têm acesso ao portal do desenvolvedor e criam aplicativos que chamam as operações de uma API.
* **Convidados** - os usuários não autenticados no portal do desenvolvedor, tais como potenciais clientes visitando o portal do desenvolvedor de uma instância de Gerenciamento de API, pertencem a esse grupo. Eles podem receber certos acessos somente leitura, como a capacidade de exibir APIs, mas não de chamá-las.

Além desses grupos de sistema, os administradores podem criar grupos personalizados ou [aproveitar grupos externos em locatários do Azure Active Directory][leverage external groups in associated Azure Active Directory tenants]. Grupos personalizados e externos podem ser usados juntamente com grupos de sistema oferecendo visibilidade aos desenvolvedores e acesso a produtos de API. Por exemplo, você poderia criar um grupo personalizado para os desenvolvedores associados a uma organização parceira específica e conceder acesso às APIs de um produto que contém apenas as APIs relevantes. Um usuário pode ser um membro de mais de um grupo.

Este guia mostra como administradores de uma instância do Gerenciamento de API podem adicionar novos grupos e associá-los a produtos e desenvolvedores.

Além de criar e gerenciar grupos no portal do editor, você pode criar e gerenciar seus grupos usando a entidade [Group](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) da API REST do Gerenciamento de API.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Pré-requisitos

Conclua as tarefas neste artigo: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-group"> </a>Criar um grupo

Esta seção mostra como adicionar um novo grupo à sua conta de Gerenciamento de API.

1. Selecione a guia **Grupos** à esquerda da tela.
2. Clique em **+Adicionar**.
3. Insira um nome exclusivo para o grupo e uma descrição opcional.
4. Pressione **Criar**.

    ![Adicionar um novo grupo](./media/api-management-howto-create-groups/groups001.png)

Quando o grupo for criado, ele será adicionado à lista **Grupos**. <br/>Para editar o **Nome** ou a **Descrição** do grupo, clique no nome do grupo e em **Configurações**.<br/>Para excluir o grupo, clique no nome do grupo e pressione **Excluir**.

Agora que foi criado, o grupo pode ser associado a produtos e desenvolvedores.

## <a name="associate-group-product"> </a>Associar um grupo a um produto

1. Selecione a guia **Produtos** à esquerda.
2. Clique no nome do produto desejado.
3. Pressione **Controle de acesso**.
4. Clique em **+ Adicionar grupo**.

    ![Associar um grupo a um produto](./media/api-management-howto-create-groups/groups002.png)
5. Selecione o grupo que deseja adicionar.

    ![Associar um grupo a um produto](./media/api-management-howto-create-groups/groups003.png)

    Para remover um grupo do produto, clique em **Excluir**.

    ![Excluir um grupo](./media/api-management-howto-create-groups/groups004.png)

Após um produto ser associado a um grupo, os desenvolvedores nesse grupo poderão ver e assinar o produto.

> [!NOTE]
> Para adicionar grupos do Active Directory do Azure, consulte [Como autorizar contas de desenvolvedor usando o Active Directory do Azure no Gerenciamento de API do Azure](api-management-howto-aad.md).

## <a name="associate-group-developer"> </a>Associar grupos a desenvolvedores

Esta seção mostra como associar grupos e membros.

1. Selecione a guia **Grupos** à esquerda da tela.
2. Selecione **Membros**.

    ![Adicionar um membro](./media/api-management-howto-create-groups/groups005.png)
3. Pressione **+Adicionar** e selecione um membro.

    ![Adicionar um membro](./media/api-management-howto-create-groups/groups006.png)
4. Pressione **Selecionar**.

Após fazer a associação entre o desenvolvedor e o grupo, você poderá vê-la na guia **Usuários** .

## <a name="next-steps"> </a>Próximas etapas

* Após um desenvolvedor ser associado a um grupo, ele poderá ver e assinar produtos associados ao grupo em questão. Para obter mais informações, consulte [Como criar e publicar um produto no Gerenciamento de API do Azure][How create and publish a product in Azure API Management],
* Além de criar e gerenciar grupos no portal do editor, você pode criar e gerenciar seus grupos usando a entidade [Group](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) da API REST do Gerenciamento de API.

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md
