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
ms.date: 12/07/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 767f60cae2f74f7e2a928253d45011bb6ceb5d0e
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97653836"
---
# <a name="add-ad-fs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Adicionar AD FS como um provedor de identidade SAML usando políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como habilitar a entrada para uma conta de usuário AD FS usando [políticas personalizadas](custom-policy-overview.md) no Azure Active Directory B2C (Azure ad B2C). Você habilita a credencial adicionando um [perfil técnico de provedor de identidade do SAML](saml-identity-provider-technical-profile.md) a uma política personalizada.

## <a name="prerequisites"></a>Pré-requisitos

- Conclua as etapas em [Introdução às políticas personalizadas no Azure Active Directory B2C](custom-policy-get-started.md).
- Certifique-se de que você tenha acesso a um arquivo. pfx de certificado com uma chave privada. Você pode gerar seu próprio certificado autoassinado e carregá-lo no Azure AD B2C. O Azure AD B2C usa esse certificado para assinar a solicitação SAML enviada ao seu provedor de identidade SAML. Para obter mais informações sobre como gerar um certificado, consulte [gerar um certificado de assinatura](identity-provider-salesforce-saml.md#generate-a-signing-certificate).
- Para que o Azure aceite a senha do arquivo. pfx, a senha deve ser criptografada com a opção TripleDES-SHA1 no utilitário de exportação do repositório de certificados do Windows, em oposição ao AES256-SHA256.

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar o certificado em seu locatário do Azure AD B2C.

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém seu locatário do Azure AD B2C. Escolha o filtro **Diretório + assinatura** no menu superior e escolha o diretório que contém o locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Na página de Visão Geral, selecione **Estrutura de Experiência de Identidade**.
5. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
6. Para **Opções**, escolha `Upload`.
7. Insira um **Nome** para a chave de política. Por exemplo, `ADFSSamlCert`. O prefixo `B2C_1A_` será adicionado automaticamente ao nome da chave.
8. Procure e selecione o arquivo .pfx do certificado com a chave privada.
9. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicionar um provedor de declarações

Se desejar que os usuários entrem usando uma conta de AD FS, você precisará definir a conta como um provedor de declarações com o qual Azure AD B2C possa se comunicar por meio de um ponto de extremidade. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

Você pode definir uma conta de AD FS como um provedor de declarações adicionando-a ao elemento **ClaimsProviders** no arquivo de extensão da política. Para obter mais informações, confira [definir um perfil técnico do provedor de identidade SAML](saml-identity-provider-technical-profile.md).

1. Abra *TrustFrameworkExtensions.xml*.
1. Localize o elemento **ClaimsProviders**. Se ele não existir, adicione-o sob o elemento raiz.
1. Adicione um novo **ClaimsProvider** da seguinte maneira:

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso AD FS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso AD FS</DisplayName>
          <Description>Login with your AD FS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlCert"/>
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

### <a name="upload-the-extension-file-for-verification"></a>Carregar o arquivo de extensão para verificação

Agora, você configurou sua política para que Azure AD B2C saiba como se comunicar com AD FS conta. Tente carregar o arquivo de extensão da política apenas para confirmar se ele não apresenta problemas até o momento.

1. Na página **Políticas Personalizadas** em seu locatário do Azure AD B2C, selecione **Carregar Política**.
2. Habilite **Substitua a política se ela existir** e, em seguida, navegue até o arquivo *TrustFrameworkExtensions.xml* e selecione-o.
3. Clique em **Carregar**.

> [!NOTE]
> A extensão do Visual Studio Code B2C usa "socialIdpUserId". Uma política social também é necessária para AD FS.
>

## <a name="register-the-claims-provider"></a>Registrar o provedor de declarações

Neste ponto, o provedor de identidade já foi definido, mas não está disponível em nenhuma das telas de inscrição ou de entrada. Para disponibilizá-lo, crie uma duplicata de um percurso de usuário de modelo existente e, em seguida, modifique-o para que ele também tenha o provedor de identidade AD FS.

1. Abra o arquivo *TrustFrameworkBase.xml* do starter pack.
2. Localize e copie todo o conteúdo do elemento **UserJourney** que inclui `Id="SignUpOrSignIn"`.
3. Abra o *TrustFrameworkExtensions.xml* e localize o elemento **UserJourneys**. Se o elemento não existir, adicione um.
4. Cole todo o conteúdo do elemento **UserJourney** que você copiou como filho do elemento **UserJourneys**.
5. Renomeie a ID do percurso do usuário. Por exemplo, `SignUpSignInADFS`.

### <a name="display-the-button"></a>Exibir o botão

O elemento **ClaimsProviderSelection** é análogo a um botão do provedor de identidade em uma tela de inscrição ou de entrada. Se você adicionar um elemento **ClaimsProviderSelection** para uma conta de AD FS, um novo botão será exibido quando um usuário chegar à página.

1. Encontre o elemento **OrchestrationStep** que inclui `Order="1"` na jornada do usuário que você criou.
2. Em **ClaimsProviderSelections**, adicione o elemento a seguir. Defina o valor de **TargetClaimsExchangeId** para um valor apropriado, por exemplo `ContosoExchange`:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Vincular o botão a uma ação

Agora que implementou um botão, você precisará vinculá-lo a uma ação. Nesse caso, a ação é para Azure AD B2C se comunicar com uma conta de AD FS para receber um token.

1. Localize o **OrchestrationStep** que inclui `Order="2"` no percurso do usuário.
2. Adicione o seguinte elemento **ClaimsExchange** certificando-se de que você use o mesmo valor para a ID que você usou para **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

    Atualize o valor de **TechnicalProfileReferenceId** para a ID do perfil técnico você já criou. Por exemplo, `Contoso-SAML2`.

3. Salve o arquivo *TrustFrameworkExtensions.xml* e carregue-o novamente para verificação.


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

### <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o arquivo de terceira parte confiável

Atualize o arquivo de RP (terceira parte confiável) que iniciará o percurso do usuário que você criou.

1. Faça uma cópia do *SignUpOrSignIn.xml* no diretório de trabalho e renomeie-a. Por exemplo, renomeie-a para *SignUpSignInADFS.xml*.
2. Abra o novo arquivo e atualize o valor do atributo **PolicyId** para **TrustFrameworkPolicy** com um valor exclusivo. Por exemplo, `SignUpSignInADFS`.
3. Atualize o valor de **PublicPolicyUri** com o URI da política. Por exemplo, `http://contoso.com/B2C_1A_signup_signin_adfs`
4. Atualize o valor do atributo **ReferenceId** em **DefaultUserJourney** para corresponder à ID do novo percurso do usuário que você criou (SignUpSignInADFS).
5. Salve suas alterações, carregue o arquivo e, em seguida, selecione a nova política na lista.
6. Verifique se o aplicativo Azure AD B2C que você criou está selecionado no campo **Selecionar aplicativo** e teste-o clicando em **Executar agora**.

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

Você pode configurar como assinar a solicitação SAML no Azure AD B2C. Os metadados [XmlSignatureAlgorithm](saml-identity-provider-technical-profile.md#metadata) controlam o valor do `SigAlg` parâmetro (cadeia de caracteres de consulta ou parâmetro post) na solicitação SAML. O exemplo a seguir configura Azure AD B2C para usar o `rsa-sha256` algoritmo de assinatura.

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