---
title: Configurar requisitos de complexidade de senha
titleSuffix: Azure AD B2C
description: Como configurar os requisitos de complexidade de senhas fornecidas pelos consumidores no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/12/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 81c6e58e34f30d5736c40c77a308321dee28ae34
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103224258"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Configurar os requisitos de complexidade de senhas fornecidas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

O Azure Active Directory B2C (Azure AD B2C) oferece suporte à alteração de requisitos de complexidade para senhas fornecidas por um usuário final ao criar uma conta. Por padrão, o Azure AD B2C usa senhas **Fortes**. O Azure AD B2C também oferece suporte a opções de configuração para controlar a complexidade de senhas que os clientes podem usar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="password-rule-enforcement"></a>Imposição de regras de senha

Durante a inscrição ou redefinição de senha, um usuário final deve fornecer uma senha que atenda às regras de complexidade. Regras de complexidade de senha são impostas pelo fluxo de usuários. É possível ter um fluxo de usuário que exija um PIN de quatro dígitos durante a inscrição, enquanto outro fluxo de usuário requer uma cadeia de oito caracteres durante a inscrição. Por exemplo, você pode usar um fluxo de usuários com diferente complexidade de senha para adultos e crianças.

A complexidade de senha nunca é aplicada durante a inscrição. Nunca será solicitado que os usuários mudem sua senha ao entrar por ela não atender aos requisitos de complexidade atual.

A complexidade da senha pode ser configurada nos seguintes tipos de fluxos de usuário:

- Fluxo de usuário de inscrição ou entrada
- Fluxo de usuário de redefinição de senha

Se estiver usando políticas personalizadas, você poderá ([configurar a complexidade da senha em uma política personalizada](password-complexity.md)).

## <a name="configure-password-complexity"></a>Configurar a complexidade de senha

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.
3. No portal do Azure, pesquise e selecione **Azure AD B2C**.
4. Escolha **Fluxos de usuário**.
2. Selecione um fluxo de usuário e clique em **Propriedades**.
3. Em **Complexidade da senha**, altere a complexidade da senha para esse fluxo de usuário para **Simples**, **Forte** ou **Personalizada**.

### <a name="comparison-chart"></a>Gráfico de comparação

| Complexidade | Descrição |
| --- | --- |
| Simples | Uma senha que tenha 8 a 64 caracteres. |
| Forte | Uma senha que tenha 8 a 64 caracteres. São necessários de 3 a 4 caracteres minúsculos, maiúsculos, números ou símbolos. |
| Personalizado | Essa opção fornece mais controle sobre as regras de complexidade de senha.  Ela permite configurar um tamanho personalizado.  E permite aceitar somente senhas numéricas (pins). |

## <a name="custom-options"></a>Opções personalizadas

### <a name="character-set"></a>Conjunto de caracteres

Permite que você aceite somente dígitos (pins) ou o conjunto completo de caracteres.

- **Somente números** permite somente dígitos (0-9) ao digitar uma senha.
- **Todos** permite qualquer letra, número ou símbolo.

### <a name="length"></a>Comprimento

Permite que você controle as exigências de comprimento da senha.

- **Comprimento mínimo** deve ser pelo menos 4.
- O **comprimento máximo** deve ser maior ou igual ao tamanho mínimo e, no máximo, pode ser de 256 caracteres.

### <a name="character-classes"></a>Classes de caracteres

Permite que você controle os diferentes tipos de caracteres usados na senha.

- **2 de 4: caracteres maiúsculos, minúsculos, números (0-9) ou símbolos** a senha deve ter, no mínimo, dois tipos de caracteres. Por exemplo, um número e um caractere minúsculo.
- **3 de 4: caractere minúsculo, caractere maiúsculo, número (0-9), símbolo** garante que a senha contenha pelo menos três tipos de caracteres. Por exemplo, um número, um caractere minúsculo e um caractere maiúsculos.
- **4 de 4: caracteres maiúsculos, minúsculos, números (0-9) ou símbolos** a senha deve ter todos os tipos de caracteres.

    > [!NOTE]
    > Exigir **4 de 4** pode resultar em frustração do usuário final. Alguns estudos mostraram que esse requisito não melhora a entropia de senha. Confira as [Diretrizes de senha NIST](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="password-predicate-validation"></a>Validação de predicado de senha

Para configurar a complexidade da senha, substitua `newPassword` os `reenterPassword` [tipos de declaração](claimsschema.md) e por uma referência a [validações de predicado](predicates.md#predicatevalidations). O elemento PredicateValidations agrupa um conjunto de predicados para formar uma validação de entrada de usuário que pode ser aplicada a um tipo de declaração. Abra o arquivo de extensões da sua política. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.

1. Pesquise o elemento [BuildingBlocks](buildingblocks.md). Se o elemento não existir, adicione-o.
1. Localize o elemento [ClaimsSchema](claimsschema.md). Se o elemento não existir, adicione-o.
1. Adicione as `newPassword` `reenterPassword` declarações e ao elemento **ClaimsSchema** .

    ```xml
    <!-- 
    <BuildingBlocks>
      <ClaimsSchema> -->
        <ClaimType Id="newPassword">
          <PredicateValidationReference Id="CustomPassword" />
        </ClaimType>
        <ClaimType Id="reenterPassword">
          <PredicateValidationReference Id="CustomPassword" />
        </ClaimType>
      <!-- 
      </ClaimsSchema>
    </BuildingBlocks>-->
    ```

1. Os [predicados](predicates.md) definem uma validação básica para verificar o valor de um tipo de declaração e retorna true ou false. A validação é feita usando um elemento de método especificado e um conjunto de parâmetros relevantes para o método. Adicione os seguintes predicados ao elemento **BuildingBlocks** imediatamente após o fechamento do `</ClaimsSchema>` elemento:

    ```xml
    <!-- 
    <BuildingBlocks>-->
      <Predicates>
        <Predicate Id="LengthRange" Method="IsLengthRange">
          <UserHelpText>The password must be between 6 and 64 characters.</UserHelpText>
          <Parameters>
            <Parameter Id="Minimum">6</Parameter>
            <Parameter Id="Maximum">64</Parameter>
          </Parameters>
        </Predicate>
        <Predicate Id="Lowercase" Method="IncludesCharacters">
          <UserHelpText>a lowercase letter</UserHelpText>
          <Parameters>
            <Parameter Id="CharacterSet">a-z</Parameter>
          </Parameters>
        </Predicate>
        <Predicate Id="Uppercase" Method="IncludesCharacters">
          <UserHelpText>an uppercase letter</UserHelpText>
          <Parameters>
            <Parameter Id="CharacterSet">A-Z</Parameter>
          </Parameters>
        </Predicate>
        <Predicate Id="Number" Method="IncludesCharacters">
          <UserHelpText>a digit</UserHelpText>
          <Parameters>
            <Parameter Id="CharacterSet">0-9</Parameter>
          </Parameters>
        </Predicate>
        <Predicate Id="Symbol" Method="IncludesCharacters">
          <UserHelpText>a symbol</UserHelpText>
          <Parameters>
            <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
          </Parameters>
        </Predicate>
      </Predicates>
    <!-- 
    </BuildingBlocks>-->
    ```

1. Adicione as validações predicadas a seguir ao elemento **BuildingBlocks** imediatamente após o fechamento do `</Predicates>` elemento:

    ```xml
    <!-- 
    <BuildingBlocks>-->
      <PredicateValidations>
        <PredicateValidation Id="CustomPassword">
          <PredicateGroups>
            <PredicateGroup Id="LengthGroup">
              <PredicateReferences MatchAtLeast="1">
                <PredicateReference Id="LengthRange" />
              </PredicateReferences>
            </PredicateGroup>
            <PredicateGroup Id="CharacterClasses">
              <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
              <PredicateReferences MatchAtLeast="3">
                <PredicateReference Id="Lowercase" />
                <PredicateReference Id="Uppercase" />
                <PredicateReference Id="Number" />
                <PredicateReference Id="Symbol" />
              </PredicateReferences>
            </PredicateGroup>
          </PredicateGroups>
        </PredicateValidation>
      </PredicateValidations>
    <!-- 
    </BuildingBlocks>-->
    ```

## <a name="disable-strong-password"></a>Desabilitar senha forte 

Os perfis técnicos a seguir são [Active Directory perfis técnicos](active-directory-technical-profile.md), que lêem e gravam dados em Azure Active Directory. Substitua esses perfis técnicos no arquivo de extensão. Use `PersistedClaims` para desabilitar a política de senha forte. Localize o elemento **ClaimsProviders**.  Adicione os seguintes provedores de declaração da seguinte maneira:

```xml
<!-- 
<ClaimsProviders>-->
  <ClaimsProvider>
    <DisplayName>Azure Active Directory</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
        <PersistedClaims>
          <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
        </PersistedClaims>
      </TechnicalProfile>
      <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
        <PersistedClaims>
          <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
        </PersistedClaims>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
<!-- 
</ClaimsProviders>-->
```

Se você usar a política de [entrada baseada em nome de usuário](https://github.com/azure-ad-b2c/samples/tree/master/policies/username-signup-or-signin) , atualize os `AAD-UserWriteUsingLogonEmail` `AAD-UserWritePasswordUsingObjectId` perfis técnicos,, e `LocalAccountWritePasswordUsingObjectId` com a política *DisableStrongPassword* .

Salve o arquivo da política.

## <a name="test-your-policy"></a>Testar sua política

### <a name="upload-the-files"></a>Fazer upload dos arquivos

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Selecione **Estrutura de Experiência de Identidade**.
5. Na página de políticas personalizadas, clique em **Carregar Política**.
6. Selecione **substituir a política, se ela existir**, e, em seguida, procure e selecione o arquivo de *TrustFrameworkExtensions.xml* .
7. Clique em **Carregar**.

### <a name="run-the-policy"></a>Executar a política

1. Abra a política de inscrição ou entrada. Por exemplo, *B2C_1A_signup_signin*.
2. Para **Aplicativo**, selecione seu aplicativo que você registrou anteriormente. Para ver o token, a **URL de resposta** deve mostrar `https://jwt.ms`.
3. Clique em **Executar agora**.
4. Selecione **Inscrever-se agora**, insira um endereço de email e insira uma nova senha. Algumas restrições de senhas são apresentadas. Termine de inserir as informações do usuário e clique em **Criar**. Você deverá ver o conteúdo do token retornado.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [Configurar a alteração de senha no Azure Active Directory B2C](add-password-change-policy.md).
- Saiba mais sobre os [predicados](predicates.md) e elementos [PredicateValidations](predicates.md#predicatevalidations) na referência de IEF.


::: zone-end
