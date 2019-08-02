---
title: Escolha o protocolo de Federação correto para seu aplicativo multilocatário
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
ms.openlocfilehash: 53b315b87200b37cda215a29a65be9babaf54f43
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795190"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>Escolha o protocolo de Federação correto para seu aplicativo multilocatário

Quando você desenvolve seu aplicativo software como serviço (SaaS), você deve selecionar o protocolo de federação que melhor atenda às necessidades de seus e seus clientes. Essa decisão se baseia em sua plataforma de desenvolvimento e seu desejo de integrar com os dados disponíveis dentro do ecossistema do Office 365 e o Azure AD dos seus clientes.

Consulte a lista completa dos [protocolos disponíveis para a integração de SSO](what-is-single-sign-on.md) com o Azure Active Directory.
A tabela seguinte compara 
* Autenticação aberta 2.0 (OAuth 2.0)
* Open ID Connect (OIDC)
* SAML (Security Assertion Markup Language)
* Web Services Federation (WSFed)

| Recurso| OAuth / OIDC| SAML / WSFed |
| - |-|-|
| Baseado na Web SSO| √| √ |
| Baseado na Web o logout único| √| √ |
| Baseado no Mobile logon único| √| √* |
| Baseado no Mobile o logout único| √| √* |
| Políticas de acesso condicional para aplicativos móveis| √| X |
| Experiência perfeita de MFA para aplicativos móveis| √| X |
| Acesso Microsoft Graph| √| X |

\* Possíveis, mas a Microsoft não fornece exemplos ou orientações.

## <a name="oauth-20-and-open-id-connect"></a>OAuth 2.0 e Open ID Connect

OAuth 2.0 é um [padrão da indústria](https://oauth.net/2/) protocolo para autorização. OIDC (OpenID Connect) é um [padrão da indústria](https://openid.net/connect/) camada de autenticação de identidade criada sobre o protocolo Oath 2.0.

### <a name="benefits"></a>Benefícios

A Microsoft recomenda usando OIDC/OAuth 2.0 que eles tenham interno para os protocolos de autorização e autenticação. Com SAML, você deve implementar a autorização Além disso.

A autorização inerente esses protocolos permite que seu aplicativo acessar e integrar dados organizacionais por meio da API do Microsoft Graph e de usuário avançada.

Usando o OAuth 2.0 e o OIDC simplifica a experiência do usuário final de seus clientes ao adotar o SSO para seu aplicativo. Você pode definir facilmente o necessário de conjuntos de permissão, que, em seguida, é representado automaticamente para o administrador ou usuário final consentimento.

Além disso, o uso desses protocolos permite aos clientes usar políticas MFA e acesso condicional para controlar o acesso aos aplicativos. A Microsoft fornece bibliotecas e [exemplos de código em várias plataformas de tecnologia](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) para ajudar o seu desenvolvimento.  

### <a name="implementation"></a>Implementação

Registre seu aplicativo com o Microsoft Identity, que é um provedor OAuth 2.0. Também, em seguida, foi possível registrar seu aplicativo com base em OAuth 2.0 com qualquer outro provedor de identidade que você deseja integrar. 

Para obter informações sobre como registrar seu aplicativo e implementar esses protocolos para que o SSO para aplicativos web, consulte [autorizar o acesso a aplicativos web usando o OpenID Connect e Azure Active Directory](../develop/sample-v2-code.md).  Para obter informações sobre como implementar esses protocolos para que o SSO em aplicativos móveis, consulte o seguinte: 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [Plataforma Universal do Windows](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2.0 e WSFed

Marcação linguagem SAML (Security Assertion) geralmente é usado para aplicativos da web. Ver [como o Azure usa o protocolo SAML](../develop/active-directory-saml-protocol-reference.md) para uma visão geral. 

Web Services Federation (WSFed) é um [padrão da indústria](http://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) geralmente usado para aplicativos da web desenvolvidos usando a plataforma .net.

### <a name="benefits"></a>Benefícios

SAML 2.0 é um padrão maduro e a maioria das plataformas tecnologia suporte ao código-fonte aberto a bibliotecas para SAML 2.0. Você pode fornecer uma interface de administração para configurar o SSO do SAML para seus clientes. Eles podem configurar o SSO do SAML para o Microsoft Azure AD e qualquer outro provedor de identidade que dá suporte a SAML 2

### <a name="trade-offs"></a>Vantagens e desvantagens

Ao usar protocolos SAML 2.0 ou WSFed para aplicativos móveis, algumas políticas de acesso condicional, incluindo autenticação multifator (MFA) terão uma experiência inadequada. Além disso, se você quiser acessar o Microsoft Graph, você precisará implementar a autorização por meio do OAuth 2.0 para gerar tokens necessárias. 

### <a name="implementation"></a>Implementação

A Microsoft não fornecer bibliotecas para implementação de SAML ou recomendável bibliotecas específicas. Há muitas bibliotecas de código-fonte aberto disponível.

## <a name="sso-and-using-microsoft-graph-rest-api"></a>SSO e usando o Microsoft Graph Rest API 

Microsoft Graph é a malha de dados em todos os Microsoft 365, incluindo o Office 365, Windows 10 e Enterprise Mobility e segurança e outros produtos como o Dynamics 365. Isso inclui os principais esquemas das entidades como usuários, grupos, calendário, email, arquivos e muito mais, a produtividade do usuário dessa unidade. Microsoft Graph oferece três interfaces para a API baseada em desenvolvedores um REST, conectar os dados do Microsoft Graph e conectores que permitem aos desenvolvedores adicionar seus próprios dados para o Microsoft Graph.  

Usando qualquer um dos protocolos acima para que o SSO permite o acesso do seu aplicativo para os dados avançados disponíveis por meio da API de REST do Microsoft Graph. Isso permite que seus clientes obter mais valor de seus investimentos em Microsoft 365. Por exemplo, seu aplicativo pode chamar a API do Microsoft Graph para integrar com a instância do Office 365 de seus clientes e Microsoft Office os usuários superfície e SharePoint itens dentro de seu aplicativo. 

Se você estiver usando Open ID Connect para autenticar, em seguida, sua experiência de desenvolvimento é perfeita, pois você irá usar OAuth2, a base do Open ID Connect, para adquirir tokens pode ser usada para invocar as APIs do Microsoft Graph. Se seu aplicativo estiver usando SAML ou WSFed, você deve adicionar o código adicional dentro de seu aplicativo para obter esses OAuth2 para adquirir tokens necessários para invocar as APIs do Microsoft Graph. 

## <a name="next-steps"></a>Próximas etapas

[Habilitar o SSO para seu aplicativo multilocatário](isv-sso-content.md)

[Criar documentação para seu aplicativo multilocatário](isv-create-sso-documentation.md)
