---
title: Saiba mais sobre os protocolos de autorização com suporte pela plataforma de identidade da Microsoft | Azure
description: Um guia para protocolos OAuth 2.0 e OpenID Connect que são compatíveis com o ponto de extremidade de plataforma do Microsoft identity.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 5fb4fa1b-8fc4-438e-b3b0-258d8c145f22
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7ffef5c3a7c8dd21654b6364013b1718bea1292
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482995"
---
# <a name="microsoft-identity-platform-protocols"></a>Protocolos de plataforma de identidade do Microsoft

O Microsoft identity platform ponto de extremidade para identidade-como um serviço com protocolos padrão do setor, OAuth 2.0 e OpenID Connect. Embora o serviço esteja em conformidade com o padrão, pode haver diferenças sutis entre duas implementações diferentes desses protocolos. As informações descritas aqui serão úteis se você optar por criar seu código enviando e tratando solicitações HTTP diretamente, ou usar uma biblioteca de software livre de terceiros ao invés de usar uma de nossas [bibliotecas de software livre](reference-v2-libraries.md).

> [!NOTE]
> Nem todos os cenários do AD do Azure e recursos são compatíveis com o ponto de extremidade de plataforma do Microsoft identity. Para determinar se deve usar o ponto de extremidade de plataforma do Microsoft identity, leia sobre [limitações da plataforma Microsoft identity](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Noções básicas

Em quase todos os fluxos do OAuth 2.0 e do OpenID Connect, há quatro partes envolvidas na troca:

![Diagrama que mostra as funções do OAuth 2.0](./media/active-directory-v2-flows/protocols-roles.svg)

* O **servidor de autorização** é o ponto de extremidade de plataforma de identidade do Microsoft e é responsável por garantir que a identidade do usuário, conceder e revogar o acesso aos recursos e emissão de tokens. O servidor de autorização também é conhecido como provedor de identidade; ele trata com segurança tudo que estiver relacionado às informações do usuário, seu acesso e as relações de confiança entre as partes de um fluxo.
* O **Proprietário do Recurso** normalmente é o usuário final. Ele é a parte que possui os dados e tem o poder de permitir que terceiros acessem esses dados ou recurso.
* O **Cliente OAuth** é o seu aplicativo, identificado pela respectiva ID de Aplicativo. Geralmente é a parte com a qual usuário final interage e solicita tokens do servidor de autorização. O cliente deve receber permissão do proprietário do recurso para acessar o recurso.
* O **Servidor de Recurso** é o local no qual o recurso ou os dados residem. Ele confia no servidor de autorização para autenticar e autorizar o cliente OAuth com segurança e usa os tokens de acesso de portador para garantir que o acesso a um recurso pode ser concedido.

## <a name="app-registration"></a>Registro do Aplicativo

Todos os aplicativos que quer aceitar tanto pessoais e contas corporativas ou de estudante devem ser registrados por meio de **registros de aplicativo** experiência na [portal do Azure](https://aka.ms/appregistrations) antes que ele possa entrar esses usuários usando o OAuth 2.0 ou o OpenID Connect. O processo de registro de aplicativo coleta e atribui alguns valores a seu aplicativo:

* Uma **ID de Aplicativo** que identifica exclusivamente o aplicativo
* Um **URI de redirecionamento** (opcional) que pode ser usado para direcionar as respostas de volta ao seu aplicativo
* Alguns outros valores específicos de cenário.

Para obter mais detalhes, saiba como [registrar um aplicativo](quickstart-register-app.md).

## <a name="endpoints"></a>Pontos de extremidade

Depois de registrado, o aplicativo se comunica com a plataforma de identidade da Microsoft, enviando solicitações para o ponto de extremidade:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Em que o `{tenant}` pode ter um de quatro valores diferente:

| Value | DESCRIÇÃO |
| --- | --- |
| `common` | Permite que os usuários com contas pessoais da Microsoft e contas corporativas/de estudante do Azure AD entrem no aplicativo. |
| `organizations` | Permite que somente usuários com contas corporativas/de estudante do Azure AD entrem no aplicativo. |
| `consumers` | Permite que somente os usuários com MSA (Contas Pessoais da Microsoft) para entrem no aplicativo. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` | Permite que somente os usuários com contas corporativas/de estudante de um locatário específico do Azure AD entrem no aplicativo. É possível usar o nome de domínio amigável do locatário do Azure AD ou o identificador GUID de locatário. |

Para saber mais sobre como interagir com esses pontos de extremidade, escolha um tipo específico de aplicativo na seção [Protocolos](#protocols) e siga os links.

> [!TIP]
> Qualquer aplicativo registrado no Azure AD pode usar o ponto de extremidade de plataforma identidade Microsoft, mesmo se eles não entrarem em contas pessoais.  Dessa forma, você pode migrar aplicativos existentes para a plataforma de identidade da Microsoft e [MSAL](reference-v2-libraries.md) sem recriar seu aplicativo.  

## <a name="tokens"></a>Tokens

A implementação de plataforma de identidade da Microsoft do OAuth 2.0 e OpenID Connect fazem amplo uso de tokens de portador, incluindo tokens de portador representados como JWTs. Um token de portador é um token de segurança leve que concede ao "portador" acesso a um recurso protegido. Nesse sentido, o "portador" é qualquer parte que possa apresentar o token. Embora uma parte deve primeiro se autenticar com a plataforma de identidade da Microsoft para receber o token de portador, se as etapas necessárias não forem tomadas para proteger o token na transmissão e o armazenamento, ele pode ser interceptado e usado por uma parte não intencional. Embora alguns tokens de segurança tenham um mecanismo interno para impedir que partes não autorizadas os utilizem, tokens de portador não possuem esse mecanismo e devem ser transportados em um canal seguro, como segurança da camada de transporte (HTTPS). Se um token de portador for transmitido livremente, uma parte mal-intencionada pode usar um ataque "man-in-the-middle" para adquirir o token e usá-lo para um acesso não autorizado a um recurso protegido. Os mesmos princípios de segurança se aplicam ao armazenar ou manter em cache tokens de portador para uso posterior. Sempre se certifique de que seu aplicativo transmita e armazene tokens de portador de maneira segura. Para obter mais considerações de segurança sobre tokens de portador, consulte [RFC 6750 seção 5](https://tools.ietf.org/html/rfc6750).

Mais detalhes de diferentes tipos de tokens usados no ponto de extremidade de plataforma de identidade está disponível no Microsoft [referência do Microsoft identity platform ponto de extremidade token](v2-id-and-access-tokens.md).

## <a name="protocols"></a>Protocolos

Se você estiver pronto para ver alguns exemplos de solicitação, inicie com um dos tutoriais a seguir. Cada um corresponde a um cenário de autenticação específico. Se você precisar de ajuda para determinar qual é o fluxo certo para você, fazer check-out [os tipos de aplicativos que você pode compilar com a plataforma de identidade Microsoft](v2-app-types.md).

* [Criar aplicativos nativos e móveis com o OAuth 2.0](v2-oauth2-auth-code-flow.md)
* [Criar aplicativos Web com o OpenID Connect](v2-protocols-oidc.md)
* [Criar aplicativos de página única com o fluxo implícito do OAuth 2.0](v2-oauth2-implicit-grant-flow.md)
* [Criar daemons ou processos do servidor com o fluxo de credenciais do cliente OAuth 2.0](v2-oauth2-client-creds-grant-flow.md)
* [Obter tokens em uma API Web com o OAuth 2.0 em nome do fluxo](v2-oauth2-on-behalf-of-flow.md)
