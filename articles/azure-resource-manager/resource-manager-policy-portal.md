---
title: "Portal do Azure para políticas de recurso | Microsoft Docs"
description: "Descreve como usar o portal do Azure para criar e gerenciar políticas do Resource Manager. As políticas podem ser aplicadas em grupos de recursos ou de assinatura."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: tomfitz
ms.openlocfilehash: 868b2cc1559053057d17b34c03e2e31347f399bf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-portal-to-assign-and-manage-resource-policies"></a>Usar o portal do Azure para atribuir e gerenciar políticas de recurso
O portal do Azure permite que você atribua políticas de recurso a grupos de recursos e assinaturas. A interface do usuário facilita a seleção da política que você deseja atribuir e a especificação de valores de parâmetros para essa política para personalizar as configurações de política. 

Para atribuir uma política por meio do portal, a definição de política já deve existir em sua assinatura. Sua assinatura tem várias definições de políticas internas que estão prontas para serem atribuídas a grupos de recursos ou assinaturas. Você vê essas políticas internas e as políticas personalizadas definidas ao usar o portal para atribuir políticas. Para obter uma introdução às políticas e como definir uma política personalizada, consulte [Visão geral da política de recurso](resource-manager-policy.md).

As políticas são herdadas por todos os recursos filho. Então, se uma política for aplicada a um grupo de recursos, ela será aplicável a todos os recursos desse grupo de recursos. Neste artigo, o termo **escopo** refere-se ao grupo de recursos ou à assinatura que foi atribuído à política. 

Políticas são avaliadas durante a criação e atualização de recursos (PUT e operações de PATCHES).

## <a name="assign-a-policy"></a>Atribuir uma política

1. Para atribuir uma política a um grupo de recursos ou a uma assinatura, selecione uma assinatura ou um grupo de recursos específico. Nas configurações, selecione **Políticas**.

   ![selecionar políticas](./media/resource-manager-policy-portal/select-policies.png)

2. Para criar uma atribuição de política para esse escopo, selecione **Adicionar atribuição**.

   ![adicionar atribuição](./media/resource-manager-policy-portal/add-assignment.png)

3. Selecione a política que você deseja atribuir. Mesmo que você não tenha adicionado nenhuma definição de política à sua assinatura, você vê as políticas internas disponíveis para atribuição. Essas políticas internas abrangem vários cenários comuns.

   ![selecionar definição](./media/resource-manager-policy-portal/select-definition.png)

4. Depois de selecionar uma política, você verá uma descrição da política e seus parâmetros. Por exemplo, a imagem a seguir mostra o parâmetro **Localizações permitidas**, que é necessário para a política que restringe as localizações disponíveis.

   ![mostrar parâmetros](./media/resource-manager-policy-portal/show-parameters.png)

5. Por meio da interface do usuário, selecione os valores a serem especificados para os parâmetros da política (por exemplo, as localizações que podem ser usadas para implantação).

   ![selecionar valores do parâmetro](./media/resource-manager-policy-portal/select-parameters.png)

6. Forneça valores para os outros parâmetros. O escopo é atribuído automaticamente de acordo com a folha selecionada ao iniciar a atribuição de política. Selecione **OK** quando tiver concluído.

   ![definir parâmetros](./media/resource-manager-policy-portal/define-parameters.png)

  Você atribuiu a política ao escopo especificado.

## <a name="view-policy-assignments"></a>Exibir atribuições de política

Depois de atribuir uma política, você a verá na lista de políticas desse escopo. A guia **Detalhes** mostra um resumo da atribuição de política.

![mostrar detalhes](./media/resource-manager-policy-portal/show-details.png)

A guia **Regra de atribuição** mostra o JSON para a definição de política.

![mostrar regra de atribuição](./media/resource-manager-policy-portal/show-assignment-rule.png)

## <a name="change-an-existing-policy-assignment"></a>Alterar uma atribuição de política existente

Para alterar uma política, selecione **Editar atribuição** ou **Excluir**

![editar ou excluir atribuição](./media/resource-manager-policy-portal/edit-delete-policy.png)

## <a name="assign-custom-policies"></a>Atribuir políticas personalizadas

Se você tiver definido políticas personalizadas na assinatura, essas políticas estarão disponíveis para atribuição por meio do portal. Essas políticas são precedidas por **[Personalizada]**

![políticas personalizadas](./media/resource-manager-policy-portal/show-custom-policy.png)

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre a sintaxe JSON para definição de políticas, consulte [Visão geral da política de recurso](resource-manager-policy.md).
* Para obter orientação sobre como as empresas podem usar o Resource Manager para gerenciar assinaturas de forma eficaz, consulte [Azure enterprise scaffold – controle de assinatura prescritivas](resource-manager-subscription-governance.md).
* O esquema da política é publicado em [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json). 

