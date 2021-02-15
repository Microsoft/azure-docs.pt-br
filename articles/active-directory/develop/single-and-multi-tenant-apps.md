---
title: Aplicativos únicos e multilocatários no Azure AD
titleSuffix: Microsoft identity platform
description: Saiba mais sobre os recursos e as diferenças entre os aplicativos de locatário único e multilocatário no Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/24/2020
ms.author: ryanwi
ms.reviewer: justhu
ms.custom: aaddev
ms.openlocfilehash: 0868d87d977b15a552b04d5dbd6d19de6931f0ae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91395899"
---
# <a name="tenancy-in-azure-active-directory"></a>Locatários no Azure Active Directory

O Azure AD (Azure Active Directory) organiza objetos como usuários e aplicativos em grupos chamados *locatários*. Os locatários permitem que um administrador defina políticas para os usuários dentro da organização e os aplicativos que a organização tem para atender às suas políticas de segurança e operacionais. 

## <a name="who-can-sign-in-to-your-app"></a>Quem pode entrar no seu aplicativo?

Quando se trata de desenvolvimento de aplicativos, os desenvolvedores podem optar por configurar o aplicativo como de locatário único ou multilocatário durante o registro do aplicativo no [portal do Azure](https://portal.azure.com).
* Os aplicativos de locatário único só estão disponíveis no locatário em que foram registrados, também conhecido como seu locatário inicial.
* Os aplicativos multilocatário estão disponíveis para usuários em seu locatário inicial e em outros locatários.

No portal do Azure, você pode configurar seu aplicativo como de locatário único ou multilocatário, definindo a audiência da seguinte maneira.

| Público | Locatário único/multilocatário | Quem pode entrar | 
|----------|--------| ---------|
| Somente contas neste diretório | Locatário único | Todas as contas de usuário e de convidado em seu diretório podem usar o aplicativo ou a API.<br>*Use essa opção se sua audiência for interna na organização.* |
| Contas de qualquer diretório do Azure AD | Multilocatário | Todos os usuários e convidados com uma conta corporativa ou de estudante da Microsoft podem usar o aplicativo ou a API. Isso inclui escolas e empresas que usam Microsoft 365.<br>*Use essa opção se sua audiência for de clientes empresariais ou educacionais.* |
| Contas em qualquer diretório do Azure AD e contas pessoais da Microsoft (como Skype, Xbox, Outlook.com) | Multilocatário | Todos os usuários com uma conta Microsoft corporativa, de estudante ou pessoal podem usar o aplicativo ou a API. Ele inclui escolas e empresas que usam Microsoft 365, bem como contas pessoais que são usadas para entrar em serviços como Xbox e Skype.<br>*Use essa opção para direcionar ao conjunto mais amplo de contas da Microsoft.* | 

## <a name="best-practices-for-multi-tenant-apps"></a>Práticas recomendadas para aplicativos multilocatário

A criação de um excelente aplicativo multilocatário pode ser complicada devido à quantidade de políticas diferentes que os administradores de TI podem definir em seus locatários. Se você optar por criar um aplicativo multilocatário, siga estas práticas recomendadas:

* Teste seu aplicativo em um locatário que tenha configurado [políticas de acesso condicional](../azuread-dev/conditional-access-dev-guide.md).
* Siga o princípio de acesso mínimo do usuário para garantir que o aplicativo solicite apenas as permissões que ele realmente precisa. 
* Forneça nomes e descrições apropriados para as permissões que expõe no aplicativo. Isso ajuda os usuários e os administradores a saber com o que eles estão concordando quando tentam usar as APIs do aplicativo. Para obter mais informações, confira a seção de práticas recomendadas no [guia de permissões](v2-permissions-and-consent.md).

## <a name="next-steps"></a>Próximas etapas

* [Como converter um aplicativo em multilocatário](howto-convert-app-to-be-multi-tenant.md)
