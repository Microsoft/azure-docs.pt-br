---
title: "Mapeamento de declarações de usuário de colaboração B2B no Azure Active Directory | Microsoft Docs"
description: "referência ao mapeamento de declarações para colaboração B2B do Azure Active Directory"
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
ms.openlocfilehash: 5f8559450b24effd40a38879aeae3a8dd03944a3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Mapeamento de declarações de usuário de colaboração B2B no Azure Active Directory

O Azure AD (Azure Active Directory) dá suporte à personalização das declarações emitidas no token SAML para usuários da colaboração B2B. Quando um usuário é autenticado no aplicativo, o Azure AD emite um token SAML para o aplicativo que contém informações (ou declarações) sobre o usuário que o identifica com exclusividade. Por padrão, isso inclui o nome de usuário, o endereço de email, o nome e sobrenome do usuário. Você pode exibir ou editar as declarações enviadas no token SAML para o aplicativo na guia Atributos.

Há dois possíveis motivos para você precisar editar as declarações emitidas no token SAML.

1. O aplicativo foi escrito para exigir um conjunto diferente de URIs de declaração ou valores de declaração

2. O aplicativo exige que a declaração NameIdentifier seja algo diferente do nome UPN armazenado no Azure Active Directory.

  ![exibir declarações no token SAML](media/active-directory-b2b-claims-mapping/view-claims-in-saml-token.png)

Para obter mais informações sobre como adicionar e editar declarações, confira este artigo sobre personalização de declarações, [Personalizando declarações emitidas no token SAML para aplicativos pré-integrados no Azure Active Directory](develop/active-directory-saml-claims-customization.md). Para usuários da colaboração B2B, o mapeamento de NameID e UPN entre locatários é impedido, por motivos de segurança.


## <a name="next-steps"></a>Próximas etapas

Procure nossos outros artigos sobre a colaboração B2B do AD do Azure:

* [O que é a colaboração B2B do AD do Azure?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Propriedades de usuário de colaboração B2B](active-directory-b2b-user-properties.md)
* [Adicionar um usuário de colaboração B2B a uma função](active-directory-b2b-add-guest-to-role.md)
* [Delegar convites de colaboração B2B](active-directory-b2b-delegate-invitations.md)
* [Grupos dinâmicos e colaboração B2B](active-directory-b2b-dynamic-groups.md)
* [Código de colaboração B2B e exemplos do PowerShell](active-directory-b2b-code-samples.md)
* [Configurar aplicativos SaaS para colaboração B2B](active-directory-b2b-configure-saas-apps.md)
* [Compartilhamento externo do Office 365](active-directory-b2b-o365-external-user.md)
* [Tokens de usuário de colaboração B2B](active-directory-b2b-user-token.md)
* [Limitações atuais da colaboração B2B](active-directory-b2b-current-limitations.md)
