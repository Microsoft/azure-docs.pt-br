---
title: Ponto de extremidade do Azure Active Directory v.2.0 | Microsoft Docs
description: "Uma introdução à criação de aplicativos com conexão à conta da Microsoft e ao Active Directory do Azure."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 2dee579f-fdf6-474b-bc2c-016c931eaa27
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 1e286044fb1a1b367fcac2dc14c47f68d5ed120d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sign-in-microsoft-account--azure-ad-users-in-a-single-app"></a>Conecte-se os usuários da Conta da Microsoft e do Azure AD em um único aplicativo
No passado, um desenvolvedor de aplicativos que quisesse dar suporte a contas da Microsoft pessoas e contas profissionais do Azure Active Directory precisava fazer a integração com dois sistemas separados.  O **ponto de extremidade do Azure AD v2.0** introduz uma nova versão da API de autenticação que permite que você entre nos dois tipos de contas usando uma integração simples.  Aplicativos que usam o ponto de extremidade v2.0 também consomem APIs REST do [Microsoft Graph](https://graph.microsoft.io) usando qualquer um dos tipos de conta.

## <a name="getting-started"></a>Introdução
Escolha sua plataforma favorita na lista a seguir para compilar um aplicativo usando nossas bibliotecas e estruturas de software livre.  Como alternativa, é possível usar nossa documentação do protocolo do OAuth 2.0 e OpenID Connect para enviar e receber mensagens de protocolo diretamente sem o uso de uma biblioteca de autenticação.

<br />

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="whats-new"></a>O que há de novo
As informações aqui serão úteis para entender o que é possível e o que não é com o ponto de extremidade v2.0.

* Saiba mais sobre os [tipos de aplicativos que podem ser criados com o ponto de extremidade v2.0](active-directory-v2-flows.md).
* Entenda as [limitações e restrições](active-directory-v2-limitations.md) do ponto de extremidade v2.0.
* Confira este vídeo de visão geral para o ponto de extremidade v2.0:

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="reference"></a>Referência
Estes links serão úteis na exploração em profundidade da plataforma:

* [Referência do Protocolo v2.0](active-directory-v2-protocols.md)
* [Referência do Token v2.0](active-directory-v2-tokens.md)
* [Referência da Biblioteca v2.0](active-directory-v2-libraries.md)
* [Escopos e Consentimento no ponto de extremidade v2.0](active-directory-v2-scopes.md)
* [O Microsoft Graph](https://graph.microsoft.io)

## <a name="help--support"></a>Ajuda + suporte
Esses são os melhores locais para obter ajuda com o desenvolvimento no Azure Active Directory.

* [Marcas `azure-active-directory` e `adal` do Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory+or+adal)
* [Feedback no Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences)


> [!NOTE]
> Se você precisar apenas entrar em contas corporativas e de estudante usando o Azure Active Directory, comece com nosso [guia do desenvolvedor do Azure AD](active-directory-developers-guide.md).  O ponto de extremidade v2.0 destina-se ao uso por desenvolvedores que precisam explicitamente entrar em contas pessoais da Microsoft.

