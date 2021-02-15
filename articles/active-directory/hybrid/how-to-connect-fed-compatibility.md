---
title: Lista de compatibilidade de federação do AD do Azure
description: Esta página contém provedores de identidade de terceiros que podem ser usados para implementar o logon único.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 22c8693e-8915-446d-b383-27e9587988ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/23/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 895e7347feea3190ba9bdc273200d2985dfaa0d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89661823"
---
# <a name="azure-ad-federation-compatibility-list"></a>Lista de compatibilidade de federação do AD do Azure
O Azure Active Directory fornece logon único e segurança de acesso a aplicativo aprimorada para Microsoft 365 e outros serviços online da Microsoft para implementações híbridas e somente na nuvem sem exigir nenhuma solução de terceiros. Microsoft 365, como a maioria dos serviços online da Microsoft, é integrada com Azure Active Directory para serviços de diretório, autenticação e autorização. O Azure Active Directory também fornece logon único a milhares de aplicativos SaaS e aplicativos Web locais. Consulte a [galeria de aplicativos](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) do Azure Active Directory para aplicativos SaaS com suporte. 

## <a name="idp-validation"></a>Validação de IDP
Se sua organização usa uma solução de Federação de terceiros, você pode configurar o logon único para seus Active Directory usuários locais com o Microsoft Online Services, como Microsoft 365, desde que a solução de Federação de terceiros seja compatível com Azure Active Directory.  Para dúvidas sobre a compatibilidade, entre em contato com seu provedor de identidade.  Se você quiser ver uma lista de provedores de identidade que foram testados anteriormente quanto à compatibilidade com o Azure AD, consulte documentos de [compatibilidade do provedor de identidade do Azure ad](https://www.microsoft.com/download/details.aspx?id=56843). 

>[!NOTE]
>A Microsoft não fornece mais testes de validação a provedores de identidade independentes para compatibilidade com o Azure Active Directory. Se você deseja testar a interoperabilidade do seu produto, consulte estas [diretrizes](https://www.microsoft.com/download/details.aspx?id=56843). 

## <a name="next-steps"></a>Próximas etapas

- [Integrar seus diretórios locais no Azure Active Directory](whatis-hybrid-identity.md)
- [Azure AD Connect e federação](how-to-connect-fed-whatis.md)
