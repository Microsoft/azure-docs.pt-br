---
title: "Comparação da colaboração B2B e B2C no Azure Active Directory | Microsoft Docs"
description: "Qual é a diferença entre colaboração B2B e a B2C do Azure Active Directory?"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 03/15/2017
ms.author: sasubram
ms.openlocfilehash: 44cbbc149787a2d6cf2e0e8750b98d33b52f6136
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Comparação da colaboração B2B e B2C no Azure Active Directory

As duas colaborações B2B e B2C do Azure Active Directory (AD do Azure) permitem que você trabalhe com usuários externos no AD do Azure. Mas como eles se comparam?


Recursos de colaboração B2B |     Oferta autônoma do B2C do AD do Azure
-------- | --------
Destinado a: organizações que desejam poder autenticar usuários de uma organização de parceiros, independentemente do provedor de identidade. | Destinado a: convidar clientes dos aplicativos Web e móveis, sejam indivíduos, clientes institucionais ou organizacionais, para o Azure AD.
Identidades suportadas: funcionários com contas corporativas ou escolares, parceiros com contas corporativas ou escolares ou qualquer email. Em breve com suporte à federação direta.  | Identidades suportadas: os usuários do consumidor com contas de aplicativo local (qualquer nome de usuário ou email) ou identidades sociais suportadas com federação direta.
Qual diretório são os usuários do parceiro encontram-se: usuários de parceiros da organização externa são gerenciados no mesmo diretório que os funcionários, mas anotados especialmente. Eles podem ser gerenciados da mesma forma que os funcionários, podem ser adicionados aos mesmos grupos e assim por diante  | Qual diretório as entidades de usuário do cliente estão: no diretório do aplicativo. Gerenciado separadamente do diretório de parceiros e funcionários da organização (se houver).
Há suporte para SSO (logon único) em todos os aplicativos conectados ao Azure AD. Por exemplo, é possível fornecer acesso ao Office 365 ou a aplicativos locais, além de outros aplicativos SaaS, como o Salesforce ou Workday.  |  Há suporte para SSO para aplicativos de clientes dentro dos locatários do B2C do AD do Azure. Não há suporte para SSO para Office 365 nem para outros aplicativos SaaS da Microsoft SaaS que não sejam da Microsoft.
Ciclo de vida do parceiro: gerenciado pela organização convidada/host.  | Ciclo de vida do cliente: autoatendido ou gerenciado pelo aplicativo.
Política de segurança e conformidade: gerenciada pela organização convidada/host.  | Política de segurança e conformidade: gerenciada pelo aplicativo.
Identidade visual: uso da marca da organização convidada/host.  |    Identidade visual: gerenciada pelo aplicativo. Geralmente, tende a ser da marca do produto com o esmaecimento da organização na tela de fundo.
Mais informações em: [Postagem de Blog](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [Documentação](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | Mais informações em: [Página](https://azure.microsoft.com/en-us/services/active-directory-b2c/), [Documentação](https://docs.microsoft.com/en-us/azure/active-directory-b2c/)


### <a name="next-steps"></a>Próximas etapas

Procure nossos outros artigos sobre a colaboração B2B do AD do Azure:

* [O que é a colaboração B2B do AD do Azure?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Propriedades de usuário de colaboração B2B](active-directory-b2b-user-properties.md)
* [Como adicionar um usuário de colaboração B2B a uma função](active-directory-b2b-add-guest-to-role.md)
* [Delegação de convites de colaboração B2B](active-directory-b2b-delegate-invitations.md)
* [Grupos dinâmicos e colaboração B2B](active-directory-b2b-dynamic-groups.md)
* [Configuração de aplicativos SaaS para colaboração B2B](active-directory-b2b-configure-saas-apps.md)
* [Tokens de usuário de colaboração B2B](active-directory-b2b-user-token.md)
* [Mapeamento de declarações de usuário de colaboração B2B](active-directory-b2b-claims-mapping.md)
* [Compartilhamento externo do Office 365](active-directory-b2b-o365-external-user.md)
* [Limitações atuais da colaboração B2B](active-directory-b2b-current-limitations.md)
* [Como obter suporte para colaboração B2B](active-directory-b2b-support.md)
