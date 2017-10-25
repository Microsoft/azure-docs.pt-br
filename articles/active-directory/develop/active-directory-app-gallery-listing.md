---
title: Listando seu aplicativo na galeria de aplicativos do Azure Active Directory
description: "Como listar um aplicativo que oferece suporte a logon único na galeria do Active Directory do Azure | Microsoft Azure"
services: active-directory
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: cf25772bd9d92b59401aa5da76e6bbd5fa5ee3e5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="listing-your-application-in-the-azure-active-directory-application-gallery"></a>Listando seu aplicativo na galeria de aplicativos do Azure Active Directory
Para listar um aplicativo que dê suporte a logon único com o Active Directory do Azure na [galeria do Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/), o aplicativo primeiro precisa implementar um dos seguintes modos de integração:

* **OpenID Connect** : integração direta com o Azure AD usando o OpenID Connect para autenticação e a API de consentimento do AD do Azure para configuração. Se você estiver apenas começando uma integração e seu aplicativo não der suporte a SAML, esse é o modo recomendado.
* **SAML** : seu aplicativo já tem a capacidade de configurar provedores de identidade de terceiros usando o protocolo SAML.

Os requisitos de listagem para cada modo estão abaixo.

## <a name="openid-connect-integration"></a>Integração do OpenID Connect
Para integrar seu aplicativo ao Azure AD, siga as [instruções de desenvolvedor](active-directory-authentication-scenarios.md). Responda às perguntas abaixo e envie para waadpartners@microsoft.com.

* Fornece credenciais para uma conta ou locatário de teste com o aplicativo que pode ser usado pela equipe do Azure AD para testar a integração.  
* Forneça instruções sobre como a equipe do AD do Azure pode entrar e conectar uma instância do AD do Azure em seu aplicativo usando a [estrutura de consentimento do AD do Azure](active-directory-integrating-applications.md#overview-of-the-consent-framework). 
* Forneça instruções adicionais necessárias à equipe do AD do Azure para testar o logon único no seu aplicativo. 
* Forneça as informações abaixo:

> Nome da empresa:
> 
> Site da empresa:
> 
> Nome do aplicativo:
> 
> Descrição do aplicativo (limite de 200 caracteres):
> 
> Site do aplicativo (informativo):
> 
> Site de suporte técnico do aplicativo ou informações de contato:
> 
> ID do aplicativo, conforme mostrado nos detalhes do aplicativo em https://portal.azure.com:
> 
> URL de inscrição do aplicativo onde os clientes vão para se inscrever e/ou comprar o aplicativo:
> 
> Escolha até três categorias em que seu aplicativo deva ser listado (para saber as categorias disponíveis, consulte o Azure Active Directory Marketplace):
> 
> Anexe o ícone pequeno do aplicativo (arquivo PNG, 45px por 45px, cor de plano de fundo sólida):
> 
> Anexe o ícone grande do aplicativo (arquivo PNG, 215px por 215px, cor de plano de fundo transparente):
> 
> Anexe o logotipo do aplicativo (arquivo PNG, 150px por 122px, cor de plano de fundo transparente):
> 
> 

## <a name="saml-integration"></a>Integração SAML
Qualquer aplicativo que dê suporte ao SAML 2.0 pode ser integrado diretamente a um locatário do AD do Azure usando [estas instruções para adicionar um aplicativo personalizado](../active-directory-saas-custom-apps.md). Depois de testar se a integração do aplicativo funciona com o AD do Azure, envie as informações a seguir para <mailto:waadpartners@microsoft.com>.

* Fornece credenciais para uma conta ou locatário de teste com o aplicativo que pode ser usado pela equipe do Azure AD para testar a integração.  
* Forneça os valores da URL de logon do SAML, da URL de emissor (ID da entidade) e da URL de resposta (serviço do consumidor de declaração) para seu aplicativo, conforme descrito [aqui](../active-directory-saas-custom-apps.md). Se você geralmente fornece esses valores como parte de um arquivo de metadados do SAML, envie este também.
* Forneça uma breve descrição de como configurar o AD do Azure como um provedor de identidade em seu aplicativo usando o SAML 2.0. Se seu aplicativo der suporte à configuração do AD do Azure como um provedor de identidade por meio de um portal administrativo de autoatendimento, verifique se as credenciais fornecidas acima incluem a capacidade de configurá-la.
* Forneça as informações abaixo:

> Nome da empresa:
> 
> Site da empresa:
> 
> Nome do aplicativo:
> 
> Descrição do aplicativo (limite de 200 caracteres):
> 
> Site do aplicativo (informativo):
> 
> Site de suporte técnico do aplicativo ou informações de contato:
> 
> URL de inscrição do aplicativo onde os clientes vão para se inscrever e/ou comprar o aplicativo:
> 
> Escolha até três categorias em que seu aplicativo deva ser listado (para saber as categorias disponíveis, consulte o [Marketplace do Active Directory do Azure](https://azure.microsoft.com/marketplace/active-directory/))):
> 
> Anexe o ícone pequeno do aplicativo (arquivo PNG, 45px por 45px, cor de plano de fundo sólida):
> 
> Anexe o ícone grande do aplicativo (arquivo PNG, 215px por 215px, cor de plano de fundo transparente):
> 
> Anexe o logotipo do aplicativo (arquivo PNG, 150px por 122px, cor de plano de fundo transparente):
> 
> 

