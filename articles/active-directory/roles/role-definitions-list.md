---
title: Listar definições de função do Azure AD – Azure AD
description: Saiba como listar funções personalizadas e internas do Azure.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 03/07/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d37b2988d32c854e4184adee998341ebadcee053
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467788"
---
# <a name="list-azure-ad-role-definitions"></a>Listar definições de função do Azure AD

Uma definição de função é uma coleção de permissões que podem ser executadas, como leitura, gravação e exclusão. Normalmente ela é chamada apenas de função. Azure Active Directory tem mais de 60 funções internas ou você pode criar suas próprias funções personalizadas. Se você já se perguntou "o que essas funções realmente fazem?", você pode ver uma lista detalhada de permissões para cada uma das funções.

Este artigo descreve como listar as funções internas e personalizadas do Azure AD junto com suas permissões.

## <a name="list-all-roles"></a>Listar todas as funções

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com) e selecione **Azure Active Directory**.

1. Selecione **funções e administradores** para ver a lista de todas as funções disponíveis.

    ![lista de funções no portal do Azure AD](./media/role-definitions-list/view-roles-in-azure-active-directory.png)

1. À direita, selecione as reticências e, em seguida, **Descrição** para ver a lista completa de permissões para uma função.

    A página inclui links para a documentação relevante para ajudá-lo a orientá-lo no gerenciamento de funções.

    ![Captura de tela que mostra a página "administrador global-descrição".](./media/role-definitions-list/role-description.png)

## <a name="next-steps"></a>Próximas etapas

* Fique à vontade para compartilhar seus comentários conosco no [fórum de funções administrativas do Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Para obter mais informações sobre funções e a atribuição de função de Administrador, confira [Atribuir funções de administrador](permissions-reference.md).
* Para obter as permissões de usuário padrão, confira uma [comparação entre as permissões de usuário membro e convidado padrão](../fundamentals/users-default-permissions.md).
