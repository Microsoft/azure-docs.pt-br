---
title: Configurar tokens - Azure Active Directory B2C | Microsoft Docs
description: Saiba como configurar o tempo de vida do token e as definições de compatibilidade no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a4299eff296d9795f8d256ff1236a8e8b4ad3e42
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585200"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Configurar tokens no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Neste artigo, você aprenderá a configurar o [tempo de vida e a compatibilidade de um token](tokens-overview.md) no Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="token-lifetime-behavior"></a>Comportamento do tempo de vida do token

Você pode configurar o tempo de vida do token, incluindo:

- **Tempos de vida do token de ID e acesso (minutos)** – o tempo de vida do token de portador OAuth 2,0 e tokens de ID. O padrão é 60 minutos (1 hora). O mínimo (inclusive) é de 5 minutos. O máximo (inclusivo) é de 1.440 minutos (24 horas).
- Tempo de **vida do token de atualização (dias)** – o período máximo antes do qual um token de atualização pode ser usado para adquirir um novo token de acesso, se o seu aplicativo tiver recebido o `offline_access` escopo. O padrão é 14 dias. O mínimo (inclusive) é de um dia. O máximo (inclusive) 90 dias.
- **Tempo de vida da janela deslizante de atualização** -o tipo de janela deslizante do token de atualização. `Bounded` indica que o token de atualização pode ser estendido como especificado no **tempo de vida útil (dias)**. `No expiry` indica que a duração da janela deslizante do token de atualização nunca expira.
- Duração do tempo de **vida (dias)** – após esse período, o usuário é forçado a autenticar-se, independentemente do período de validade do token de atualização mais recente adquirido pelo aplicativo. O valor deve ser maior ou igual ao valor de **tempo de vida do token de atualização** .

O diagrama a seguir mostra o comportamento do tempo de vida da janela deslizante do token de atualização.

![Tempo de vida do token de atualização](./media/configure-tokens/refresh-token-lifetime.png)

> [!NOTE]
> Aplicativos de página única usando o fluxo de código de autorização com PKCE sempre têm um tempo de vida de token de atualização de 24 horas. [Saiba mais sobre as implicações de segurança de tokens de atualização no navegador](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser).

## <a name="configure-token-lifetime"></a>Configurar tempo de vida do token

::: zone pivot="b2c-user-flow"

Para configurar o tempo de vida do token do fluxo de usuário:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém seu locatário do Azure AD B2C. Selecione o filtro **Diretório + assinatura** no menu superior e escolha o diretório que contém o locatário do Azure AD B2C.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Selecione **Fluxos de usuários (políticas)** .
1. Abra o fluxo de usuários criado anteriormente.
1. Selecione **Propriedades**.
1. Em **tempo de vida do token**, ajuste as propriedades para atender às necessidades do seu aplicativo.
1. Clique em **Save** (Salvar).

::: zone-end

::: zone pivot="b2c-custom-policy"

Para alterar as configurações de compatibilidade de token, defina os metadados do perfil técnico do [emissor do token](jwt-issuer-technical-profile.md) na extensão ou o arquivo de terceira parte confiável da política que você deseja impactar. O perfil técnico do emissor do token é semelhante ao seguinte exemplo:

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          <Item Key="token_lifetime_secs">3600</Item>
          <Item Key="id_token_lifetime_secs">3600</Item>
          <Item Key="refresh_token_lifetime_secs">1209600</Item>
          <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
          <!--<Item Key="allow_infinite_rolling_refresh_token">true</Item>-->
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

Os seguintes valores são definidos no exemplo anterior:

- tempos de vida de token de acesso de **token_lifetime_secs** (segundos). O padrão é 3.600 (1 hora). O mínimo é 300 (5 minutos). O máximo é 86.400 (24 horas). 
- tempos de vida do token de ID de **id_token_lifetime_secs** (segundos). O padrão é 3.600 (1 hora). O mínimo é 300 (5 minutos). O máximo é 86.400 (24 horas). 
- **refresh_token_lifetime_secs** Tempos de vida de token de atualização (segundos). O padrão é 120, 9600 (14 dias). O mínimo é 86.400 (24 horas). O máximo é 7.776.000 (90 dias). 
- tempo de vida da janela deslizante do token de atualização de **rolling_refresh_token_lifetime_secs** (segundos). O padrão é 7.776.000 (90 dias). O mínimo é 86.400 (24 horas). O máximo é 31.536.000 (365 dias). Se você não quiser impor um tempo de vida da janela deslizante, defina o valor de `allow_infinite_rolling_refresh_token` como `true` . 
- o tempo de vida da janela deslizante do token de atualização **allow_infinite_rolling_refresh_token** nunca expira. 

::: zone-end


## <a name="token-compatibility-settings"></a>Configurações de compatibilidade de token

Você pode configurar a compatibilidade de token, incluindo:

- **Emissor (ISS) Claim** -o formato de emissor do token de ID e acesso.
- **Declaração de Subject (sub)** -a entidade de segurança sobre a qual o token declara informações, como o usuário de um aplicativo. Esse valor é imutável e não pode ser reatribuído nem reutilizado. Ele pode ser usado para executar verificações de autorização com segurança, por exemplo, quando o token é usado para acessar um recurso. Por padrão, a declaração de entidade é preenchida com a ID de objeto do usuário no diretório.
- **Declaração representando o fluxo do usuário** – essa declaração identifica o fluxo do usuário que foi executado. Valores possíveis: `tfp` (padrão) ou `acr`.

::: zone pivot="b2c-user-flow"

Para definir suas configurações de compatibilidade de fluxo de usuário:

1. Selecione **Fluxos de usuários (políticas)** .
1. Abra o fluxo de usuários criado anteriormente.
1. Selecione **Propriedades**.
1. Em **configurações de compatibilidade de token**, ajuste as propriedades para atender às necessidades do seu aplicativo.
1. Clique em **Save** (Salvar).

::: zone-end

::: zone pivot="b2c-custom-policy"

Para alterar as configurações de compatibilidade de token, defina os metadados do perfil técnico do [emissor do token](jwt-issuer-technical-profile.md) na extensão ou o arquivo de terceira parte confiável da política que você deseja impactar. O perfil técnico do emissor do token é semelhante ao seguinte exemplo:

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          ...
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

- **Declaração iss (Issuer)** – a declaração iss (Issuer) é definida com o item de metadados **IssuanceClaimPattern**. Os valores aplicáveis são `AuthorityAndTenantGuid` e `AuthorityWithTfp`.
- **Configuração de declaração que representa a ID de política** – as opções para definir esse valor são `TFP` (política de confiança da estrutura) e `ACR` (referência do contexto de autenticação). `TFP` é o valor recomendado. Defina **AuthenticationContextReferenceClaimPattern** com o valor de `None`.

    No elemento **ClaimsSchema**, adicione esse elemento:

    ```xml
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    No elemento **OutputClaims**, adicione este elemento:

    ```xml
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    Para o ACR, remova o item **AuthenticationContextReferenceClaimPattern**.

- **Declaração sub (Subject)** – essa opção é padronizada para a ObjectID. Se você quer mudar essa configuração para `Not Supported`, substitua esta linha:

    ```xml
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    com esta linha:

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

::: zone-end

## <a name="provide-optional-claims-to-your-app"></a>Fornecer declarações opcionais para seu aplicativo

As declarações de aplicativo são valores que são retornados para o aplicativo. Atualize seu fluxo de usuário para conter as declarações desejadas.

::: zone pivot="b2c-user-flow"

1. Selecione **Fluxos de usuários (políticas)** .
1. Abra o fluxo de usuários criado anteriormente.
1. Selecione **Declarações do aplicativo**.
1. Escolha as declarações e os atributos que você deseja enviar de volta para seu aplicativo.
1. Clique em **Save** (Salvar).

::: zone-end

::: zone pivot="b2c-custom-policy"

As declarações de saída do [perfil técnico da política de terceira parte confiável](relyingparty.md#technicalprofile) são valores retornados para um aplicativo. A adição de declarações de saída emitirá as declarações para o token após um percurso de usuário bem-sucedido e será enviada para o aplicativo. Modifique o elemento de perfil técnico na seção terceira parte confiável para adicionar as declarações desejadas como uma declaração de saída.

1. Abra o arquivo de política personalizada. Por exemplo, SignUpOrSignin.xml.
1. Localize o elemento OutputClaims. Adicione o OutputClaim que você deseja incluir no token. 
1. Defina os atributos de declaração de saída. 

O exemplo a seguir adiciona a `accountBalance` declaração. A declaração accountBalance é enviada para o aplicativo como um saldo. 

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <!--Add the optional claims here-->
      <OutputClaim ClaimTypeReferenceId="accountBalance" DefaultValue="" PartnerClaimType="balance" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

O elemento OutputClaim contém os seguintes atributos:

- **ClaimTypeReferenceId** -o identificador de um tipo de declaração já definido na seção [ClaimsSchema](claimsschema.md) no arquivo de política ou no arquivo de política pai.
- **PartnerClaimType** -permite que você altere o nome da declaração no token. 
- **DefaultValue** -um valor padrão. Você também pode definir o valor padrão para um [resolvedor de declaração](claim-resolver-overview.md), como a ID do locatário.
- **AlwaysUseDefaultValue** -força o uso do valor padrão.

::: zone-end

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como [solicitar tokens de acesso](access-tokens.md).