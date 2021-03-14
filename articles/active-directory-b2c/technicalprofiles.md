---
title: Perfis técnicos
titleSuffix: Azure AD B2C
description: Especifique o elemento TechnicalProfiles de uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bcff1ffd574db910c3206d82e4da0e9428db788f
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "102631788"
---
# <a name="technical-profiles"></a>Perfis técnicos

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Um *perfil técnico* fornece uma estrutura com um mecanismo interno para se comunicar com diferentes tipos de partes. Os perfis técnicos são usados para se comunicar com seu locatário de Azure Active Directory B2C (Azure AD B2C) para criar um usuário ou ler um perfil de usuário. Um perfil técnico pode ser autodeclarado para permitir a interação com o usuário. Por exemplo, um perfil técnico pode coletar a credencial do usuário para entrar e, em seguida, renderizar a página de inscrição ou a página de redefinição de senha.

## <a name="types-of-technical-profiles"></a>Tipos de perfis técnicos

Um perfil técnico permite esses tipos de cenários:

- [Application insights](analytics-with-application-insights.md): envia dados de evento para [Application insights](../azure-monitor/app/app-insights-overview.md).
- [AD do Azure](active-directory-technical-profile.md): fornece suporte para o gerenciamento de usuário do Azure ad B2C.
- [Autenticação multifator do Azure ad](multi-factor-auth-technical-profile.md): fornece suporte para verificar um número de telefone usando a autenticação multifator do Azure AD.
- [Transformação de declarações](claims-transformation-technical-profile.md): chama as transformações de declarações de saída para manipular valores de declarações, validar declarações ou definir valores padrão para um conjunto de declarações de saída.
- [Dica de token de ID](id-token-hint.md): valida a `id_token_hint` assinatura de token JWT, o nome do emissor e o público-alvo do token e extrai a declaração do token de entrada.
- [Emissor do token JWT](jwt-issuer-technical-profile.md): emite um token JWT retornado para o aplicativo de terceira parte confiável.
- [OAuth1](oauth1-technical-profile.md): Federação com qualquer provedor de identidade do protocolo OAuth 1,0.
- [OAuth2](oauth2-technical-profile.md): Federação com qualquer provedor de identidade do protocolo OAuth 2,0.
- [Senha de uso único](one-time-password-technical-profile.md): fornece suporte para gerenciar a geração e a verificação de uma senha de uso único.
- [OpenID Connect](openid-connect-technical-profile.md): Federação com qualquer provedor de identidade de protocolo OpenID Connect.
- [Fator de telefone](phone-factor-technical-profile.md): dá suporte ao registro e à verificação de números de telefone.
- [Provedor RESTful](restful-technical-profile.md): chama serviços de API REST, como validação de entrada do usuário, enriquecimento de dados do usuário ou integração com aplicativos de linha de negócios.
- [Provedor de identidade SAML](identity-provider-generic-saml.md): Federação com qualquer provedor de identidade de protocolo SAML.
- [Emissor de token SAML](saml-service-provider.md): emite um token SAML retornado para o aplicativo de terceira parte confiável.
- [Auto-declaração](self-asserted-technical-profile.md): interage com o usuário. Por exemplo, o coleta a credencial do usuário para entrar, renderizar a página de inscrição ou redefinir a senha.
- [Gerenciamento de sessão](custom-policy-reference-sso.md): lida com diferentes tipos de sessões.

## <a name="technical-profile-flow"></a>Fluxo do perfil técnico

Todos os tipos de perfis técnicos compartilham o mesmo conceito. Eles começam lendo as declarações de entrada e executam transformações de declarações. Em seguida, eles se comunicam com a parte configurada, como um provedor de identidade, uma API REST ou os serviços de diretório do Azure AD. Depois que o processo for concluído, o perfil técnico retornará as declarações de saída e poderá executar transformações de declarações de saída. O diagrama a seguir mostra como as transformações e mapeamentos referenciados no perfil técnico são processados. Depois que a transformação de declarações é executada, as declarações de saída são armazenadas imediatamente no recipiente de declarações, independentemente da parte com a qual o perfil técnico interage.

![Diagrama que ilustra o fluxo de perfil técnico.](./media/technical-profiles/technical-profile-flow.png)

1. **Gerenciamento de sessão de logon único (SSO)**: restaura o estado de sessão do perfil técnico usando o [Gerenciamento de sessão de SSO](custom-policy-reference-sso.md).
1. **Transformação de declarações de entrada**: antes do perfil técnico ser iniciado, Azure ad B2C executa a [transformação de declarações](claimstransformations.md)de entrada.
1. **Declarações de entrada**: as declarações são coletadas do recipiente de declarações que são usadas para o perfil técnico.
1. **Execução de perfil técnico**: o perfil técnico troca as declarações com a parte configurada. Por exemplo:
    - Redireciona o usuário para o provedor de identidade para concluir a entrada. Após conectar-se, o usuário retornará e prosseguirá com a execução do perfil técnico.
    - Chama uma API REST ao enviar parâmetros como InputClaims e obter informações de volta como OutputClaims.
    - Cria ou atualiza a conta de usuário.
    - Envia e verifica a mensagem de texto de autenticação multifator.
1. **Perfis técnicos de validação**: um [perfil técnico autodeclarado](self-asserted-technical-profile.md) pode chamar [perfis técnicos de validação](validation-technical-profile.md) para validar os dados profiledos pelo usuário.
1. **Declarações de saída**: as declarações são retornadas de volta para o recipiente de declarações. Você pode usar essas declarações na próxima etapa de orquestrações ou nas transformações de declarações de saída.
1. **Transformações de declarações de saída**: depois que o perfil técnico for concluído, Azure ad B2C executará as [transformações de declarações](claimstransformations.md)de saída.
1. **Gerenciamento de sessão de SSO**: persiste os dados do perfil técnico para a sessão usando o [Gerenciamento de sessão de SSO](custom-policy-reference-sso.md).

Um elemento **TechnicalProfiles** contém um conjunto de perfis técnicos com suporte pelo provedor de declarações. Cada provedor de declarações deve ter pelo menos um perfil técnico. O perfil técnico determina os pontos de extremidade e os protocolos necessários para se comunicar com o provedor de declarações. Um provedor de declarações pode ter vários perfis técnicos.

```xml
<ClaimsProvider>
  <DisplayName>Display name</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Technical profile identifier">
      <DisplayName>Display name of technical profile</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        ...
      </Metadata>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

O elemento **TechnicalProfile** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
|---------|---------|---------|
| Id | Sim | Um identificador exclusivo do perfil técnico. O perfil técnico pode ser referenciado usando esse identificador de outros elementos no arquivo de política. Os exemplos são **OrchestrationSteps** e **ValidationTechnicalProfile**. |

O elemento **TechnicalProfile** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Domínio | 0:1 | O nome de domínio do perfil técnico. Por exemplo, se o perfil técnico especificar o provedor de identidade Facebook, o nome de domínio será Facebook.com. |
| DisplayName | 1:1 | O nome de exibição do perfil técnico. |
| Descrição | 0:1 | A descrição do perfil técnico. |
| Protocolo | 1:1 | O protocolo usado para a comunicação com a outra parte. |
| Metadados | 0:1 | Um conjunto de chaves e valores que controla o comportamento do perfil técnico. |
| InputTokenFormat | 0:1 | O formato do token de entrada. Os valores possíveis são `JSON`, `JWT`, `SAML11` ou `SAML2`. O `JWT` valor representa um token Web JSON de acordo com a especificação IETF. O `SAML11` valor representa um token de segurança SAML 1,1 de acordo com a especificação Oasis. O `SAML2` valor representa um token de segurança SAML 2,0 de acordo com a especificação Oasis. |
| OutputTokenFormat | 0:1 | O formato do token de saída. Os valores possíveis são `JSON`, `JWT`, `SAML11` ou `SAML2`. |
| CryptographicKeys | 0:1 | Uma lista de chaves de criptografia que são usadas no perfil técnico. |
| InputClaimsTransformations | 0:1 | Uma lista de referências definidas previamente a transformações de declarações que devem ser executadas antes que uma declaração seja enviada ao provedor de declarações ou à terceira parte confiável. |
| InputClaims | 0:1 | Uma lista de referências definidas anteriormente para tipos de declaração que são usados como entrada no perfil técnico. |
| PersistedClaims | 0:1 | Uma lista de referências definidas anteriormente para tipos de declaração que serão persistidas pelo perfil técnico. |
| DisplayClaims | 0:1 | Uma lista de referências definidas anteriormente para tipos de declaração que são apresentados pelo [perfil técnico autodeclarado](self-asserted-technical-profile.md). O recurso DisplayClaims está atualmente em visualização. |
| OutputClaims | 0:1 | Uma lista de referências definidas anteriormente para tipos de declaração que são usados como saída no perfil técnico. |
| OutputClaimsTransformations | 0:1 | Uma lista de referências definidas previamente a transformações de declarações que devem ser executadas depois que as declarações são recebidas do provedor de declarações. |
| ValidationTechnicalProfiles | 0:n | Uma lista de referências a outros perfis técnicos que o perfil técnico usa para fins de validação. Para obter mais informações, consulte [validação de perfil técnico](validation-technical-profile.md).|
| SubjectNamingInfo | 0:1 | Controla a produção do nome da entidade em tokens, em que o nome da entidade é especificado separadamente das declarações. Os exemplos são OAuth ou SAML. |
| IncludeInSso | 0:1 | Se o uso desse perfil técnico deve aplicar o comportamento de SSO para a sessão ou, em vez disso, exigir interação explícita. Esse elemento é válido somente em perfis SelfAsserted usados em um perfil técnico de validação. Os valores possíveis são `true` (padrão) ou `false`. |
| IncludeClaimsFromTechnicalProfile | 0:1 | Um identificador de um perfil técnico do qual todas as declarações de entrada e saídas devem ser adicionadas a esse perfil técnico. O perfil técnico referenciado precisa ser definido no mesmo arquivo de política. |
| IncludeTechnicalProfile |0:1 | Um identificador de um perfil técnico do qual todos os dados devem ser adicionados a esse perfil técnico. |
| UseTechnicalProfileForSessionManagement | 0:1 | Um perfil técnico diferente a ser usado para gerenciamento de sessão. |
|EnabledForUserJourneys| 0:1 |Controla se o perfil técnico é executado em um percurso do usuário. |

## <a name="protocol"></a>Protocolo

O elemento **Protocol** especifica o protocolo a ser usado para a comunicação com a outra entidade. O elemento **Protocol** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Nome | Sim | O nome de um protocolo válido com suporte pelo Azure AD B2C usado como parte do perfil técnico. Os valores possíveis são,,,, `OAuth1` `OAuth2` `SAML2` `OpenIdConnect` `Proprietary` ou `None` . |
| Manipulador | Não | Quando o nome do protocolo é definido como `Proprietary` , especifica o nome do assembly que é usado pelo Azure ad B2C para determinar o manipulador de protocolo. |

## <a name="metadata"></a>Metadados

O elemento **Metadata** contém as opções de configuração relevantes para um protocolo específico. A lista de metadados com suporte é documentada na especificação de [perfil técnico](#types-of-technical-profiles) correspondente. Um elemento de **metadados** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Item | 0:n | Os metadados que se relacionam ao perfil técnico. Cada tipo de perfil técnico tem um conjunto diferente de itens de metadados. Para obter mais informações, consulte a seção tipos de perfil técnico. |

### <a name="item"></a>Item

O elemento **Item** do elemento **Metadata** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Chave | Sim | A chave de metadados. Consulte cada [tipo de perfil técnico](#types-of-technical-profiles) para a lista de itens de metadados. |

O exemplo a seguir ilustra o uso de metadados relevantes para o [perfil técnico do OAuth2](oauth2-technical-profile.md#metadata).

```xml
<TechnicalProfile Id="Facebook-OAUTH">
  ...
  <Metadata>
    <Item Key="ProviderName">facebook</Item>
    <Item Key="authorization_endpoint">https://www.facebook.com/dialog/oauth</Item>
    <Item Key="AccessTokenEndpoint">https://graph.facebook.com/oauth/access_token</Item>
    <Item Key="HttpBinding">GET</Item>
    <Item Key="UsePolicyInRedirectUri">0</Item>
    ...
  </Metadata>
  ...
</TechnicalProfile>
```

O exemplo a seguir ilustra o uso de metadados relevantes para o [perfil técnico da API REST](restful-technical-profile.md#metadata).

```xml
<TechnicalProfile Id="REST-Validate-Email">
  ...
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    ...
  </Metadata>
  ...
</TechnicalProfile>
```

## <a name="cryptographic-keys"></a>Chaves criptográficas

Para estabelecer confiança com os serviços com os quais ele se integra, o Azure AD B2C armazena segredos e certificados na forma de [chaves de política](policy-keys-overview.md). Durante a execução do perfil técnico, Azure AD B2C recupera as chaves de criptografia de Azure AD B2C chaves de política. Em seguida, Azure AD B2C usa as chaves para estabelecer a confiança ou criptografar ou assinar um token. Essas relações de confiança consistem em:

- Federação com provedores de identidade [OAuth1](oauth1-technical-profile.md#cryptographic-keys), [OAuth2](oauth2-technical-profile.md#cryptographic-keys)e [SAML](identity-provider-generic-saml.md) .
- Protegendo a conexão com os [serviços de API REST](secure-rest-api.md).
- Assinar e criptografar os tokens [JWT](jwt-issuer-technical-profile.md#cryptographic-keys) e [SAML](saml-service-provider.md) .

O elemento **CryptographicKeys** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Chave | 1:n | Uma chave de criptografia usada neste perfil técnico. |

### <a name="key"></a>Chave

O elemento **Key** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Id | Não | Um identificador exclusivo de um par de chaves específico referenciado por outros elementos no arquivo de política. |
| StorageReferenceId | Sim | Um identificador de um contêiner de chave de armazenamento referenciado por outros elementos no arquivo de política. |

## <a name="input-claims-transformations"></a>Transformações de declarações de entrada

O elemento **InputClaimsTransformations** pode conter uma coleção de elementos de transformação de declarações de entrada que são usados para modificar as declarações de entrada ou gerar novas.

As declarações de saída de uma transformação de declarações anterior na coleção de transformação de declarações podem ser declarações de entrada de uma transformação de declarações de entrada subsequente. Dessa forma, você pode ter uma sequência de transformações de declarações que dependem umas das outras.

O elemento **InputClaimsTransformations** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1:n | O identificador de uma transformação de declarações que deve ser executado antes que uma declaração seja enviada ao provedor de declarações ou à terceira parte confiável. Uma transformação de declarações pode ser usada para modificar as declarações de ClaimsSchema existentes ou gerar novas declarações. |

### <a name="inputclaimstransformation"></a>InputClaimsTransformation

O elemento **InputClaimsTransformation** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Um identificador de uma transformação de declarações já definido no arquivo de política ou no arquivo de política pai. |

Os perfis técnicos a seguir fazem referência à transformação de declarações **CreateOtherMailsFromEmail** . A transformação declarações adiciona o valor da `email` declaração à `otherMails` coleção antes de persistir os dados para o diretório.

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  ...
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  ...
</TechnicalProfile>
```

## <a name="input-claims"></a>Declarações de entrada

O elemento **InputClaims** pega as declarações do recipiente de declarações que são usadas para o perfil técnico. Por exemplo, um [perfil técnico autodeclarado](self-asserted-technical-profile.md) usa as declarações de entrada para preencher antecipadamente as declarações de saída fornecidas pelo usuário. Um perfil técnico da API REST usa as declarações de entrada para enviar parâmetros de entrada ao ponto de extremidade da API REST. O Azure AD usa uma declaração de entrada como um identificador exclusivo para ler, atualizar ou excluir uma conta.

O elemento **InputClaims** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| InputClaim | 1:n | Um tipo de declaração de entrada esperado. |

### <a name="inputclaim"></a>InputClaim

O elemento **InputClaim** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Sim | O identificador de um tipo de declaração. A declaração já está definida na seção de esquema de declarações no arquivo de política ou no arquivo de política pai. |
| DefaultValue | Não | Um valor padrão a ser usado para criar uma declaração se a declaração indicada por ClaimTypeReferenceId não existir para que a declaração resultante possa ser usada como um elemento InputClaim pelo perfil técnico. |
| PartnerClaimType | Não | O identificador do tipo de declaração do parceiro externo para o qual o tipo de declaração da política especificado é mapeado. Se o atributo PartnerClaimType não for especificado, o tipo de declaração de política especificado será mapeado para o tipo de declaração de parceiro do mesmo nome. Use essa propriedade quando seu nome do tipo de declaração for diferente do da outra parte. Um exemplo é se o primeiro nome de declaração for *determinado*, enquanto o parceiro usa uma declaração chamada *first_name*. |

## <a name="display-claims"></a>Exibir declarações

O elemento **DisplayClaims** contém uma lista de declarações a serem apresentadas na tela para coletar dados do usuário. Na coleção exibir declarações, você pode incluir uma referência a um [tipo de declaração](claimsschema.md) ou a um [controle de exibição](display-controls.md) que você criou.

- Um tipo de declaração é uma referência a uma declaração a ser exibida na tela.
  - Para forçar o usuário a fornecer um valor para uma declaração específica, defina o atributo **Required** do elemento **DisplayClaim** como `true` .
  - Para preencher previamente os valores de declarações de exibição, use as declarações de entrada que foram descritas anteriormente. O elemento também pode conter um valor padrão.
  - O elemento **ClaimType** na coleção **DisplayClaims** precisa definir o elemento **userinputtype** como qualquer tipo de entrada de usuário com suporte pelo Azure ad B2C. Os exemplos são `TextBox` ou `DropdownSingleSelect` .
- Um controle de exibição é um elemento de interface do usuário que tem funcionalidade especial e interage com o serviço de back-end Azure AD B2C. Ele permite que o usuário execute ações na página que invocam um perfil técnico de validação no back-end. Um exemplo é verificar um endereço de email, número de telefone ou número de fidelidade do cliente.

A ordem dos elementos em **DisplayClaims** especifica a ordem na qual Azure ad B2C renderiza as declarações na tela.

O elemento **DisplayClaims** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| DisplayClaim | 1:n | Um tipo de declaração de entrada esperado. |

### <a name="displayclaim"></a>DisplayClaim

O elemento **DisplayClaim** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Não | O identificador de um tipo de declaração já definido na seção ClaimsSchema no arquivo de política ou no arquivo de política pai. |
| DisplayControlReferenceId | Não | O identificador de um [controle de exibição](display-controls.md) já definido na seção de ClaimsSchema no arquivo de política ou de política pai. |
| Obrigatório | No | Indica se a declaração de exibição é necessária. |

O exemplo a seguir ilustra o uso de declarações de exibição e controles de exibição em um perfil técnico autodeclarado.

![Captura de tela que mostra um perfil técnico autodeclarado com declarações de exibição.](./media/technical-profiles/display-claims.png)

No seguinte perfil técnico:

- A primeira declaração de exibição faz uma referência ao `emailVerificationControl` controle de exibição, que coleta e verifica o endereço de email.
- A quinta declaração de exibição faz uma referência ao `phoneVerificationControl` controle de exibição, que coleta e verifica um número de telefone.
- As outras declarações de exibição são elementos ClaimType a serem coletados do usuário.

```xml
<TechnicalProfile Id="Id">
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim DisplayControlReferenceId="phoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
</TechnicalProfile>
```

## <a name="persisted-claims"></a>Declarações persistentes

O elemento **PersistedClaims** contém todos os valores que devem ser persistidos por um [perfil técnico do Azure ad](active-directory-technical-profile.md) com possíveis informações de mapeamento entre um tipo de declaração já definido na seção [ClaimsSchema](claimsschema.md) na política e o nome de atributo do Azure AD.

O nome da declaração é o nome do atributo do [Azure ad](user-profile-attributes.md) , a menos que o atributo **PartnerClaimType** seja especificado, que contém o nome do atributo do Azure AD.

O elemento **PersistedClaims** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| PersistedClaim | 1:n | O tipo de declaração que deverá persistir. |

### <a name="persistedclaim"></a>PersistedClaim

O elemento **PersistedClaim** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Sim | O identificador de um tipo de declaração já definido na seção ClaimsSchema no arquivo de política ou no arquivo de política pai. |
| DefaultValue | Não | Um valor padrão a ser usado para criar uma declaração se a declaração não existir. |
| PartnerClaimType | Não | O identificador do tipo de declaração do parceiro externo para o qual o tipo de declaração da política especificado é mapeado. Se o atributo PartnerClaimType não for especificado, o tipo de declaração de política especificado será mapeado para o tipo de declaração de parceiro do mesmo nome. Use essa propriedade quando seu nome do tipo de declaração for diferente do da outra parte. Um exemplo é se o primeiro nome de declaração for *determinado*, enquanto o parceiro usa uma declaração chamada *first_name*. |

No exemplo a seguir, o perfil técnico do **AAD-UserWriteUsingLogonEmail** ou o [pacote de início](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts), que cria uma nova conta local, persiste as seguintes declarações:

```xml
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
  <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
  <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
  <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />
  <PersistedClaim ClaimTypeReferenceId="givenName" />
  <PersistedClaim ClaimTypeReferenceId="surname" />
</PersistedClaims>
```

## <a name="output-claims"></a>Declarações de saída

O elemento **OutputClaims** é uma coleção de declarações que são retornadas para o recipiente de declarações depois que o perfil técnico é concluído. Você pode usar essas declarações na próxima etapa de orquestrações ou nas transformações de declarações de saída. O elemento **OutputClaims** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| OutputClaim | 1:n | Um tipo de declaração de saída esperado. |

### <a name="outputclaim"></a>OutputClaim

O elemento **OutputClaim** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Sim | O identificador de um tipo de declaração já definido na seção ClaimsSchema no arquivo de política ou no arquivo de política pai. |
| DefaultValue | Não | Um valor padrão a ser usado para criar uma declaração se a declaração não existir. |
|AlwaysUseDefaultValue |Não |Força o uso do valor padrão. |
| PartnerClaimType | Não | O identificador do tipo de declaração do parceiro externo para o qual o tipo de declaração da política especificado é mapeado. Se o atributo de tipo de declaração de parceiro não for especificado, o tipo de declaração de política especificado será mapeado para o tipo de declaração de parceiro do mesmo nome. Use essa propriedade quando seu nome do tipo de declaração for diferente do da outra parte. Um exemplo é se o primeiro nome de declaração for *determinado*, enquanto o parceiro usa uma declaração chamada *first_name*. |

## <a name="output-claims-transformations"></a>Transformações de declarações de saída

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** . As transformações de declarações de saída são usadas para modificar as declarações de saída ou gerar novas. Após a execução, as declarações de saída são recolocadas no recipiente de declarações. Você pode usar essas declarações na próxima etapa de orquestrações.

As declarações de saída de uma transformação de declarações anterior na coleção de transformação de declarações podem ser declarações de entrada de uma transformação de declarações de entrada subsequente. Dessa forma, você pode ter uma sequência de transformações de declarações que dependem umas das outras.

O elemento **OutputClaimsTransformations** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1:n | Os identificadores de transformações de declarações que devem ser executados antes que uma declaração seja enviada ao provedor de declarações ou à terceira parte confiável. Uma transformação de declarações pode ser usada para modificar as declarações de ClaimsSchema existentes ou gerar novas declarações. |

### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

O elemento **OutputClaimsTransformation** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Um identificador de uma transformação de declarações já definido no arquivo de política ou no arquivo de política pai. |

O seguinte perfil técnico faz referência à transformação de declarações AssertAccountEnabledIsTrue para avaliar se a conta está habilitada ou não depois de ler a `accountEnabled` declaração do diretório.

```xml
<TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
  ...
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="accountEnabled" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
  </OutputClaims>
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
  ...
</TechnicalProfile>
```

## <a name="validation-technical-profiles"></a>Perfis técnicos de validação

Um perfil técnico de validação é usado para validar as declarações de saída em um [perfil técnico autodeclarado](self-asserted-technical-profile.md#validation-technical-profiles). Um perfil técnico de validação é um perfil técnico comum de qualquer protocolo, como o [Azure ad](active-directory-technical-profile.md) ou uma [API REST](restful-technical-profile.md). O perfil técnico de validação retorna declarações de saída ou retorna o código de erro. A mensagem de erro é renderizada para o usuário na tela, o que permite que o usuário tente novamente.

O diagrama a seguir ilustra como o Azure AD B2C usa um perfil técnico de validação para validar as credenciais do usuário.

![Diagrama que mostra um fluxo de perfil técnico de validação.](./media/technical-profiles/validation-technical-profile.png)

O elemento **ValidationTechnicalProfiles** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | Os identificadores dos perfis técnicos usados validam algumas ou todas as declarações de saída do perfil técnico de referência. Todas as declarações de entrada do perfil técnico referenciado precisam aparecer nas declarações de saída do perfil técnico de referência. |

### <a name="validationtechnicalprofile"></a>ValidationTechnicalProfile

O elemento **ValidationTechnicalProfile** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Um identificador de um perfil técnico já definido no arquivo de política ou no arquivo de política pai. |

## <a name="subjectnaminginfo"></a>SubjectNamingInfo

O elemento **SubjectNamingInfo** define o nome da entidade usada em tokens em uma política de terceira [parte confiável](relyingparty.md#subjectnaminginfo). O elemento **SubjectNamingInfo** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ClaimType | Sim | Um identificador de um tipo de declaração já definido na seção ClaimsSchema no arquivo de política. |

## <a name="include-technical-profile"></a>Incluir perfil técnico

Um perfil técnico pode incluir outro perfil técnico para alterar as configurações ou adicionar uma nova funcionalidade. O elemento **IncludeTechnicalProfile** é uma referência ao perfil técnico comum do qual um perfil técnico é derivado. Para reduzir a redundância e a complexidade de seus elementos de política, use a inclusão quando tiver vários perfis técnicos que compartilham os elementos principais. Use um perfil técnico comum com o conjunto comum de configuração, juntamente com perfis técnicos de tarefas específicos que incluem o perfil técnico comum.

Suponha que você tenha um [perfil técnico de API REST](restful-technical-profile.md) com um único ponto de extremidade onde você precisa enviar diferentes conjuntos de declarações para cenários diferentes. Crie um perfil técnico comum com a funcionalidade compartilhada, como o URI do ponto de extremidade da API REST, os metadados, o tipo de autenticação e as chaves de criptografia. Crie perfis técnicos de tarefas específicos que incluam o perfil técnico comum. Em seguida, adicione as declarações de entrada e saída ou substitua o URI de ponto de extremidade da API REST relevante para esse perfil técnico.

O elemento **IncludeTechnicalProfile** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Um identificador de um perfil técnico já definido no arquivo de política ou no arquivo de política pai. |

O exemplo a seguir ilustra o uso da inclusão:

- **REST-API-comum**: um perfil técnico comum com a configuração básica.
- **REST-ValidateProfile**: inclui o perfil técnico **REST-API-comum** e especifica as declarações de entrada e saída.
- **REST-UpdateProfile**: inclui o perfil técnico **REST-API-comum** , especifica as declarações de entrada e substitui os `ServiceUrl` metadados.

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-API-Common">
      <DisplayName>Base REST API configuration</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="SendClaimsIn">Body</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
      </CryptographicKeys>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Validate the account and return promo code</DisplayName>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="REST-API-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="REST-UpdateProfile">
       <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/update</Item>
      </Metadata>
      <DisplayName>Update the user profile</DisplayName>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="email" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="REST-API-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

### <a name="multilevel-inclusion"></a>Inclusão de vários níveis

Um perfil técnico pode incluir um único perfil técnico. Não há limite para o número de níveis de inclusão. Por exemplo, o perfil técnico **AAD-UserReadUsingAlternativeSecurityId-NOERROR** inclui o **AAD-UserReadUsingAlternativeSecurityId**. Este perfil técnico define o `RaiseErrorIfClaimsPrincipalDoesNotExist` item de metadados como `true` e gerará um erro se uma conta social não existir no diretório. O **AAD-UserReadUsingAlternativeSecurityId-NOERROR** substitui esse comportamento e desabilita essa mensagem de erro.

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId** inclui o perfil técnico `AAD-Common`.

```xml
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

O **AAD-UserReadUsingAlternativeSecurityId-NOERROR** e o **AAD-UserReadUsingAlternativeSecurityId** não especificam o elemento de **protocolo** necessário porque ele está especificado no perfil técnico do **AAD-comum** .

```xml
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

## <a name="use-technical-profile-for-session-management"></a>Usar perfil técnico para gerenciamento de sessão

O elemento **UseTechnicalProfileForSessionManagement** referencia o [perfil técnico da sessão de SSO](custom-policy-reference-sso.md). O elemento **UseTechnicalProfileForSessionManagement** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Um identificador de um perfil técnico já definido no arquivo de política ou no arquivo de política pai. |

## <a name="enabled-for-user-journeys"></a>Habilitado para viagens do usuário

O [ClaimsProviderSelections](userjourneys.md#claims-provider-selection) em um percurso do usuário define a lista de opções de seleção de provedor de declarações e sua ordem. Com o elemento **EnabledForUserJourneys** , você filtra qual provedor de declarações está disponível para o usuário. O elemento **EnabledForUserJourneys** contém um dos seguintes valores:

- **Sempre**: executa o perfil técnico.
- **Nunca**: ignora o perfil técnico.
- **OnClaimsExistence**: executa somente quando uma determinada declaração especificada no perfil técnico existir.
- **OnItemExistenceInStringCollectionClaim**: executa somente quando um item existe em uma declaração de coleção de cadeia de caracteres.
- **OnItemAbsenceInStringCollectionClaim**: executa somente quando um item não existe em uma declaração de coleção de cadeia de caracteres.

Usar **OnClaimsExistence**, **OnItemExistenceInStringCollectionClaim** ou **OnItemAbsenceInStringCollectionClaim** exige que você forneça os seguintes metadados:

- **ClaimTypeOnWhichToEnable**: especifica o tipo da declaração a ser avaliada.
- **ClaimValueOnWhichToEnable**: especifica o valor que deve ser comparado.

O seguinte perfil técnico é executado somente quando a coleção de cadeia de caracteres **identityProviders** contém o valor de `facebook.com`:

```xml
<TechnicalProfile Id="UnLink-Facebook-OAUTH">
  <DisplayName>Unlink Facebook</DisplayName>
...
    <Metadata>
      <Item Key="ClaimTypeOnWhichToEnable">identityProviders</Item>
      <Item Key="ClaimValueOnWhichToEnable">facebook.com</Item>
    </Metadata>
...
  <EnabledForUserJourneys>OnItemExistenceInStringCollectionClaim</EnabledForUserJourneys>
</TechnicalProfile>
```
