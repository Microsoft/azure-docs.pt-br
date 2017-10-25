---
title: "Azure Active Directory B2C: adicionando um provedor SAML do Salesforce usando políticas personalizadas | Microsoft Docs"
description: "Saiba mais sobre como criar e gerenciar políticas personalizadas do Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: d7f4143f-cd7c-4939-91a8-231a4104dc2c
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 06/11/2017
ms.author: parakhj
ms.openlocfilehash: 269cbd80fb6e861fa8588025eec70b6c6e2890d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-sign-in-by-using-salesforce-accounts-via-saml"></a>Azure Active Directory B2C: entrar usando contas do Salesforce via SAML

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como usar [políticas personalizadas](active-directory-b2c-overview-custom.md) para configurar a entrada de usuários de uma organização específica do Salesforce.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-ad-b2c-setup"></a>Configuração do Azure AD B2C

Não deixe de concluir todas as etapas que mostram como [começar a usar as políticas personalizadas](active-directory-b2c-get-started-custom.md) no Azure Active Directory B2C (Azure AD B2C).

Estão incluídos:

* Criar um locatário do Azure AD B2C.
* Criar um aplicativo Azure AD B2C.
* Registrar dois aplicativos de mecanismo de políticas.
* Configurar chaves.
* Configurar o pacote inicial.

### <a name="salesforce-setup"></a>Configuração do Salesforce

Neste artigo, presumimos que você já tenha feito o seguinte:

* Inscreveu-se em uma conta do Salesforce. Você pode se inscrever em uma [conta gratuita do Developer Edition](https://developer.salesforce.com/signup).
* [Configurou Meu Domínio](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) para sua organização do Salesforce.

## <a name="set-up-salesforce-so-users-can-federate"></a>Configurar o Salesforce para que os usuários possam estabelecer a federação

Para ajudar o Azure AD B2C a se comunicar com o Salesforce, você precisa obter a URL de metadados do Salesforce.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Configurar o Salesforce como um provedor de identidade

> [!NOTE]
> Neste artigo, presumimos que você esteja usando o [Salesforce Lightning Experience](https://developer.salesforce.com/page/Lightning_Experience_FAQ).

1. [Entre no Salesforce](https://login.salesforce.com/). 
2. No menu à esquerda, em **Configurações**, expanda **Identidade** e clique em **Provedor de Identidade**.
3. Clique em **Habilitar Provedor de Identidade**.
4. Em **Selecione um certificado**, selecione o certificado que você quer que o Salesforce use para se comunicar com o Azure AD B2C. (Você pode usar o certificado padrão.) Clique em **Salvar**. 

### <a name="create-a-connected-app-in-salesforce"></a>Criar um aplicativo conectado no Salesforce

1. Na página **Provedor de Identidade**, vá até **Provedores de Serviço**.
2. Clique em **Os Provedores de Serviço agora são criados por meio de Aplicativos Conectados. Clique aqui.**
3. Em **Informações Básicas**, forneça os valores necessários para seu aplicativo conectado.
4. Em **Configurações do Aplicativo Web**, marque a caixa de seleção **Habilitar SAML**.
5. No campo **ID da Entidade**, digite a URL a seguir. Certifique-se substituir o valor de `tenantName`.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```
6. No campo **URL ACS**, digite a URL a seguir. Certifique-se substituir o valor de `tenantName`.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Deixe os valores padrão para todas as outras configurações.
8. Role até a parte inferior da lista e, em seguida, clique em **Salvar**.

### <a name="get-the-metadata-url"></a>Obter a URL de metadados

1. Na página de visão geral de seu aplicativo conectado, clique em **Gerenciar**.
2. Copie o valor de **Ponto de Extremidade de Descoberta de Metadados** e salve-o. Você o usará posteriormente neste artigo.

### <a name="set-up-salesforce-users-to-federate"></a>Configurar os usuários do Salesforce para federação

1. Na página **Gerenciar** de seu aplicativo conectado, vá até **Perfis**.
2. Clique em **Gerenciar Perfis**.
3. Selecione os perfis (ou grupos de usuários) que você deseja federar com o Azure AD B2C. Como administrador do sistema, marque a caixa de seleção **Administrador do Sistema** para que você possa estabelecer a federação usando sua conta do Salesforce.

## <a name="generate-a-signing-certificate-for-azure-ad-b2c"></a>Gerar um certificado de autenticação para o Azure AD B2C

Solicitações enviadas para o Salesforce precisam ser assinadas pelo Azure AD B2C. Para gerar um certificado de autenticação, abra o Azure PowerShell e, em seguida, execute os seguintes comandos.

> [!NOTE]
> Não deixe de atualizar o nome do locatário e a senha nas primeiras duas linhas.

```PowerShell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="add-the-saml-signing-certificate-to-azure-ad-b2c"></a>Adicionar o certificado de autenticação SAML ao Azure AD B2C

Carregue o certificado de autenticação para seu locatário do Azure AD B2C: 

1. Vá até seu locatário do Azure AD B2C. Clique em **Configurações** > **Estrutura de Experiência de Identidade** > **Chaves da Política**.
2. Clique em **+Adicionar** e, em seguida:
    1. Clique em **Opções** > **Carregar**.
    2. Insira um **Nome** (por exemplo, SAMLSigningCert). O prefixo *B2C_1A_* será adicionado automaticamente ao nome da chave.
    3. Para selecionar seu certificado, selecione **carregar o controle de arquivo**. 
    4. Insira a senha do certificado que você definiu no script do PowerShell.
3. Clique em **Criar**.
4. Verifique se você criou uma chave (por exemplo, B2C_1A_SAMLSigningCert). Anote o nome completo (incluindo *B2C_1A_*). Você fará referência a essa chave posteriormente na política.

## <a name="create-the-salesforce-saml-claims-provider-in-your-base-policy"></a>Criar o provedor de declarações SAML do Salesforce em sua política base

Você precisa definir o Salesforce como um provedor de declarações para que os usuários possam entrar usando o Salesforce. Em outras palavras, você precisa especificar o ponto de extremidade com o qual o Azure AD B2C se comunicará. O ponto de extremidade *fornecerá* um conjunto de *declarações* usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado. Faça isso adicionando um `<ClaimsProvider>` ao Salesforce no arquivo de extensão da política:

1. No diretório de trabalho, abra o arquivo de extensão (TrustFrameworkExtensions.xml).
2. Localize a seção `<ClaimsProviders>`. Caso ela não exista, crie-a sob o nó raiz.
3. Adicione um novo `<ClaimsProvider>`:

    ```XML
    <ClaimsProvider>
      <Domain>salesforce</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="salesforce">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="RequestsSigned">false</Item>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="externalIdp"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="SAMLIdp" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

Sob o nó `<ClaimsProvider>`:

1. Altere o valor de `<Domain>` para um valor exclusivo para distinguir `<ClaimsProvider>` de outros provedores de identidade.
2. Atualize o valor de `<DisplayName>` para um nome de exibição do provedor de declarações. Atualmente, esse valor não é usado.

### <a name="update-the-technical-profile"></a>Atualizar o perfil técnico

Para obter um token SAML do Salesforce, defina os protocolos que o Azure AD B2C usará para se comunicar com o Azure AD (Azure Active Directory). Faça isso no elemento `<TechnicalProfile>` de `<ClaimsProvider>`:

1. Atualize a ID do nó `<TechnicalProfile>`. Essa ID é usada para se referir a esse perfil técnico em outras partes da política.
2. Atualize o valor de `<DisplayName>`. Esse valor é exibido no botão de entrada em sua página de entrada.
3. Atualize o valor de `<Description>`.
4. O Salesforce usa o protocolo SAML 2.0. Certifique-se de que o valor de `<Protocol>` seja **SAML2**.

Atualize a seção `<Metadata>` no XML anterior para refletir as configurações de sua conta específica do Salesforce. No XML, atualize os valores dos metadados:

1. Atualize o valor de `<Item Key="PartnerEntity">` com a URL de metadados do Salesforce que você copiou anteriormente. Ela tem o seguinte formato: 

    `https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp/connectedapp.xml`

2. Na seção `<CryptographicKeys>`, atualize o valor de ambas as instâncias de `StorageReferenceId` para o nome da chave de seu certificado de autenticação (por exemplo, B2C_1A_SAMLSigningCert).

### <a name="upload-the-extension-file-for-verification"></a>Carregar o arquivo de extensão para verificação

Agora, a política agora está configurada para que o Azure AD B2C saiba como se comunicar com o Salesforce. Tente carregar o arquivo de extensão da política para confirmar se ele não apresenta problemas até o momento. Para carregar o arquivo de extensão da política:

1. Acesse a folha **Todas as Políticas** no locatário do Azure AD B2C.
2. Marque a caixa de seleção **Substituir a política caso ela exista**.
3. Carregue o arquivo de extensões (TrustFrameworkExtensions.xml). Certifique-se de que ele não falhe na validação.

## <a name="register-the-salesforce-saml-claims-provider-to-a-user-journey"></a>Registrar o provedor de declarações SAML do Salesforce em um percurso do usuário

Em seguida, adicione o provedor de identidade SAML do Salesforce a um dos percursos do usuário. Neste ponto, o provedor de identidade foi definido, mas não está disponível em nenhuma das páginas de inscrição/entrada do usuário. Para adicionar o provedor de identidade a uma página de entrada, crie primeiro uma duplicata de um Percurso do Usuário do modelo existente. Em seguida, modifique o modelo para que ele também tenha o provedor de identidade do Azure AD.

1. Abra o arquivo base da política (por exemplo, TrustFrameworkBase.xml).
2. Localize o elemento `<UserJourneys>` e copie todo o valor de `<UserJourney>`, incluindo Id=“SignUpOrSignIn”.
3. Abra o arquivo de extensão (por exemplo, TrustFrameworkExtensions.xml). Localize o elemento `<UserJourneys>`. Se o elemento não existir, crie um.
4. Cole todo o `<UserJourney>` copiado como um filho do elemento `<UserJourneys>`.
5. Renomeie a ID do novo `<UserJourney>` (por exemplo, SignUpOrSignUsingContoso).

### <a name="display-the-identity-provider-button"></a>Exibir o botão do provedor de identidade

O elemento `<ClaimsProviderSelection>` é análogo a um botão de provedor de identidade em uma página de inscrição/entrada. Ao adicionar um elemento `<ClaimsProviderSelection>` ao Salesforce, um novo botão aparecerá quando um usuário chegar na página. Para exibir o botão do provedor de identidade:

1. No `<UserJourney>` criado, localize o `<OrchestrationStep>` com `Order="1"`.
2. Adicione o XML a seguir:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

3. Defina `TargetClaimsExchangeId` como um valor lógico. Recomendamos seguir a mesma convenção que os outros (por exemplo, *\[ClaimProviderName\]Exchange*).

### <a name="link-the-identity-provider-button-to-an-action"></a>Vincular o botão do provedor de identidade a uma ação

Agora que você implementou um botão do provedor de identidade, vincule-o a uma ação. Nesse caso, a ação destina-se a que o Azure AD B2C se comunique com o Salesforce para receber um token SAML. Faça isso vinculando o perfil técnico ao provedor de declarações SAML do Salesforce:

1. No nó `<UserJourney>`, localize o `<OrchestrationStep>` com `Order="2"`.
2. Adicione o XML a seguir:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

3. Atualize `Id` com o mesmo valor que você usou anteriormente para `TargetClaimsExchangeId`.
4. Atualize `TechnicalProfileReferenceId` para o `Id` do perfil técnico você criou anteriormente (por exemplo, ContosoProfile).

### <a name="upload-the-updated-extension-file"></a>Carregar o arquivo de extensão atualizado

Agora você concluiu a modificação do arquivo de extensão. Salve e carregue esse arquivo. Certifique-se de que todas as validações tenham êxito.

### <a name="update-the-relying-party-file"></a>Atualizar o arquivo de terceira parte confiável

Em seguida, atualize o arquivo de RP (terceira parte confiável) que iniciará o percurso do usuário que você criou:

1. Faça uma cópia de SignUpOrSignIn.xml em seu diretório de trabalho. Em seguida, renomeie-a (por exemplo, SignUpOrSignInWithAAD.xml).
2. Abra o novo arquivo e atualize o atributo `PolicyId` de `<TrustFrameworkPolicy>` com um valor exclusivo. Esse é o nome da política (por exemplo, SignUpOrSignInWithAAD).
3. Modifique o atributo `ReferenceId` em `<DefaultUserJourney>` para que ele corresponda a `Id` do novo percurso do usuário criado (por exemplo, SignUpOrSignUsingContoso).
4. Salve as alterações e, em seguida, carregue o arquivo.

## <a name="test-and-troubleshoot"></a>Testar e solucionar problemas

Para testar a política personalizada que você acabou de carregar, no portal do Azure, vá até a folha de política e, em seguida, clique em **Executar agora**. Se ela falhar, consulte [Solucionar problemas com políticas personalizadas](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Próximas etapas

Envie seus comentários para [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com).
