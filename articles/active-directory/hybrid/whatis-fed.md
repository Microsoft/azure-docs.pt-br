---
title: O que é a federação com o Azure AD? | Microsoft Docs
description: Descreve a federação com o Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb1c3bc9f89db3f4b694803a63293a5537d4b98b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89278745"
---
# <a name="what-is-federation-with-azure-ad"></a>O que é a federação com o Azure AD?

Federação é uma coleção de domínios que estabeleceram confiança. O nível de confiança pode variar, mas normalmente inclui a autenticação e quase sempre inclui a autorização. Uma federação típica pode incluir um número de organizações que estabeleceram confiança para acesso compartilhado a um conjunto de recursos.

Você pode estabelecer a federação em seu ambiente local com o Azure AD e usar essa federação para autenticação e autorização.  Esse método de entrada garante que toda a autenticação do usuário ocorra localmente.  Esse método permite que os administradores implementem níveis mais rigorosos de controle de acesso. A federação com o AD FS e o PingFederate está disponível.

![Identidade federada](./media/whatis-hybrid-identity/federated-identity.png)


> [!TIP]
> Se você optar por usar a Federação com o Active Directory Federation Services (AD FS), outra opção será configurar a sincronização de hash de senha como um backup em caso de falha na infraestrutura do AD FS.


## <a name="next-steps"></a>Próximas etapas

- [O que é identidade híbrida?](./whatis-hybrid-identity.md)
- [O que é o Azure AD Connect e o Connect Health?](whatis-azure-ad-connect.md)
- [O que é sincronização de hash da senha?](whatis-phs.md)
- [O que é federação?](whatis-fed.md)
- [O que é logon único?](how-to-connect-sso.md)
- [Como funciona a federação](how-to-connect-fed-whatis.md)
- [Federação com PingFederate](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)