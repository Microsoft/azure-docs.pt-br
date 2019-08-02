---
title: Habilitar o SSO para seu aplicativo multilocatário
description: Diretrizes para fornecedores independentes de software sobre como integrar com o Azure Active Directory
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c89a83ade6305579e700afb86f0b9e3aca2695e
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795173"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>Habilitar o logon único para seu aplicativo multilocatário  

Quando você oferece o seu aplicativo para uso por outras empresas por meio de uma compra ou uma assinatura, você tornar seu aplicativo disponível aos clientes dentro de seus próprios locatários do Azure. Isso é conhecido como criar um aplicativo multilocatário. Para visão geral desse conceito, consulte [aplicativos de multilocatário no Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) e [multilocação no Azure Active Directory](../develop/single-and-multi-tenant-apps.md).

## <a name="what-is-single-sign-on"></a>O que é o logon único

Logon único (SSO) adiciona segurança e conveniência quando os usuários entram aplicativos usando o Azure Active Directory e outras identidades. Quando um aplicativo é SSO habilitado, os usuários não precisarão inserir credenciais separadas para acessar esse aplicativo. Para obter uma explicação completa de logon único. [Consulte o logon único para aplicativos no Azure Active Directory](what-is-single-sign-on.md).

## <a name="why-enable-single-sign-on-in-your-application"></a>Por que habilitar o logon único em seu aplicativo?

Há muitas vantagens para habilitar o SSO em seu aplicativo multilocatário. Quando você habilita o SSO para seu aplicativo:

* Seu aplicativo pode ser listado no Azure Marketplace, onde seu aplicativo é detectável por milhões de organizações que usam o Active Directory do Azure.
  * Permite que os clientes configurar rapidamente o aplicativo com o Azure AD.

* Seu aplicativo pode ser detectável na Galeria de aplicativos do Office 365, o iniciador de aplicativos do Office 365 e no Microsoft Search no Office.com

* Seu aplicativo pode usar a API REST do Microsoft Graph para acessar os dados que impulsionam a produtividade do usuário que está disponível no Microsoft Graph.

* Você pode reduzir os custos de suporte, tornando mais fácil para seus clientes.
  * Coproduced documentação específica do aplicativo com a equipe do Azure AD para a adoção de atenuações nossos clientes mútuos.
  * Se o SSO em um único clique está habilitado, os administradores de TI dos seus clientes não precisa aprender a configurar seu aplicativo para uso em sua organização.

* Você fornece aos clientes a capacidade de gerenciar totalmente de seus convidados e funcionários identidades autenticação e autorização.

  * Colocando toda responsabilidade de conformidade e gerenciamento de conta com o proprietário do cliente dessas identidades.

  * Fornecendo capacidade de habilitar ou desabilitar o SSO para provedores de identidade específicas, grupos ou usuários atender às suas necessidades de negócios.

* Você aumentar sua presença e adoptability. Muitas organizações de grandes porte exigem que (ou aspirar) aos seus funcionários têm experiências SSO contínuas em todos os aplicativos. Facilita o SSO é importante.

* Reduza o atrito do usuário final, que pode aumentar o uso do usuário final e aumentar sua receita.

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>Como habilitar o logon único em seu aplicativo publicado

1. [Escolha o protocolo de Federação correto para seu aplicativo multilocatário](isv-choose-multi-tenant-federation.md).
1. Implementar o SSO em seu aplicativo
   - Consulte [orientação sobre padrões de autenticação](../develop/v2-app-types.md)
   - Ver [exemplos de código do Active Directory do Azure](../develop/sample-v2-code.md) para protocolos OAuth e OIDC
1. [Criar seu locatário do Azure](isv-tenant-multi-tenant-app.md) e testar seu aplicativo
1. [Criar e publicar a documentação de SSO em seu site](isv-create-sso-documentation.md).
1. [Enviar sua listagem do aplicativo](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) e de parceiros com a Microsoft para criar a documentação no site da Microsoft.
1. [Junte-se Microsoft Partner Network (gratuito) e criar seu ir para o plano de mercado](https://partner.microsoft.com/en-us/explore/commercial#gtm).
