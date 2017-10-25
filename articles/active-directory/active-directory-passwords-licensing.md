---
title: 'Licenciamento: SSPR do Azure AD | Microsoft Docs'
description: "Requisitos de licenciamento para redefinição da senha de autoatendimento do Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 5445a479fd6893048eb8ff356fa829a3dcd5f7d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Requisitos de licenciamento para redefinição da senha de autoatendimento do Azure AD

Para a redefinição de senhas do AD do Azure funcionar, você **deverá ter pelo menos uma licença atribuída na sua organização**. Não impomos licenciamento por usuário na experiência de redefinição da senha. Para manter a conformidade com o contrato de licenciamento da Microsoft, você precisa atribuir licenças a todos os usuários que usam recursos premium.

* **Somente usuários de nuvem** – Qualquer SKU pago do Office 365 (O365) ou Azure AD Basic
* Usuários na **nuvem** e/ou **locais** – Azure AD Premium P1 ou P2, Enterprise Mobility + Security (EMS) ou Secure Productive Enterprise (SPE)

## <a name="licenses-required-for-password-writeback"></a>Licenças necessárias para write-back de senha

Para usar o write-back de senha, você deve ter uma das licenças a seguir atribuídas no locatário.

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3
* Enterprise Mobility + Security E5
* Microsoft 365 E3
* Microsoft 365 E5

> [!NOTE]
> Os planos de licenciamento do Standalone Office 365 **não dão suporte ao write-back de senha** e exigem um dos planos anteriores para que essa funcionalidade funcione.

As informações de licenciamento adicionais, inclusive custos, podem ser encontradas nas páginas a seguir

* [Site de Preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Recursos e funcionalidades do Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365](https://www.microsoft.com/microsoft-365/enterprise)

## <a name="enable-group-or-user-based-licensing"></a>Habilitar licenciamento com base em grupo ou usuário

O Azure AD já dá suporte ao licenciamento com base em grupo, permitindo que os administradores atribuam licenças em massa a um grupo de usuários, em vez de atribuí-los um por vez. [Atribuir, verificar e resolver problemas com licenças](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses)

Alguns serviços da Microsoft não estão disponíveis em todos os locais. Para que uma licença possa ser atribuída a um usuário, o administrador precisa especificar a propriedade “Local de uso” para o usuário. A atribuição de licenças pode ser feita em Usuário > Perfil > seção Configurações no portal do Azure. **Ao usar a atribuição de grupo de licenças, qualquer usuário sem um local de uso especificado herda o local do diretório.**

## <a name="next-steps"></a>Próximas etapas

Os links a seguir fornecem informações adicionais sobre a redefinição de senha usando o Azure AD

* [**Início rápido** ](active-directory-passwords-getting-started.md) - Para deixar em funcionamento com o gerenciamento de senha de autoatendimento do Azure AD 
* [**Dados** ](active-directory-passwords-data.md) - Entender os dados necessários e como são usados para o gerenciamento de senhas
* [**Distribuição**](active-directory-passwords-best-practices.md): planeje e implante o SSPR para seus usuários usando as diretrizes descritas aqui
* [**Personalizar**](active-directory-passwords-customize.md): personalize a aparência da experiência do SSPR em sua empresa.
* [**Relatórios**](active-directory-passwords-reporting.md): descubra se, quando e onde os usuários estão acessando a funcionalidade SSPR
* [**Detalhamento Técnico**](active-directory-passwords-how-it-works.md): veja os bastidores para entender como o recurso funciona
* [**Perguntas frequentes**](active-directory-passwords-faq.md): como? Por quê? O quê? Onde? Quem? Quando? – respostas para perguntas que você sempre quis fazer
* [**Solução de problemas**](active-directory-passwords-troubleshoot.md) - Saiba como resolver problemas comuns que vemos com a SSPR
