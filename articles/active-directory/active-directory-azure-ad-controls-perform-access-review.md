---
title: "Como examinar o acesso usando as revisões de acesso do Azure AD | Microsoft Docs"
description: "Como examinar o acesso usando as revisões de acesso do Azure AD."
services: active-directory
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: c9115bb5bc3d22c67ea0ac77b8972ae7f919926b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-review-access-with-azure-ad-access-reviews"></a>Como revisar o acesso usando as revisões de acesso do Azure AD

O Azure Active Directory simplifica como as empresas gerenciam o acesso a aplicativos e membros de grupos no Azure AD e outros Serviços Online da Microsoft, com um recurso chamado revisões de acesso.  Você pode ter recebido um email da Microsoft solicitando que você revise o acesso, membros de um grupo ou os usuários com acesso a um aplicativo. 

## <a name="opening-an-access-review"></a>Como abrir uma revisão de acesso

Você pode ver as revisões de acesso pendente clicando no link no email.  Se você não tiver o email, pode localizar as revisões de acesso fazendo o seguinte:

1. Entre no [Painel de acesso do Azure AD](https://myapps.microsoft.com).
2. Clique no ícone de usuário no canto superior direito da página, que exibe a organização padrão e o seu nome. Se houver mais de uma organização listada, selecione a organização que solicitou uma revisão de acesso.
3. Se houver um bloco rotulado **Revisões de acesso** do lado direito da página, clique nele. Caso contrário, se o bloco não estiver visível, não haverá revisões de acesso a executar para essa organização, e nenhuma ação será necessária no momento.

## <a name="filling-out-an-access-review"></a>Preenchendo uma revisão de acesso

Selecione uma revisão de acesso na lista e você verá os nomes de usuários que precisam ser revisados.  Você apenas poderá ver um nome - o seu - se a solicitação era revisar seu próprio acesso.

Para cada linha na lista, você pode decidir se deseja aprovar ou negar o acesso do usuário. Você pode clicar na linha e escolher se deseja aprovar ou negar. (Se não conhece o usuário, você pode responder a isso também.)

O revisor pode exigir que você forneça uma justificativa de aprovação de acesso contínuo ou associação a um grupo.

## <a name="next-steps"></a>Próximas etapas

Observe que o acesso negado do usuário não é removido imediatamente. Ele pode ser removido quando a revisão for concluída ou um administrador parar a revisão. Portanto, se você deseja alterar a resposta e aprovar um usuário negado anteriormente, ou negar um usuário aprovado anteriormente, clique na linha, redefina a resposta e escolha uma nova resposta.  Você pode fazer isso até que a revisão de acesso seja concluída.



