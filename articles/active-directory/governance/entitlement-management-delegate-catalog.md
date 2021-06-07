---
title: Delegar governança de acesso para criadores de catálogo no gerenciamento de direitos do Azure AD-Azure Active Directory
description: Saiba como delegar a governança de acesso dos administradores de ti aos criadores de catálogo e aos gerentes de projeto para que eles possam gerenciar o acesso.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: e41f372cd0105896765d5a267456b6bda767dd4d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87798419"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management"></a>Delegar governança de acesso para criadores de catálogo no gerenciamento de direitos do Azure AD

Um catálogo é um contêiner de recursos e pacotes de acesso. Você cria um catálogo quando deseja agrupar recursos relacionados e pacotes de acesso. Por padrão, um administrador global ou um administrador de usuário pode [criar um catálogo](entitlement-management-catalog-create.md)e pode adicionar outros usuários como proprietários de catálogo.

Para delegar a usuários que não são administradores, para que eles possam criar seus próprios catálogos, você pode adicionar esses usuários à função de criador de catálogo definida pelo gerenciamento de direitos do Azure AD. Você pode adicionar usuários individuais ou pode adicionar um grupo, cujos membros são então capazes de criar catálogos.  Depois de criar um catálogo, eles podem posteriormente adicionar recursos que eles possuem ao catálogo.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>Como um administrador de ti, delegado a um criador de catálogo

Siga estas etapas para atribuir um usuário à função de criador de catálogo.

**Função de pré-requisito:** Administrador global ou administrador de usuário

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, na seção **Gerenciamento de direitos** , clique em **configurações**.

1. Clique em **Editar**.

    ![Configurações para adicionar criadores de catálogo](./media/entitlement-management-delegate-catalog/settings-delegate.png)

1. Na seção **Gerenciamento de direitos de representante** , clique em **Adicionar criadores de catálogo** para selecionar os usuários ou grupos aos quais você deseja delegar essa função de gerenciamento de direitos.

1. Clique em **Selecionar**.

1. Clique em **Salvar**.

## <a name="allow-delegated-roles-to-access-the-azure-portal"></a>Permitir que funções delegadas acessem o portal do Azure

Para permitir que funções delegadas, como criadores de catálogo e gerenciadores de pacotes de acesso, acessem o portal do Azure para gerenciar pacotes de acesso, você deve verificar a configuração do portal de administração.

**Função de pré-requisito:** Administrador global ou administrador de usuário

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **usuários**.

1. No menu à esquerda, clique em **configurações do usuário**.

1. Certifique-se de que **restringir o acesso ao portal de administração do Azure ad** esteja definido como **não**.

    ![Configurações de usuário do Azure AD-portal de administração](./media/entitlement-management-delegate-catalog/user-settings.png)

## <a name="next-steps"></a>Próximas etapas

- [Criar e gerenciar um catálogo de recursos](entitlement-management-catalog-create.md)
- [Delegar governança de acesso para acessar gerenciadores de pacotes](entitlement-management-delegate-managers.md)

