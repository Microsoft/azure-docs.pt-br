---
title: Adicionar ADFS como um provedor de identidade SAML usando políticas personalizadas no Azure Active Directory B2C | Microsoft Docs
description: Configurar o ADFS 2016 usando o protocolo SAML e as políticas personalizadas no Azure Active Directory B2C
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 2c469b333c6896d33b440bfadf0ebbdbeece71a3
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272141"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Adicionar ADFS como um provedor de identidade SAML usando políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como habilitar a entrada de uma conta de usuário do ADFS usando [políticas personalizadas](active-directory-b2c-overview-custom.md) no Azure AD (Azure Active Directory) B2C. Você ativa o login adicionando um [perfil técnico do SAML](saml-technical-profile.md) a uma política personalizada.

## <a name="prerequisites"></a>Pré-requisitos

- Conclua as etapas em [Introdução às políticas personalizadas no Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Certifique-se de que você tenha acesso a um arquivo. pfx de certificado com uma chave privada. Você pode gerar seu próprio certificado autoassinado e carregá-lo no Azure AD B2C. O Azure AD B2C usa esse certificado para assinar a solicitação SAML enviada ao seu provedor de identidade SAML.

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar o certificado em seu locatário do Azure AD B2C.

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Verifique se que você estiver usando o diretório que contém o seu locatário do Azure AD B2C. Selecione o **filtro de diretório e assinatura** no menu superior e escolha o diretório que contém o seu locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Na página de Visão Geral, selecione **Estrutura de Experiência de Identidade**.
5. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
6. Para **Opções**, escolha `Upload`.
7. Insira um **Nome** para a chave de política. Por exemplo: `SamlCert`. O prefixo `B2C_1A_` será adicionado automaticamente ao nome da chave.
8. Procure e selecione o arquivo .pfx do certificado com a chave privada.
9. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicionar um provedor de declarações

Se você quiser que os usuários entrem usando uma conta do ADFS, defina a conta como um provedor de declarações com o qual o Azure AD B2C pode se comunicar por meio de um ponto de extremidade. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado. 

Você pode definir uma conta do ADFS como um provedor de declarações adicionando-o ao elemento **ClaimsProviders** no arquivo de extensão da política.

1. Abra *TrustFrameworkExtensions.xml*.
2. Localize o elemento **ClaimsProviders**. Se ele não existir, adicione-o sob o elemento raiz.
3. Adicione um novo **ClaimsProvider** da seguinte maneira:

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso ADFS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso ADFS</DisplayName>
          <Description>Login with your ADFS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-ADFS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
            <Item Key="XmlSignatureAlgorithm">Sha256</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
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
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Substitua `your-ADFS-domain` pelo nome do domínio do ADFS e substitua o valor da declaração de saída **identityProvider** pelo DNS (valor arbitrário que indica o domínio).
5. Salve o arquivo.

### <a name="upload-the-extension-file-for-verification"></a>Carregar o arquivo de extensão para verificação

Agora, você já configurou a política, portanto, o Azure AD B2C sabe como se comunicar com a conta do ADFS. Tente carregar o arquivo de extensão da política apenas para confirmar se ele não apresenta problemas até o momento.

1. Na página **Políticas Personalizadas** em seu locatário do Azure AD B2C, selecione **Carregar Política**.
2. Habilite **Substitua a política se ela existir** e, em seguida, navegue até o arquivo *TrustFrameworkExtensions.xml* e selecione-o.
3. Clique em **Carregar**.

> [!NOTE]
> O código do Visual Studio B2C extensão usa "socialIdpUserId". Uma política de redes sociais também é necessária para o AD FS.
>

## <a name="register-the-claims-provider"></a>Registrar o provedor de declarações

Neste ponto, o provedor de identidade já foi definido, mas não está disponível em nenhuma das telas de inscrição ou entrada. Para disponibilizá-lo, você criará uma duplicata de um percurso do usuário de modelo existente e, depois, o modificará para que ele também tenha o provedor de identidade ADFS.

1. Abra o arquivo *TrustFrameworkBase.xml* do pacote de início.
2. Localize e copie todo o conteúdo do elemento **UserJourney** que inclui `Id="SignUpOrSignIn"`.
3. Abra o *TrustFrameworkExtensions.xml* e localize o elemento **UserJourneys**. Se o elemento não existir, adicione um.
4. Cole todo o conteúdo do elemento **UserJourney** que você copiou como filho do elemento **UserJourneys**.
5. Renomeie a ID do percurso do usuário. Por exemplo: `SignUpSignInADFS`.

### <a name="display-the-button"></a>Exibir o botão

O elemento **ClaimsProviderSelection** é análogo a um botão do provedor de identidade em uma tela de inscrição ou de entrada. Se você adicionar um elemento **ClaimsProviderSelection** à conta do ADFS, um novo botão será exibido quando o usuário chegar à página.

1. Localize o elemento **OrchestrationStep** que inclui `Order="1"` no percurso do usuário que você criou.
2. Em **ClaimsProviderSelections**, adicione o elemento a seguir. Defina o valor de **TargetClaimsExchangeId** para um valor apropriado, por exemplo `ContosoExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Vincular o botão a uma ação

Agora que implementou um botão, você precisará vinculá-lo a uma ação. A ação, nesse caso, é para o Azure AD B2C comunicar-se com uma conta do ADFS para receber um token.

1. Localize o **OrchestrationStep** que inclui `Order="2"` no percurso do usuário.
2. Adicione o seguinte elemento **ClaimsExchange** certificando-se de que você use o mesmo valor para a ID que você usou para **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```
    
    Atualize o valor de **TechnicalProfileReferenceId** para a ID do perfil técnico que você criou anteriormente. Por exemplo: `Contoso-SAML2`.

3. Salve o arquivo *TrustFrameworkExtensions.xml* e carregue-o novamente para verificação.


## <a name="configure-an-adfs-relying-party-trust"></a>Configurar um objeto de confiança de terceira parte confiável do ADFS

Para usar o ADFS como um provedor de identidade no Azure AD B2C, é necessário criar uma confiança de terceira parte confiável do ADFS com os metadados SAML do Azure AD B2C. O exemplo a seguir mostra um endereço de URL para os metadados do SAML de um perfil técnico do Azure AD B2C:

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

Substitua os valores a seguir:

- **your-tenant** pelo nome do locatário, como your-tenant.onmicrosoft.com.
- **your-policy** pelo nome da política. Por exemplo, B2C_1A_signup_signin_adfs.
- **seu perfil técnico** com o nome do seu perfil de técnico do provedor de identidade SAML. Por exemplo, Contoso-SAML2.
 
Abra um navegador e navegue até a URL. Certifique-se de digitar a URL correta e ter acesso ao arquivo de metadados XML. Para adicionar uma nova confiança de terceira parte confiável usando o snap-in Gerenciamento do ADFS e definir manualmente as configurações, execute o procedimento a seguir em um servidor de federação. Associação em **Administradores** ou equivalente no computador local é o mínimo necessário para concluir esse procedimento.

1. No Gerenciador do Servidor, selecione **Ferramentas** e, em seguida, selecione **Gerenciamento do ADFS**.
2. Selecione **Adicionar Relação de Confiança de Terceira Parte Confiável**.
3. Na **Página inicial**, escolha **Com reconhecimento de declaração** e, em seguida, clique em **Iniciar**.
4. Na página **Selecionar Fonte de Dados**, selecione **Importar dados sobre a terceira parte confiável publicados online ou em uma rede local**, forneça a URL de metadados do Azure AD B2C e clique em **Avançar**.
5. Na página **Especificar Nome para Exibição**, insira um **Nome de exibição**, em **Notas**, insira uma descrição para essa confiança de terceira parte confiável e clique em **Avançar**.
6. Na página **Escolher Política de Controle de Acesso**, selecione uma política e, em seguida, clique em **Avançar**.
7. Na página **Pronto para Adicionar Objeto de Confiança**, examine as configurações e, em seguida, clique em **Avançar** para salvar as informações de seu objeto de confiança de terceira parte confiável.
8. Na página **Concluir**, clique em **Fechar**, essa ação exibe automaticamente a caixa de diálogo **Editar Regras de Declaração**.
9. Selecione **Adicionar Regra**.  
10. Em **Modelo de regra de declaração**, selecione **Enviar atributos do LDAP como declarações**.
11. Forneça um **nome de Regra de declaração**. Para o **Repositório de atributos**, selecione **Selecionar Active Directory**, adicione as seguintes declarações e clique em **Concluir** e **OK**.

    | Atributo LDAP | Tipo de declaração de saída |
    | -------------- | ------------------- |
    | User-Principal-Name | userPrincipalName |
    | Sobrenome | family_name |
    | Given-Name | given_name |
    | E-Mail-Address | email |
    | Display-Name | name |
    
12.  Com base no tipo de certificado, talvez seja necessário definir o algoritmo de HASH. Na janela de propriedades de confiança de terceira parte confiável (Demonstração B2C), selecione a guia **Avançado** e altere o **algoritmo de Secure hash** para `SHA-256`, e clique em **OK**.  
13. No Gerenciador do Servidor, selecione **Ferramentas** e, em seguida, selecione **Gerenciamento do ADFS**.
14. Selecione a terceira parte confiável que você criou, selecione **Atualização dos metadados da federação** e, em seguida, clique em **Atualizar**. 

## <a name="create-an-azure-ad-b2c-application"></a>Criar um aplicativo Azure AD B2C

A comunicação com o Azure AD B2C ocorre por meio de um aplicativo que você cria no seu locatário. Esta seção lista etapas opcionais que você pode concluir para criar um aplicativo de teste, caso ainda não tenha feito isso.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que contém seu locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Selecione **Aplicativos** e, em seguida, selecione **Adicionar**.
5. Insira um nome para o aplicativo, por exemplo *testapp1*.
6. Para **Aplicativo Web/API Web**, selecione `Yes` e, em seguida, insira `https://jwt.ms` para a **URL de resposta**.
7. Clique em **Criar**.

### <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o arquivo de terceira parte confiável

Atualize o arquivo de RP (terceira parte confiável) que iniciará o percurso do usuário que você criou.

1. Faça uma cópia do *SignUpOrSignIn.xml* no diretório de trabalho e renomeie-a. Por exemplo, renomeie-a para *SignUpSignInADFS.xml*.
2. Abra o novo arquivo e atualize o valor do atributo **PolicyId** para **TrustFrameworkPolicy** com um valor exclusivo. Por exemplo: `SignUpSignInADFS`.
3. Atualize o valor de **PublicPolicyUri** com o URI da política. Por exemplo, `http://contoso.com/B2C_1A_signup_signin_adfs`
4. Atualize o valor do atributo **ReferenceId** em **DefaultUserJourney** para corresponder à ID do novo percurso do usuário que você criou (SignUpSignInADFS).
5. Salve suas alterações, faça upload do arquivo e, em seguida, selecione a nova política na lista.
6. Verifique se o aplicativo Azure AD B2C que você criou está selecionado no campo **Selecionar aplicativo** e teste-o clicando em **Executar agora**.

