---
title: Examine o acesso a grupos ou aplicativos nas revisões de acesso-Azure Active Directory | Microsoft Docs
description: Saiba como revisar seu próprio acesso a grupos ou aplicativos em Azure Active Directory revisões de acesso.
services: active-directory
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/21/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4b48e035476e5381104b442f87dcac03ac11778
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499672"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Examinar o acesso a grupos ou aplicativos nas revisões de acesso do Azure AD

O Azure Active Directory (AD do Azure) simplifica como as empresas gerenciam o acesso a grupos ou aplicativos no Azure AD e outros serviços online da Microsoft com um recurso chamado revisões de acesso do Azure AD.

Este artigo descreve como examinar seu próprio acesso a um grupo ou a um aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

- Azure AD Premium P2

Para obter mais informações, consulte [quais usuários devem ter licenças?](access-reviews-overview.md#which-users-must-have-licenses).

## <a name="open-the-access-review"></a>Abrir a revisão de acesso

A primeira etapa para executar uma revisão de acesso é encontrar e abrir a revisão de acesso.

1. Procure um email da Microsoft que solicita que você examine o acesso. Aqui está um email de exemplo para examinar o acesso a um grupo.

    ![Email de exemplo da Microsoft para revisar o acesso a um grupo](./media/review-your-access/access-review-email.png)

1. Clique no link **examinar acesso** para abrir a revisão de acesso.

Se você não tiver o email, poderá encontrar suas revisões de acesso pendentes seguindo estas etapas.

1. Entre no portal do myapps em [https://myapps.microsoft.com](https://myapps.microsoft.com).

    ![Portal do myapps Listando aplicativos aos quais você tem permissões](./media/review-your-access/myapps-access-panel.png)

1. No canto superior direito da página, clique no símbolo do usuário para exibir seu nome e a organização padrão. Se houver mais de uma organização listada, selecione a organização que solicitou uma análise de acesso.

1. No lado direito da página, clique no bloco **revisões de acesso** para ver uma lista das revisões de acesso pendentes.

    Se o bloco não estiver visível, não haverá revisões de acesso para executar nessa organização e nenhuma ação será necessária no momento.

    ![Lista de revisões de acesso pendente para seus aplicativos e grupos](./media/review-your-access/access-reviews-list.png)

1. Clique no link **Iniciar revisão** para a revisão de acesso que você deseja executar.

## <a name="perform-the-access-review"></a>Executar a revisão de acesso

Depois de abrir a revisão de acesso, você poderá ver seu acesso.

1. Examine seu acesso e decida se você ainda precisa de acesso.

    Se a solicitação for revisar o acesso para outros, a página terá uma aparência diferente. Para obter mais informações, consulte [examinar o acesso a grupos ou aplicativos](perform-access-review.md).

    ![Abra a revisão de acesso perguntando se você ainda precisa de acesso a um grupo](./media/review-your-access/perform-access-review.png)

1. Clique em **Sim** para manter seu acesso ou clique em **não** para remover o acesso.

1. Se você clicar em **Sim**, talvez seja necessário especificar uma justificativa na caixa **motivo** .

    ![Revisão de acesso concluída perguntando se você ainda precisa de acesso a um grupo](./media/review-your-access/perform-access-review-submit.png)

1. Clique em **Enviar**.

    Sua seleção é enviada e você retornou para o portal do myapps.

    Se você quiser alterar sua resposta, abra novamente a página de revisões de acesso e atualize sua resposta. Você pode alterar sua resposta a qualquer momento até que a revisão de acesso seja encerrada.

    > [!NOTE]
    > Se você indicou que não precisa mais de acesso, não será removido imediatamente. Você será removido quando a revisão terminar ou quando um administrador parar a revisão.

## <a name="next-steps"></a>Próximas etapas

- [Concluir uma revisão de acesso de grupos ou aplicativos](complete-access-review.md)
