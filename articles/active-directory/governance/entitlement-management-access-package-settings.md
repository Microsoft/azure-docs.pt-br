---
title: Compartilhar link para solicitar um pacote de acesso no gerenciamento de direitos do Azure AD-Azure Active Directory
description: Saiba como compartilhar o link para solicitar um pacote de acesso em Azure Active Directory gerenciamento de direitos.
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
ms.openlocfilehash: 32973d063c36a45eee79db0b4da4e7419cb70013
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87798539"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management"></a>Compartilhar link para solicitar um pacote de acesso no gerenciamento de direitos do Azure AD

A maioria dos usuários em seu diretório pode entrar no portal meu acesso e ver automaticamente uma lista de pacotes de acesso que eles podem solicitar. No entanto, para usuários externos de parceiros comerciais que ainda não estão em seu diretório, você precisará enviar um link que eles possam usar para solicitar um pacote de acesso. 

Desde que o catálogo do pacote de acesso esteja [habilitado para usuários externos](entitlement-management-catalog-create.md) e você tenha uma [política para o diretório do usuário externo](entitlement-management-access-package-request-policy.md), o usuário externo poderá usar o link meu portal de acesso para solicitar o pacote de acesso.

## <a name="share-link-to-request-an-access-package"></a>Compartilhar link para solicitar um pacote de acesso

**Função de pré-requisito:** Administrador global, Administrador de usuário, Proprietário do catálogo ou Gerenciador de pacote de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Na página Visão geral, copie o **link meu portal de acesso**.

    ![Visão geral do pacote de acesso – Link do portal Meus Acessos](./media/entitlement-management-shared/my-access-portal-link.png)

    É importante que você copie todo o link meu portal de acesso ao enviá-lo para um parceiro comercial interno. Isso garante que o parceiro terá acesso ao portal do seu diretório para fazer sua solicitação. O link começa com `myaccess` , inclui uma dica de diretório e termina com uma ID de pacote de acesso.  (Para o governo dos EUA, o domínio no link meu portal de acesso será `myaccess.microsoft.us` .)

    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Envie um email para o parceiro comercial externo. Eles podem compartilhar o link com seus usuários para solicitar o pacote de acesso.

## <a name="next-steps"></a>Próximas etapas

- [Solicitar acesso a um pacote de acesso](entitlement-management-request-access.md)
- [Aprovar ou negar solicitações de acesso](entitlement-management-request-approve.md)