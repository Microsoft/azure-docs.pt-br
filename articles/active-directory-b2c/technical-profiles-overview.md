---
title: Sobre perfis técnicos em políticas personalizadas do Azure Active Directory B2C | Microsoft Docs
description: Saiba mais sobre os perfis técnicos que são usados em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f3be6cebafb6d0f50b5ac9a9e40e5707202ea643
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849431"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>Sobre perfis técnicos em políticas personalizadas do Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Um perfil técnico fornece uma estrutura com um mecanismo interno para se comunicar com diferentes tipos de partes usando uma política personalizada no Azure Active Directory (Azure AD) B2C. Os perfis técnicos são usados para se comunicar com o locatário do Azure AD B2C, para criar um usuário ou ler um perfil de usuário. Um perfil técnico pode ser autodeclarado para permitir a interação com o usuário. Por exemplo, colete a credencial do usuário para entrar e renderizar a página de inscrição ou a página de redefinição de senha.

## <a name="type-of-technical-profiles"></a>Tipo de perfis técnicos

Um perfil técnico permite esses tipos de cenários:

- [Azure Active Directory](active-directory-technical-profile.md) – oferece suporte ao gerenciamento de usuário do Azure Active Directory B2C.
- [Emissor de token JWT](jwt-issuer-technical-profile.md) – emite um token JWT que é retornado para o aplicativo de terceira parte confiável.
- **Provedor do fator de telefone** – autenticação multifator.
- [OAuth1](oauth1-technical-profile.md) – federação com qualquer provedor de identidade de protocolo OAuth 1.0.
- [OAuth2](oauth2-technical-profile.md) – federação com qualquer provedor de identidade de protocolo OAuth 2.0.
- [OpenIdConnect](openid-connect-technical-profile.md) – federação com qualquer provedor de identidade de protocolo OpenId Connect.
- [Transformação de declarações](claims-transformation-technical-profile.md) – chama transformações de declarações de saída para manipular valores de declarações, validar declarações ou definir valores padrão para um conjunto de declarações de saída.
- [Provedor Restful](restful-technical-profile.md) – chama os serviços da API REST, como validar a entrada do usuário, enriquecer dados do usuário ou integrar-se aos aplicativos de linha de negócios.
- [SAML2](saml-technical-profile.md) – federação com qualquer provedor de identidade de protocolo SAML.
- [Self-Asserted](self-asserted-technical-profile.md) – Interagir com o usuário. Por exemplo, colete a credencial do usuário para entrar e renderizar a página de inscrição ou a redefinição de senha.
- **WsFed** – federação com qualquer provedor de identidade de protocolo WsFed.
- [Gerenciamento de sessão](active-directory-b2c-reference-sso-custom.md) – lida com diferentes tipos de sessões.
- **Percepções sobre o aplicativo**

## <a name="technical-profile-flow"></a>Fluxo do perfil técnico

Todos os tipos de perfis técnicos compartilham o mesmo conceito. Você envia declarações de entrada, executa a transformação de declarações e se comunica com a parte configurada, como um provedor de identidade, uma API REST ou serviços de diretório do Azure AD. Após a conclusão do processo, o perfil técnico retorna as declarações de saída e pode executar a transformação de declarações de saída. O diagrama a seguir mostra como as transformações e mapeamentos referenciados no perfil técnico são processados. Independentemente da entidade com a qual o perfil técnico interage, após a execução de qualquer declaração de transformação, as declarações de saída do perfil técnico são imediatamente armazenadas no recipiente de declarações.

![Diagrama ilustrando o fluxo de perfil técnico](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)
 
1. **InputClaimsTransformation** – as declarações de entrada de todas as [transformações de declarações](claimstransformations.md) de entrada são recolhidas do recipiente de declarações e, após a execução, as declarações de saída são novamente colocadas no recipiente de declarações. As declarações de saída de uma transformação de declarações de entrada podem ser declarações de entrada de uma transformação de declarações de entrada subsequentes.
2. **InputClaims** – as declarações são retiradas do recipiente de declarações e são usadas no perfil técnico. Por exemplo, um [perfil técnico autodeclarado](self-asserted-technical-profile.md) usa as declarações de entrada para preencher antecipadamente as declarações de saída fornecidas pelo usuário. Um perfil técnico da API REST usa as declarações de entrada para enviar parâmetros de entrada ao ponto de extremidade da API REST. O Azure Active Directory usa a declaração de entrada como um identificador exclusivo para ler, atualizar ou excluir uma conta.
3. **Execução do perfil técnico** – o perfil técnico troca as declarações pela entidade configurada. Por exemplo:
    - Redirecione o usuário para o provedor de identidade para concluir a conexão. Após conectar-se, o usuário retornará e prosseguirá com a execução do perfil técnico.
    - Chame uma API REST enquanto envia parâmetros como o InputClaims e receba de volta informações como o OutputClaims.
    - Crie ou atualize a conta de usuário.
    - Envia e verifica a mensagem de texto da MFA.
4. **ValidationTechnicalProfiles** – no caso de um [perfil técnico autodeclarado](self-asserted-technical-profile.md), é possível chamar um [perfil técnico de validação](validation-technical-profile.md) de entrada. O perfil técnico de validação valida os dados analisados pelo usuário e retorna uma mensagem de erro ou OK, com ou sem declarações de saída. Por exemplo, antes de criar uma nova conta, o Azure AD B2C verifica se o usuário já existe nos serviços de diretório. Você pode chamar um perfil técnico da API REST para adicionar sua própria lógica de negócios.<p>O escopo das declarações de saída de um perfil técnico de validação limita-se ao perfil técnico que invoca o perfil técnico de validação e outros perfis técnicos de validação no mesmo perfil técnico. Se você quiser usar as declarações de saída na próxima etapa da orquestração, será necessário incluir as declarações de saída no perfil técnico que chama o perfil técnico de validação.
5. **OutputClaims** -declarações são retornadas ao recipiente de declarações. Você pode usar essas declarações na próxima etapa de orquestrações ou transformações de declarações de saída.
6. **OutputClaimsTransformations** – as declarações de entrada de todas as [ transformações de declarações](claimstransformations.md) de saída são retiradas do recipiente de declarações. As declarações de saída do perfil técnico das etapas anteriores podem ser entradas de uma transformação de declarações de saída. Após a execução, as declarações de saída são recolocadas no recipiente de declarações. As declarações de saída de uma transformação de declarações de saída também podem ser declarações de entrada de uma transformação de declarações de saída subsequentes.
7. **Gerenciamento de sessão de SSO (logon único)**  - [Gerenciamento de sessão de SSO](active-directory-b2c-reference-sso-custom.md) controla a interação com um usuário após o usuário já ter se autenticado. Por exemplo, o administrador pode controlar se a seleção de provedores de identidade é exibida ou se os detalhes de conta local precisam ser inseridos novamente.

A alteração de configurações ou a adição de novas funcionalidades de um perfil técnico podem ser herdadas de outro perfil técnico.  O elemento **IncludeTechnicalProfile** é uma referência ao perfil técnico de base do qual um perfil técnico é derivado.

Por exemplo, o perfil técnico **AAD-UserReadUsingAlternativeSecurityId-NoError** inclui o **AAD-UserReadUsingAlternativeSecurityId**. Esse perfil técnico define o item de metadados **RaiseErrorIfClaimsPrincipalDoesNotExist** como `true` e gera um erro se uma conta social não existir no diretório. **AAD-UserReadUsingAlternativeSecurityId-NoError** substitui esse comportamento e desativa a mensagem de erro se o usuário não existir.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId** inclui o perfil técnico `AAD-Common`.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">User does not exist. Please sign up before you can sign in.</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId-NoError** e **AAD UserReadUsingAlternativeSecurityId** não especificam o elemento **Protocol** necessário porque ele é especificado no perfil técnico **AAD-Common**.

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

Um perfil técnico pode incluir ou herdar outro perfil técnico, que pode incluir outro perfil técnico. Não há nenhum limite ao número de níveis. Dependendo dos requisitos de negócios, a jornada do usuário pode chamar **AAD-UserReadUsingAlternativeSecurityId** que gera um erro se uma conta social do usuário não existir ou **AAD-UserReadUsingAlternativeSecurityId-NoError**, o que não ocorre Levante um erro.











