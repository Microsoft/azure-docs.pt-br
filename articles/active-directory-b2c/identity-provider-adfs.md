---
title: Adicionar AD FS como um provedor de identidade SAML usando políticas personalizadas
titleSuffix: Azure AD B2C
description: Configurar AD FS 2016 usando o protocolo SAML e políticas personalizadas no Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 3082c249b04b5efc71187dd03515bc8c875b7c2f
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102448584"
---
# <a name="add-ad-fs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Adicionar AD FS como um provedor de identidade SAML usando políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como habilitar a entrada para uma conta de usuário AD FS usando [políticas personalizadas](custom-policy-overview.md) no Azure Active Directory B2C (Azure ad B2C). Você habilita a entrada adicionando um provedor de [identidade SAML](identity-provider-generic-saml.md) a uma política personalizada.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-a-self-signed-certificate"></a>Crie um certificado autoassinado

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar o certificado em seu locatário do Azure AD B2C.

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém seu locatário do Azure AD B2C. Escolha o filtro **Diretório + assinatura** no menu superior e escolha o diretório que contém o locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Na página de Visão Geral, selecione **Estrutura de Experiência de Identidade**.
5. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
6. Para **Opções**, escolha `Upload`.
7. Insira um **Nome** para a chave de política. Por exemplo, `SAMLSigningCert`. O prefixo `B2C_1A_` será adicionado automaticamente ao nome da chave.
8. Procure e selecione o arquivo .pfx do certificado com a chave privada.
9. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicionar um provedor de declarações

Se desejar que os usuários entrem usando uma conta de AD FS, você precisará definir a conta como um provedor de declarações com o qual Azure AD B2C possa se comunicar por meio de um ponto de extremidade. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

Você pode definir uma conta de AD FS como um provedor de declarações adicionando-a ao elemento **ClaimsProviders** no arquivo de extensão da política. Para obter mais informações, consulte [definir um provedor de identidade SAML](identity-provider-generic-saml.md).

1. Abra *TrustFrameworkExtensions.xml*.
1. Localize o elemento **ClaimsProviders**. Se ele não existir, adicione-o sob o elemento raiz.
1. Adicione um novo **ClaimsProvider** da seguinte maneira:

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your AD FS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userPrincipalName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Substitua `your-AD-FS-domain` pelo nome do seu domínio de AD FS e substitua o valor da declaração de saída **identityprovider** pelo seu DNS (valor arbitrário que indica seu domínio).

1. Localize a seção `<ClaimsProviders>` e adicione o snippet XML a seguir. Se sua política já contiver o perfil técnico `SM-Saml-idp`, pule para a próxima etapa. Para saber mais, confira [gerenciamento de sessão de logon único](custom-policy-reference-sso.md).

    ```xml
    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-Saml-idp">
          <DisplayName>Session Management Provider</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IncludeSessionIndex">false</Item>
            <Item Key="RegisterServiceProviders">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Salve o arquivo.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="configure-an-ad-fs-relying-party-trust"></a>Configurar um AD FS confiança de terceira parte confiável

Para usar AD FS como um provedor de identidade no Azure AD B2C, você precisa criar uma AD FS terceira parte confiável com os metadados de SAML Azure AD B2C. O exemplo a seguir mostra um endereço de URL para os metadados do SAML de um perfil técnico do Azure AD B2C:

```
https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/your-policy/samlp/metadata?idptp=your-technical-profile
```

Substitua os seguintes valores:

- **seu locatário** com o nome do locatário, como Your-Tenant.onmicrosoft.com.
- **your-policy** pelo nome da política. Por exemplo, B2C_1A_signup_signin_adfs.
- **seu perfil técnico** com o nome do seu perfil técnico do provedor de identidade SAML. Por exemplo, Contoso-SAML2.

Abra um navegador e navegue até a URL. Certifique-se de digitar a URL correta e ter acesso ao arquivo de metadados XML. Para adicionar um novo objeto de confiança de terceira parte confiável usando o snap-in de Gerenciamento do AD FS e definir manualmente as configurações, execute o seguinte procedimento em um servidor de Federação. A associação em **Administradores** ou equivalente no computador local é o mínimo necessário para concluir este procedimento.

1. Em Gerenciador do Servidor, selecione **ferramentas** e, em seguida, selecione **Gerenciamento de AD FS**.
2. Selecione **Adicionar Relação de Confiança de Terceira Parte Confiável**.
3. Na **Página inicial**, escolha **Com reconhecimento de declaração** e, em seguida, clique em **Iniciar**.
4. Na página **Selecionar Fonte de Dados**, selecione **Importar dados sobre a terceira parte confiável publicados online ou em uma rede local**, forneça a URL de metadados do Azure AD B2C e clique em **Avançar**.
5. Na página **Especificar Nome para Exibição**, insira um **Nome de exibição**, em **Notas**, insira uma descrição para essa confiança de terceira parte confiável e clique em **Avançar**.
6. Na página **Escolher Política de Controle de Acesso**, selecione uma política e, em seguida, clique em **Avançar**.
7. Na página **Pronto para adicionar confiança**, revise as configurações e clique em **Avançar** para salvar as informações do objeto de confiança de terceira parte confiável.
8. Na página **Concluir**, clique em **Fechar**, essa ação exibe automaticamente a caixa de diálogo **Editar Regras de Declaração**.
9. Selecione **Adicionar Regra**.
10. Em **Modelo de regra de declaração**, selecione **Enviar atributos do LDAP como declarações**.
11. Forneça um **nome de Regra de declaração**. Para o **repositório de atributos**, selecione **selecionar Active Directory**, adicione as seguintes declarações e clique em **concluir** e em **OK**.

    | Atributo LDAP | Tipo de declaração de saída |
    | -------------- | ------------------- |
    | User-Principal-Name | userPrincipalName |
    | Sobrenome | family_name |
    | Given-Name | given_name |
    | E-Mail-Address | email |
    | Display-Name | name |

    Observe que esses nomes não serão exibidos na lista suspensa tipo de declaração de saída. Você precisa digitá-los manualmente no. (A lista suspensa é realmente editável).

12.  Com base no tipo de certificado, talvez seja necessário definir o algoritmo de HASH. Na janela de propriedades de confiança de terceira parte confiável (Demonstração B2C), selecione a guia **Avançado** e altere o **algoritmo de Secure hash** para `SHA-256`, e clique em **OK**.
13. Em Gerenciador do Servidor, selecione **ferramentas** e, em seguida, selecione **Gerenciamento de AD FS**.
14. Selecione a terceira parte confiável que você criou, selecione **Atualização dos metadados da federação** e, em seguida, clique em **Atualizar**.

## <a name="test-your-custom-policy"></a>Testar sua política personalizada

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.
1. No portal do Azure, pesquise e selecione **Azure AD B2C**.
1. Em **políticas**, selecione **estrutura de experiência de identidade**
1. Selecione a política de terceira parte confiável, por exemplo `B2C_1A_signup_signin` .
1. Para **aplicativo**, selecione um aplicativo Web que você [registrou anteriormente](tutorial-register-applications.md). A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Selecione o botão **executar agora** .
1. Na página inscrever-se ou entrar, selecione **contoso AD FS** para entrar com o provedor de identidade AD FS contoso.

Se o processo de entrada for bem-sucedido, seu navegador será redirecionado para `https://jwt.ms` , que exibe o conteúdo do token retornado por Azure ad B2C.

## <a name="troubleshooting-ad-fs-service"></a>Solucionando problemas de serviço AD FS  

AD FS está configurado para usar o log de aplicativos do Windows. Se você enfrentar desafios Configurando AD FS como um provedor de identidade SAML usando políticas personalizadas no Azure AD B2C, convém verificar o log de eventos do AD FS:

1. Na barra de **pesquisa** do Windows, digite **Visualizador de eventos** e, em seguida, selecione o **Visualizador de eventos** aplicativo de área de trabalho.
1. Para exibir o log de um computador diferente, clique com o botão direito do mouse em **Visualizador de Eventos (local)** . Selecione **Conectar a outro computador** e preencha os campos para completar a caixa de diálogo **Selecionar Computador**.
1. No **Visualizador de Eventos**, abra **Logs de aplicativos e serviços**.
1. Selecione **AD FS** e, em seguida, selecione **administrador**. 
1. Para exibir mais informações sobre um evento, clique duas vezes no evento.  

### <a name="saml-request-is-not-signed-with-expected-signature-algorithm-event"></a>A solicitação SAML não está assinada com o evento de algoritmo de assinatura esperado

Esse erro indica que a solicitação SAML enviada pelo Azure AD B2C não está assinada com o algoritmo de assinatura esperado configurado no AD FS. Por exemplo, a solicitação SAML é assinada com o algoritmo de assinatura `rsa-sha256` , mas o algoritmo de assinatura esperado é `rsa-sha1` . Para corrigir esse problema, verifique se os Azure AD B2C e AD FS estão configurados com o mesmo algoritmo de assinatura.

#### <a name="option-1-set-the-signature-algorithm-in-azure-ad-b2c"></a>Opção 1: definir o algoritmo de assinatura no Azure AD B2C  

Você pode configurar como assinar a solicitação SAML no Azure AD B2C. Os metadados [XmlSignatureAlgorithm](identity-provider-generic-saml.md) controlam o valor do `SigAlg` parâmetro (cadeia de caracteres de consulta ou parâmetro post) na solicitação SAML. O exemplo a seguir configura Azure AD B2C para usar o `rsa-sha256` algoritmo de assinatura.

```xml
<Metadata>
  <Item Key="WantsEncryptedAssertions">false</Item>
  <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
  <Item Key="XmlSignatureAlgorithm">Sha256</Item>
</Metadata>
```

#### <a name="option-2-set-the-signature-algorithm-in-ad-fs"></a>Opção 2: definir o algoritmo de assinatura no AD FS 

Como alternativa, você pode configurar o algoritmo de assinatura de solicitação SAML esperado no AD FS.

1. Em Gerenciador do Servidor, selecione **ferramentas** e, em seguida, selecione **Gerenciamento de AD FS**.
1. Selecione a **terceira parte** confiável que você criou anteriormente.
1. Selecione **Propriedades** e **Avançar**
1. Configure o **algoritmo de hash seguro** e selecione **OK** para salvar as alterações.

::: zone-end
