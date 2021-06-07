---
title: Alterar as configurações do ciclo de vida de um pacote de acesso no gerenciamento de direitos do Azure AD-Azure Active Directory
description: Saiba como alterar as informações do solicitante & configurações de ciclo de vida para um pacote de acesso no gerenciamento de direitos Azure Active Directory.
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
ms.openlocfilehash: 4fd119238476b842a4dba1efeec86544ca263920
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90980100"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Alterar as configurações do ciclo de vida de um pacote de acesso no gerenciamento de direitos do Azure AD

Como um Gerenciador de pacotes do Access, você pode alterar as configurações do ciclo de vida de um pacote do Access a qualquer momento editando uma política existente. Se você alterar a data de validade de uma política, a data de expiração das solicitações que já estão em um estado de aprovação pendente ou aprovado não será alterada.

Este artigo descreve como alterar as configurações de ciclo de vida de um pacote do Access existente.

## <a name="open-requestor-information"></a>Abrir informações do solicitante
Para garantir que os usuários tenham o acesso certo a um pacote do Access, as perguntas personalizadas podem ser configuradas para solicitar aos usuários que solicitem acesso a determinados pacotes de acesso. As opções de configuração incluem: localização, obrigatório/opcional e formatos de resposta de texto/várias opções. Os solicitantes verão as perguntas quando solicitarem o pacote e os aprovadores veem as respostas às perguntas para ajudá-las a tomar suas decisões. Use as etapas a seguir para configurar as perguntas em um pacote do Access:

## <a name="open-lifecycle-settings"></a>Abrir configurações do ciclo de vida

Para alterar as configurações do ciclo de vida de um pacote do Access, você precisa abrir a política correspondente. Siga estas etapas para abrir as configurações de ciclo de vida de um pacote do Access.

**Função de pré-requisito:** Administrador global, Administrador de usuário, Proprietário do catálogo ou Gerenciador de pacote de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Clique em **políticas** e, em seguida, clique na política que tem as configurações de ciclo de vida que você deseja editar.

    O painel detalhes da política é aberto na parte inferior da página.

    ![Pacote de acesso-painel detalhes da política](./media/entitlement-management-shared/policy-details.png)

1. Clique em **Editar** para editar a política.

    ![Pacote de acesso-editar política](./media/entitlement-management-shared/policy-edit.png)

1. Clique na guia **ciclo de vida** para abrir as configurações do ciclo de vida.

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>Próximas etapas

- [Alterar as configurações de solicitação e aprovação para um pacote de acesso](entitlement-management-access-package-request-policy.md)