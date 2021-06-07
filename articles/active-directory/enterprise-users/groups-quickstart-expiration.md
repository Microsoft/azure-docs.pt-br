---
title: Início rápido da política de expiração de grupo - Azure AD | Microsoft Docs
description: Expiração de grupos do Microsoft 365 – Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4eeaa98cb19b119e4da2af7bee39b5aebd303485
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063144"
---
# <a name="quickstart-set-microsoft-365-groups-to-expire-in-azure-active-directory"></a>Configurar os grupos do Microsoft 365 a serem expirados no Azure Active Directory

Neste início rápido, você pode definir a política de expiração para os grupos do Microsoft 365. Quando os usuários podem configurar seus próprios grupos, os grupos não utilizados podem multiplicar-se. Uma maneira de gerenciar os grupos não utilizados é definir esses grupos para expirar, para reduzir o trabalho de ter que exclui-los manualmente.

A política de expiração é simples:

- Grupos com atividades do usuário são renovados automaticamente à medida que a expiração se aproxima
- Os proprietários do grupo serão notificados para renovar um grupo prestes a expirar
- Um grupo que não é renovado será excluído
- Um grupo excluído do Microsoft 365 pode ser restaurado em 30 dias, por um proprietário do grupo ou por um administrador do Azure AD

> [!NOTE]
> Agora, os grupos usam a inteligência do Azure AD para renovar automaticamente com base no uso recente. Essa decisão de renovação é baseada na atividade do usuário em grupos nos serviços do Microsoft 365, como Outlook, SharePoint, Teams, Yammer e outros.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisite"></a>Pré-requisito

 A função com privilégios mínimos necessária para configurar a expiração do grupo é Administrador de usuários na organização.

## <a name="turn-on-user-creation-for-groups"></a>Ativar a criação de usuário para grupos

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta Administrador de usuários.

2. Selecione **Grupos** e, em seguida, selecione **Geral**.
  
   ![Página de configurações de grupos de autoatendimento](./media/groups-quickstart-expiration/self-service-settings.png)

3. Defina **Usuários podem criar grupos do Microsoft 365** para **Sim**.

4. Selecione **Salvar** para salvar as configurações de grupos quando você terminar.

## <a name="set-group-expiration"></a>Definir a expiração de grupo

1. Entre no [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory** > **Grupos** > **Expiração** para abrir as configurações de expiração.
  
   ![Página de configurações de expiração para grupo](./media/groups-quickstart-expiration/expiration-settings.png)

2. Defina o intervalo de expiração. Selecione um valor predefinido ou insira um valor personalizado superior a 31 dias. 

3. Forneça um endereço de email para o qual as notificações de expiração e renovação devem ser enviadas quando um grupo não tem nenhum proprietário.

4. Para este guia de início rápido, defina **Habilitar expiração para estes grupos do Microsoft 365** para **Todos**.

5. Selecione **Salvar** para salvar as configurações de expiração quando você terminar.

É isso! Neste guia de início rápido, você definiu com sucesso a política de expiração para os grupos do Microsoft 365 selecionados.

## <a name="clean-up-resources"></a>Limpar os recursos

### <a name="to-remove-the-expiration-policy"></a>Para remover a política de expiração

1. Certifique-se de que você está conectado no [portal do Azure](https://portal.azure.com) com uma conta que seja de Administrador Global para a sua organização do Azure AD.
2. Selecione **Azure Active Directory** > **Grupos** > **Expiração**.
3. Defina **Habilitar expiração para estes grupos do Microsoft 365** para **Nenhum**.

### <a name="to-turn-off-user-creation-for-groups"></a>Para desativar a criação de usuários para grupos

1. Selecione **Azure Active Directory** > **Grupos** > **Geral**. 
2. Defina **Os usuários podem criar grupos do Microsoft 365 nos portais do Azure** para **Não**.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a expiração, incluindo instruções e restrições técnicas do PowerShell, confira o artigo a seguir:

> [!div class="nextstepaction"]
> [Política de expiração do PowerShell](groups-lifecycle.md)
