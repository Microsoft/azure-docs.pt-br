---
title: Ocultar ou excluir o pacote de acesso no gerenciamento de direitos-Azure AD
description: Saiba como ocultar ou excluir um pacote de acesso no gerenciamento de direitos Azure Active Directory.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22aa97c6e627c2072636ca2e079877ff0f608b68
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87798793"
---
# <a name="hide-or-delete-an-access-package-in-azure-ad-entitlement-management"></a>Ocultar ou excluir um pacote de acesso no gerenciamento de direitos do Azure AD

Os pacotes do Access são detectáveis por padrão. Isso significa que, se uma política permitir que um usuário solicite o pacote de acesso, ele verá automaticamente o pacote de acesso listado em seu portal meu acesso. No entanto, você pode alterar a configuração **oculta** para que o pacote de acesso não esteja listado no meu portal de acesso do usuário.

Este artigo descreve como ocultar ou excluir um pacote de acesso.

## <a name="change-the-hidden-setting"></a>Alterar a configuração oculta

Siga estas etapas para alterar a configuração **oculta** de um pacote de acesso.

**Função de pré-requisito:** Administrador global, Administrador de usuário, Proprietário do catálogo ou Gerenciador de pacote de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Na página Visão geral, clique em **Editar**.

1. Defina a configuração **oculta** .

    Se definido como **não**, o pacote de acesso será listado no meu portal de acesso do usuário.

    Se definido como **Sim**, o pacote de acesso não será listado no meu portal de acesso do usuário. A única maneira de um usuário exibir o pacote de acesso é se eles tiverem o **link direto meu acesso do portal** para o pacote de acesso. Para obter mais informações, consulte [compartilhar link para solicitar um pacote de acesso](entitlement-management-access-package-settings.md).

## <a name="delete-an-access-package"></a>Excluir um pacote de acesso

Um pacote do Access só poderá ser excluído se não tiver nenhuma atribuição de usuário ativa. Siga estas etapas para excluir um pacote de acesso.

**Função de pré-requisito:** Administrador global, Administrador de usuário, Proprietário do catálogo ou Gerenciador de pacote de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. No menu à esquerda, clique em **atribuições** e remova o acesso para todos os usuários.

1. No menu à esquerda, clique em **visão geral** e, em seguida, clique em **excluir**.

1. Na mensagem de exclusão exibida, clique em **Sim**.

## <a name="next-steps"></a>Próximas etapas

- [Exibir, adicionar e remover atribuições para um pacote de acesso](entitlement-management-access-package-assignments.md)
- [Exibir relatórios e logs](entitlement-management-reports.md)
