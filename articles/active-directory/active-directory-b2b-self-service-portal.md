---
title: "Portal de inscrição para autoatendimento para colaboração do Azure Active Directory B2B | Microsoft Docs"
description: "A colaboração B2B do Active Directory do Azure dá suporte a relações entre empresas, permitindo que os parceiros de negócios acessem de maneira seletiva seus aplicativos corporativos"
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
ms.date: 05/24/2017
ms.author: sasubram
ms.openlocfilehash: 307373c75bbb87cec683f7a3097f8f159c9d5e61
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Portal de autoatendimento para inscrição na colaboração B2B do Azure AD

Os clientes podem fazer muito com os recursos internos expostos por meio de nosso [Portal do Azure](https://portal.azure.com) de administração de TI e de nosso [Painel de acesso do aplicativo](https://myapps.microsoft.com) para usuários finais. Mas nós também estamos cientes de que as empresas precisam personalizar o fluxo de trabalho de integração para usuários B2B para se adequar às necessidades da organização deles. Eles podem fazer isso com a [nossa API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

Ao discutir isso com nossos clientes, vimos uma necessidade comum se destacar das outras. A organização que está convidando pode não saber antecipadamente quem são os colaboradores externos individuais que precisam de acesso aos seus recursos. Eles queriam uma maneira de os usuários das empresas parceiras fazerem a própria inscrição com um conjunto de políticas controladas pela organização que está convidando. Esse cenário é possível por meio de nossas APIs, portanto, publicamos um projeto no Github que fez exatamente isso: [exemplo de projeto no Github](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

Nosso projeto no Github demonstra como as organizações podem usar as nossas APIs e fornecer uma capacidade de inscrição para autoatendimento baseada em política para seus parceiros confiáveis, com regras que determinam os aplicativos que eles podem acessar. Os usuários de parceiros poderão obter acesso aos recursos quando precisarem, com segurança, sem exigir que a organização que está convidando integre-os manualmente. É possível implantar facilmente o projeto em uma assinatura do Azure de sua escolha.

## <a name="as-is-code"></a>Código no estado em que se encontra

Lembre-se de que esse código é disponibilizado como um exemplo para demonstrar o uso da API de convite do Azure Active Directory B2B. Ele deve ser personalizado pela sua equipe de desenvolvimento ou por um parceiro e deve ser revisado antes de ser implantado em um cenário de produção.

## <a name="next-steps"></a>Próximas etapas

Procure nossos outros artigos sobre a colaboração B2B do AD do Azure:
* [O que é a colaboração B2B do AD do Azure?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Como os administradores do Azure Active Directory adicionam usuários de colaboração B2B?](active-directory-b2b-admin-add-users.md)
* [Como os operadores de informação adicionam usuários de colaboração B2B?](active-directory-b2b-iw-add-users.md)
* [The elements of the B2B collaboration invitation email](active-directory-b2b-invitation-email.md) (Os elementos do email de convite para colaboração B2B)
* [Resgate de convite de colaboração B2B](active-directory-b2b-redemption-experience.md)
* [Licenciamento da colaboração B2B do Azure AD](active-directory-b2b-licensing.md)
* [Solução de problemas de colaboração B2B do Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [Perguntas frequentes sobre a colaboração B2B do Azure Active Directory](active-directory-b2b-faq.md)
* [Autenticação multifator para usuários de colaboração B2B](active-directory-b2b-mfa-instructions.md)
* [Adicionar usuários de colaboração B2B sem um convite](active-directory-b2b-add-user-without-invite.md)
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)