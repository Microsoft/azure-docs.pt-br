---
title: Revisão automática de um pacote de acesso no gerenciamento de direitos do Azure AD
description: Saiba como examinar o acesso do usuário de pacotes de acesso de gerenciamento de direitos em Azure Active Directory revisões de acesso (versão prévia).
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
ms.openlocfilehash: 31c44f2423cdc5c43638fe2515757bcb11a9814c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87798436"
---
# <a name="self-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Revisão automática de um pacote de acesso no gerenciamento de direitos do Azure AD

O gerenciamento de direitos do Azure AD simplifica o modo como as empresas gerenciam o acesso a grupos, aplicativos e sites do SharePoint. Este artigo descreve como um usuário faz uma autorevisão de seus pacotes de acesso atribuídos.

## <a name="open-the-access-review"></a>Abrir a revisão de acesso

Para fazer uma revisão de acesso, você deve primeiro abrir a revisão de acesso. Use o procedimento a seguir para localizar e abrir a revisão de acesso:

1. Você pode receber um email da Microsoft que solicita que você examine o acesso. Localize o email para abrir a revisão de acesso. Aqui está um exemplo de um email solicitando uma revisão do acesso: 
    
    ![Email de revisor de revisão de acesso](./media/entitlement-management-access-reviews-review-access/self-review-reviewer-email.png)

1. Clique no link **examinar acesso** .

1. Você também pode ir diretamente para https://myaccess.microsoft.com para encontrar suas revisões de acesso pendentes se não receber um email.  (Para o governo dos EUA, use `https://myaccess.microsoft.us` em vez disso.)

1. Clique em **revisões de acesso** na barra de navegação à esquerda para ver uma lista de revisões de acesso pendentes atribuídas a você.


1.  Clique na revisão que você gostaria de começar.

## <a name="perform-the-access-review"></a>Executar a revisão de acesso

Depois de abrir a revisão de acesso, você poderá ver seu acesso. Use o procedimento a seguir para fazer a revisão de acesso:

1.  Decida se você ainda precisa de acesso ao pacote de acesso. Por exemplo, o projeto no qual você está trabalhando não está completo, portanto, você ainda precisa de acesso para continuar trabalhando no projeto.

1.  Clique em **Sim** para manter seu acesso ou clique em **não** para remover o acesso.
    >[!NOTE]
    >Se você tiver declarado que não precisa mais de acesso, não será removido do pacote de acesso imediatamente. Você será removido do pacote do Access quando a revisão terminar ou se um administrador parar a revisão.

1.  Se você clicou em **Sim**, talvez seja necessário incluir uma instrução de justificativa na caixa **motivo** .

1.  Clique em **Enviar**.

Você pode retornar à revisão se mudar de ideia e decidir alterar sua resposta antes do final da revisão.

## <a name="next-steps"></a>Próximas etapas

- [Examinar o acesso aos pacotes do Access](entitlement-management-access-reviews-review-access.md) 
