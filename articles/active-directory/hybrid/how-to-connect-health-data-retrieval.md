---
title: Recuperação de dados de instruções de Azure AD Connect Health | Microsoft Docs
description: Esta página descreve como recuperar dados de Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/02/2020
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d147d2c094923e971e52e1dbfe3f7a19776d38c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89463389"
---
# <a name="azure-ad-connect-health-instructions-for-data-retrieval"></a>Instruções de Azure AD Connect Health para recuperação de dados

Este documento descreve como usar Azure AD Connect para recuperar dados do Azure AD Connect Health.

[!INCLUDE [active-directory-app-provisioning.md](../../../includes/gdpr-intro-sentence.md)]

## <a name="retrieve-all-email-addresses-for-users-configured-for-health-alerts"></a>Recupere todos os endereços de email para usuários configurados para alertas de integridade.

Para recuperar os endereços de email para todos os usuários configurados no Azure AD Connect Health para receber alertas, use as etapas a seguir.

1.  Inicie na folha de integridade Azure Active Directory Connect e selecione **serviços de sincronização** na barra de navegação à esquerda.
 ![Serviços de sincronização](./media/how-to-connect-health-data-retrieval/retrieve1.png)

2.  Clique no bloco **alertas** .</br>
 ![Alerta](./media/how-to-connect-health-data-retrieval/retrieve3.png)

3.  Clique em **configurações de notificação**.
 ![Notificação](./media/how-to-connect-health-data-retrieval/retrieve4.png)

4.  Na folha **configuração de notificação** , você encontrará a lista de endereços de email que foram habilitados como destinatários para notificações de alerta de integridade.
 ![E-mail](./media/how-to-connect-health-data-retrieval/retrieve5a.png)
 
## <a name="retrieve-accounts-that-were-flagged-with-ad-fs-bad-password-attempts"></a>Recuperar contas que foram sinalizadas com AD FS tentativas de senha inadequadas

Para recuperar contas que foram sinalizadas com AD FS tentativas de senha inadequadas, use as etapas a seguir.

1.  A partir da folha Azure Active Directory Health, selecione **erros de sincronização**.
 ![Erros de sincronização](./media/how-to-connect-health-data-retrieval/retrieve6.png)

2.  Na folha **erros de sincronização** , clique em **Exportar**. Isso irá exportar uma lista de erros de sincronização gravados.
 ![Exportar](./media/how-to-connect-health-data-retrieval/retrieve7.png)

## <a name="next-steps"></a>Próximas etapas
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Instalação do Agente do Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Operações de Azure AD Connect Health](how-to-connect-health-operations.md)
* [Perguntas frequentes do Azure AD Connect Health](reference-connect-health-faq.md)
* [Histórico de versão do Azure AD Connect Health](reference-connect-health-version-history.md)