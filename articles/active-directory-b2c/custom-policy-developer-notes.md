---
title: Notas do desenvolvedor para políticas personalizadas
titleSuffix: Azure AD B2C
description: Observações para os desenvolvedores sobre a configuração e a manutenção do Azure AD B2C com políticas personalizadas.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/19/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 76dcb9aec935dd4ea4f57a1362953d9741d8eaf0
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095792"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Notas do desenvolvedor para políticas personalizadas no Azure Active Directory B2C

A configuração de política personalizada no Azure Active Directory B2C agora está disponível para o público geral. Esse método de configuração se destina a desenvolvedores avançados de identidade para criar soluções de identidade complexas. As políticas personalizadas disponibilizam o poder da Identity Experience Framework em locatários do Azure AD B2C.
Os desenvolvedores de identidade avançada que usam políticas personalizadas devem reservar um tempo para concluir os passo a passo e ler os documentos de referência.

Embora a maioria das opções de política personalizada disponíveis já esteja disponível para o público geral, há recursos subjacentes, como tipos de perfil técnico e APIs de definição de conteúdo que estão em diferentes estágios no ciclo de vida do software. Muitos outros recursos estão chegando. A tabela abaixo especifica o nível de disponibilidade em um nível mais granular.

## <a name="features-that-are-generally-available"></a>Recursos que estão disponíveis para o público geral

- Criar e carregar percursos do usuário de autenticação personalizada usando políticas personalizadas.
    - Descrever as percursos do usuário passo a passo como trocas entre os provedores de declarações.
    - Definir a ramificação condicional em percursos do usuário.
- Interagir com os serviços habilitados para API REST nos percursos do usuário de autenticação personalizada.
- Federar com provedores de identidade que estão em conformidade com o protocolo OpenIDConnect.
- Federar com provedores de identidade que seguem o protocolo SAML 2.0.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Responsabilidades dos desenvolvedores de conjunto de recursos de política personalizada

A configuração de política manual concede acesso de nível inferior para a plataforma subjacente do Azure AD B2C e resulta na criação de uma estrutura confiável exclusiva. As muitas permutas possíveis de provedores de identidade personalizados, relações de confiança, integrações com serviços externos e fluxos de trabalho passo a passo exigem uma abordagem unidirecional para design e configuração.

Os desenvolvedores que consomem o conjunto de recursos de política personalizada devem aderir às seguintes diretrizes:

- Se familiarize com a linguagem de configuração das políticas gerenciadas e do gerenciamento de chave/segredos. Para obter mais informações, veja [TrustFrameworkPolicy](trustframeworkpolicy.md).
- Apropriar-se de cenários e de integrações personalizadas. Documente seu trabalho e informe sua organização do site ativo.
- Executar testes de cenário metódicos.
- Siga as práticas recomendadas de desenvolvimento e preparo de software. É recomendável um mínimo de um ambiente de desenvolvimento e teste.
- Mantenha-se informado sobre novos desenvolvimentos de provedores de identidade e de serviços para integração. Por exemplo, acompanhe as alterações em segredos e alterações programadas e não programadas no serviço.
- Configure o monitoramento ativo e monitore a capacidade de resposta dos ambientes de produção. Para obter mais informações sobre a integração com o Application Insights, veja [Azure Active Directory B2C: Coleta de logs](analytics-with-application-insights.md).
- Mantenha atualizados os endereços de email de contato na assinatura do Azure e permaneça responsivo aos emails da equipe de site ativo da Microsoft.
- Execute ação em tempo hábil quando for aconselhado a fazer isso pela equipe de site ativo da Microsoft.

## <a name="terms-for-features-in-public-preview"></a>Termos para os recursos na versão prévia pública

- Incentivamos o uso dos recursos na versão prévia pública somente para fins de avaliação.
- Os contratos de nível de serviço (SLAs) não se aplicam aos recursos na versão prévia pública.
- As solicitações de suporte para os recursos na versão prévia pública podem ser arquivadas através dos canais de suporte normais.

## <a name="features-by-stage-and-known-issues"></a>Recursos por estágio e problemas conhecidos

Os recursos de política personalizada estão sob o desenvolvimento constante. A tabela a seguir é um índice de recursos e componentes disponíveis.


### <a name="protocols-and-authorization-flows"></a>Protocolos e fluxos de autorização

| Recurso | Desenvolvimento | Visualização | GA | Observações |
|-------- | :-----------: | :-------: | :--: | ----- |
| [Código de autorização OAuth2](authorization-code-flow.md) |  |  | X |  |
| Código de autorização OAuth2 com PKCE |  |  | X | [Clientes públicos e aplicativos de página única](authorization-code-flow.md)  |
| [Fluxo implícito de OAuth2](implicit-flow-single-page-application.md) |  |  | X |  |
| [Credenciais da senha de proprietário do recurso OAuth2](ropc-custom.md) |  | X |  |  |
| [OIDC Connect](openid-connect.md) |  |  | X |  |
| [SAML2](saml-service-provider.md)  |  |  |X  | POST e redirecionamento de associações. |
| OAuth1 |  |  |  | Sem suporte. |
| WSFED | X |  |  |  |

### <a name="identify-providers-federation"></a>Identificação da federação de provedores 

| Recurso | Desenvolvimento | Visualização | GA | Observações |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OpenID Connect](openid-connect-technical-profile.md) |  |  | X | Por exemplo, Google+.  |
| [OAuth2](oauth2-technical-profile.md) |  |  | X | Por exemplo, Facebook.  |
| [OAuth1](oauth1-technical-profile.md) |  | X |  | Por exemplo, Twitter. |
| [SAML2](identity-provider-generic-saml.md) |  |   | X | Por exemplo, Salesforce, ADFS. |
| WSFED| X |  |  |  |


### <a name="rest-api-integration"></a>Integração da API REST

| Recurso | Desenvolvimento | Visualização | GA | Observações |
|-------- | :-----------: | :-------: | :--: | ----- |
| [API REST com autenticação básica](secure-rest-api.md#http-basic-authentication) |  |  | X |  |
| [API REST com autenticação de certificado do cliente](secure-rest-api.md#https-client-certificate-authentication) |  |  | X |  |
| [API REST com autenticação de portador OAuth2](secure-rest-api.md#oauth2-bearer-authentication) |  | X |  |  |

### <a name="component-support"></a>Suporte do componente

| Recurso | Desenvolvimento | Visualização | GA | Observações |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Autenticação por telefone](phone-factor-technical-profile.md) |  |  | X |  |
| [Autenticação do Azure AD MFA](multi-factor-auth-technical-profile.md) |  | X |  |  |
| [Senha de uso único](one-time-password-technical-profile.md) |  | X |  |  |
| [Azure Active Directory](active-directory-technical-profile.md) como diretório local |  |  | X |  |
| Subsistema de email do Azure para verificação de email |  |  | X |  |
| [Provedores de serviço de email de terceiros](custom-email-mailjet.md) |  |X  |  |  |
| [Suporte a vários idiomas](localization.md)|  |  | X |  |
| [Validações de predicado](predicates.md) |  |  | X | Por exemplo, complexidade de senha. |
| [Controles de exibição](display-controls.md) |  |X  |  |  |


### <a name="app-ief-integration"></a>Integração de aplicativo IEF

| Recurso | Desenvolvimento | Visualização | GA | Observações |
| ------- | :-----------: | :-------: | :--: | ----- |
| Parâmetro `domain_hint` de cadeia de caracteres de consulta |  |  | X | Disponível como declaração, pode ser passado para IDP. |
| Parâmetro `login_hint` de cadeia de caracteres de consulta |  |  | X | Disponível como declaração, pode ser passado para IDP. |
| Inserir JSON na jornada do usuário através de `client_assertion` | X |  |  | Será preterido. |
| Inserir JSON na jornada do usuário como `id_token_hint` |  | X |  | Abordagem avançada para passar JSON. |
| [Passagem do token do provedor de identidade para o aplicativo](idp-pass-through-user-flow.md) |  | X |  | Por exemplo, do Facebook para o aplicativo. |


### <a name="session-management"></a>Gerenciamento da sessão

| Recurso | Desenvolvimento | Visualização | GA | Observações |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Provedor de sessão de SSO](custom-policy-reference-sso.md#defaultssosessionprovider) |  |  | X |  |
| [Provedor de sessão de logon externo](custom-policy-reference-sso.md#externalloginssosessionprovider) |  |  | X |  |
| [Provedor de sessão de SSO do SAML](custom-policy-reference-sso.md#samlssosessionprovider) |  |  | X |  |
| [OAuthSSOSessionProvider](custom-policy-reference-sso.md#oauthssosessionprovider)  |  | X |  |  |
| [Logout único](session-behavior.md#sign-out)  |  | X |  |  |

### <a name="security"></a>Segurança

| Recurso | Desenvolvimento | Visualização | GA | Observações |
|-------- | :-----------: | :-------: | :--: | ----- |
| Chaves de política – gerar, manual, upload |  |  | X |  |
| Chaves de política – RSA/Cert, segredos |  |  | X |  |


### <a name="developer-interface"></a>Interface do desenvolvedor

| Recurso | Desenvolvimento | Visualização | GA | Observações |
| ------- | :-----------: | :-------: | :--: | ----- |
| Portal do Azure – IEF UX |  |  | X |  |
| Upload de política |  |  | X |  |
| [Logs de jornada do usuário do Application Insights](troubleshoot-with-application-insights.md) |  | X |  | Usados para solução de problemas durante o desenvolvimento.  |
| [Logs de eventos do Application Insights](analytics-with-application-insights.md) |  | X |  | Usados para monitorar os fluxos de usuário em produção. |


## <a name="next-steps"></a>Próximas etapas

- Verifique as [operações de Microsoft Graph disponíveis para Azure AD B2C](microsoft-graph-operations.md)
- Saiba mais sobre [políticas personalizadas e as diferenças com os fluxos de usuário](custom-policy-overview.md).
