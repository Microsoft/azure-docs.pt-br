---
title: "Azure Active Directory B2C: Adicionar ADFS como um provedor de identidade SAML usando políticas personalizadas"
description: "Um artigo de instruções sobre como configurar o ADFS 2016 usando o protocolo SAML e políticas personalizadas"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: yoelh
ms.openlocfilehash: 8713fc7dd27023e1244ccb00673dd1652689baf5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-add-adfs-as-a-saml-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: Adicionar ADFS como um provedor de identidade SAML usando políticas personalizadas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como habilitar a entrada para usuários da conta ADFS por meio de [políticas personalizadas](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas no artigo [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md).

As etapas incluem:

1.  Criar um objeto de confiança de terceira parte confiável do ADFS.
2.  Adicionar o certificado do objeto de confiança de terceira parte confiável do ADFS ao Azure AD B2C.
3.  Adicionar provedor de declarações a uma política.
4.  Registrar o provedor de declarações da conta do ADFS a um percurso do usuário.
5.  Carregar a política para um locatário do Azure AD B2C e testá-la.

## <a name="to-create-a-claims-aware-relying-party-trust"></a>Para criar um objeto de confiança de terceira parte confiável com reconhecimento de declarações

Para usar o ADFS como um provedor de identidade no Azure AD (Azure Active Directory) B2C, é necessário criar um objeto de confiança de terceira parte confiável do ADFS e fornecê-lo com os parâmetros corretos.

Para adicionar um novo objeto de confiança de terceira parte confiável usando o snap-in de Gerenciamento do AD FS e definir manualmente as configurações, execute o seguinte procedimento em um servidor de Federação.

A associação em **Administradores**, ou equivalente, no computador local é o mínimo necessário para concluir este procedimento. Examine os detalhes sobre como usar as contas apropriadas e associações de grupos em [Grupos Padrão Locais e de Domínio](http://go.microsoft.com/fwlink/?LinkId=83477)

1.  Em Gerenciador do Servidor, clique em **Ferramentas** e depois selecione **Gerenciamento do ADFS**.

2.  Clique em **Adicionar Objeto de Confiança de Terceira Parte Confiável**.
    ![Adicionar Objeto de Confiança de Terceira Parte Confiável](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-1.png)

3.  Na página **Boas-vindas**, escolha **Reconhecimento de declaração** e clique em **Iniciar**.
    ![Na página Boas-vindas, escolha Reconhecimento de declaração](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-2.png)
4.  Na página **Selecionar Fonte de Dados**, clique em **Inserir dados sobre a terceira parte confiável manualmente** e clique em **Avançar**.
    ![Inserir dados sobre a terceira parte confiável](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-3.png)

5.  Na página **Especificar Nome de Exibição**, digite um nome em **Nome de exibição**, em **Notas** digite uma descrição para essa terceira parte confiável e, em seguida, clique em **Avançar** .
    ![Especifique o Nome de Exibição e notas](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-4.png)
6.  Opcional. Se você tiver um certificado de criptografia de token opcional, na página **Configurar Certificado**, clique em **Procurar** para localizar o arquivo de certificado e, em seguida, clique em **Avançar**.
    ![Configurar Certificado](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-5.png)
7.  Na página **Configurar URL**, marque a caixa de seleção **Habilitar o suporte para o protocolo WebSSO de SAML 2.0**. Em **URL do serviço de SSO do SAML 2.0 da terceira parte confiável**, digite a URL do ponto de extremidade do serviço SAML (Security Assertion Markup Language) para essa terceira parte confiável e, em seguida, clique em **Avançar**.  Para a **URL do serviço de SSO do SAML 2.0 da terceira parte confiável**, cole a `https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/{policy}`. Substitua {tenant} pelo nome do locatário (por exemplo, contosob2c.onmicrosoft.com) e substitua {policy} pelo nome da política de extensões (por exemplo, B2C_1A_TrustFrameworkExtensions).
    > [!IMPORTANT]
    >O nome da política é aquele da qual a política de signup_or_signin herda, nesse caso é: `B2C_1A_TrustFrameworkExtensions`.
    >Por exemplo, a URL pode ser: https://login.microsoftonline.com/te/**contosob2c**.onmicrosoft.com/**B2C_1A_TrustFrameworkBase**.

    ![URL do serviço de SSO do SAML 2.0 da terceira parte confiável](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-6.png)
8. Na página **Configurar Identificadores**, especifique a mesma URL da etapa anterior, clique em **Adicionar** para adicioná-la à lista e, em seguida, clique em **Avançar**.
    ![Identificadores de objeto de confiança de terceira parte confiável](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-7.png)
9.  Em **Escolher Política de Controle de Acesso**, escolha uma política e clique em **Avançar**.
    ![Escolher a Política de Controle de Acesso](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-8.png)
10.  Na página **Pronto para Adicionar Objeto de Confiança**, examine as configurações e, em seguida, clique em **Avançar** para salvar as informações de seu objeto de confiança de terceira parte confiável.
    ![Salvar as informações de seu objeto de confiança de terceira parte confiável](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-9.png)
11.  Na página **Concluir**, clique em **Fechar**, essa ação exibe automaticamente a caixa de diálogo **Editar Regras de Declaração**.
    ![Editar Regras de Declaração](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-10.png)
12. Clique em **Adicionar Regra**.  
      ![Adicionar nova regra](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-1.png)
13.  Em **Modelo de regra de declaração**, selecione **Enviar atributos do LDAP como declarações**.
    ![Selecione Enviar atributos do LDAP como regra de modelo de declarações](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-2.png)
14.  Forneça o **Nome da regra de declaração**. Para o **Repositório de atributos**, selecione **Selecionar Active Directory** Adicione as seguintes declarações e clique em **Concluir** e em **OK**.
    ![Definir propriedades de regra](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-3.png)
15.  No Gerenciador do Servidor, selecione **Objetos de Confiança de Terceira Parte Confiável**, depois, selecione o objeto de confiança de terceira parte confiável que você criou e clique em **Propriedades**.
    ![Editar propriedades da terceira parte confiável](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-1.png)
16.  na janela de propriedades do objeto de confiança de terceira parte (Demonstração de B2C), clique na guia **Assinatura** e clique em **Adicionar**.  
    ![Definir assinatura](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-2.png)
17.  Adicione o certificado de assinatura (arquivo .cert, sem a chave privada).  
    ![Adicionar seu certificado de assinatura](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-3.png)
18.  Na janela de propriedades do objeto de confiança da terceira parte confiável (Demonstração de B2C), clique na guia **Avançado** e altere o **Algoritmo de hash seguro** para **SHA-1**, clique em **OK**.  
    ![Definir o algoritmo de hash seguro como SHA-1](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-4.png)

## <a name="add-the-adfs-account-application-key-to-azure-ad-b2c"></a>Adicionar a chave de aplicativo da conta do ADFS ao Azure AD B2C
A federação com contas do ADFS exige um segredo do cliente para a conta do ADFS para confiar no Azure AD B2C em nome do aplicativo. Você precisa armazenar o certificado ADFS em seu locatário do Azure AD B2C. 

1.  Vá até seu locatário do Azure AD B2C e selecione **Configurações de B2C** > **Identity Experience Framework**
2.  Selecione **Chaves de Política** para exibir as chaves disponíveis no seu locatário.
3.  Clique em **+Adicionar**.
4.  Para as **Opções**, use **Upload**.
5.  Para o **Nome**, use `ADFSSamlCert`.  
    O prefixo `B2C_1A_` pode ser adicionado automaticamente.
6.  No Carregamento do arquivo, ** selecione seu arquivo de certificado .pfx com chave privada. Observação: esse certificado (com a chave privada) deve ser o mesmo que foi emitido e usado para a terceira parte confiável do ADFS.
![Carregar chave de política](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-cert.png)
7.  Clique em **Criar**
8.  Confirme que você criou a chave `B2C_1A_ADFSSamlCert`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Adicionar um provedor de declarações à política de extensão
Se quiser que os usuários entrem usando a conta do ADFS, você precisará definir a conta do ADFS como um provedor de declarações. Em outras palavras, você precisa especificar um ponto de extremidade com o qual o Azure AD B2C se comunica. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

Defina o ADFS como um provedor de declarações adicionando o nó `<ClaimsProvider>` em seu arquivo de política da extensão:

1. Abra o arquivo de política de extensão (TrustFrameworkExtensions.xml) de seu diretório de trabalho. Se você precisar de um editor de XML, [experimente o Visual Studio Code](https://code.visualstudio.com/download), um editor de plataforma cruzada leve.
2. Localize a seção `<ClaimsProviders>`
3. Adicione o seguinte trecho XML sob o elemento `ClaimsProviders` e substitua `identityProvider` pelo DNS (valor arbitrário que indica o seu domínio) e salve o arquivo. 

```xml
<ClaimsProvider>
    <Domain>contoso.com</Domain>
    <DisplayName>Contoso ADFS</DisplayName>
    <TechnicalProfiles>
    <TechnicalProfile Id="Contoso-SAML2">
        <DisplayName>Contoso ADFS</DisplayName>
        <Description>Login with your Contoso account</Description>
        <Protocol Name="SAML2"/>
        <Metadata>
        <Item Key="RequestsSigned">false</Item>
        <Item Key="WantsEncryptedAssertions">false</Item>
        <Item Key="PartnerEntity">https://{your_ADFS_domain}/federationmetadata/2007-06/federationmetadata.xml</Item>
        </Metadata>
        <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
        </CryptographicKeys>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userPrincipalName" />
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

## <a name="register-the-adfs-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Registrar o provedor de declarações da conta do ADFS a um percurso do usuário de Inscrição ou Entrada
Neste ponto, o provedor de identidade foi configurado.  No entanto, ele não está disponível em qualquer uma das telas de inscrição/entrada. Agora você precisa adicionar o provedor de identidade da conta do ADFS ao percurso de usuário `SignUpOrSignIn` do usuário. Para disponibilizá-lo, criamos uma duplicata de um percurso de usuário do modelo existente.  Em seguida, nós o modificamos para inclua o provedor de identidade do ADFS:

>[!NOTE]
>Se você tiver copiado anteriormente o elemento `<UserJourneys>` do arquivo de base de sua política para o arquivo de extensão (TrustFrameworkExtensions.xml), poderá ignorar esta seção.

1.  Abra o arquivo base da política (por exemplo, TrustFrameworkBase.xml).
2.  Localize o elemento `<UserJourneys>` e copie todo o conteúdo do nó `<UserJourneys>`.
3.  Abra o arquivo de extensão (por exemplo, TrustFrameworkExtensions.xml) e localize o elemento `<UserJourneys>`. Se o elemento não existir, adicione um.
4.  Cole todo o conteúdo do nó `<UserJournesy>` copiado como um filho do elemento `<UserJourneys>`.

### <a name="display-the-button"></a>Exibir o botão
O elemento `<ClaimsProviderSelections>` define a lista de opções de seleção de provedor de declarações e sua ordem.  O elemento `<ClaimsProviderSelection>` é análogo a um botão de provedor de identidade em uma página de inscrição/entrada. Se você adicionar um elemento `<ClaimsProviderSelection>` para a conta do ADFS, um novo botão será exibido quando um usuário chegar à página. Para adicionar este elemento:

1.  Localize o nó `<UserJourney>` que inclui `Id="SignUpOrSignIn"` no percurso do usuário que você acabou de copiar.
2.  Localize o nó `<OrchestrationStep>` que inclui `Order="1"`
3.  Adicione o seguinte trecho XML ao nó `<ClaimsProviderSelections>`:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```
### <a name="link-the-button-to-an-action"></a>Vincular o botão a uma ação

Agora que implementou um botão, você precisará vinculá-lo a uma ação. Nesse caso, a ação destina-se a que o Azure AD B2C se comunique com a conta do ADFS para receber um token. Vincule o botão a uma ação vinculando o perfil técnico ao provedor de declarações da conta do ADFS:

1.  Localize o `<OrchestrationStep>` que inclui `Order="2"` no nó `<UserJourney>`.
2.  Adicione o seguinte trecho XML ao nó `<ClaimsExchanges>`:

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

> [!NOTE]
> * Certifique-se de que o `Id` tenha o mesmo valor de `TargetClaimsExchangeId` na seção anterior.
> * Certifique-se de que `TechnicalProfileReferenceId` esteja definido como o perfil técnico criado anteriormente (Contoso-SAML2).

## <a name="upload-the-policy-to-your-tenant"></a>Carregar a política ao seu locatário
1.  No [Portal do Azure](https://portal.azure.com), alterne para o [contexto do seu locatário do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) e abra a folha do **Azure AD B2C**.
2.  Selecione **Estrutura de Experiência de Identidade**.
3.  Abra a folha **Todas as Políticas**.
4.  Selecione **Carregar Política**.
5.  Marque a caixa **Substituir a política caso ela exista**.
6.  **Carregue** TrustFrameworkExtensions.xml e verifique se ele não falhou na validação

## <a name="test-the-custom-policy-by-using-run-now"></a>Testar a política personalizada usando a opção Executar Agora
1.  Abra as **Configurações do Azure AD B2C** e acesse a **Estrutura de Experiência de Identidade**.
2.  Abra a **B2C_1A_signup_signin**, a política personalizada da RP (terceira parte confiável) que você carregou. Selecione **Executar Agora**.
3.  Você deve conseguir entrar usando a conta do ADFS.

## <a name="optional-register-the-adfs-account-claims-provider-to-profile-edit-user-journey"></a>[Opcional] Registrar o provedor de declarações da conta do ADFS ao percurso de usuário de Edição de Perfil
Convém adicionar também o provedor de identidade da conta do ADFS ao percurso de usuário `ProfileEdit` do usuário. Para disponibilizá-lo, repetimos as duas últimas etapas:

### <a name="display-the-button"></a>Exibir o botão
1.  Abra o arquivo de extensão de sua política (por exemplo, TrustFrameworkExtensions.xml).
2.  Localize o nó `<UserJourney>` que inclui `Id="ProfileEdit"` no percurso do usuário que você acabou de copiar.
3.  Localize o nó `<OrchestrationStep>` que inclui `Order="1"`
4.  Adicione o seguinte trecho XML ao nó `<ClaimsProviderSelections>`:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```

### <a name="link-the-button-to-an-action"></a>Vincular o botão a uma ação
1.  Localize o `<OrchestrationStep>` que inclui `Order="2"` no nó `<UserJourney>`.
2.  Adicione o seguinte trecho XML ao nó `<ClaimsExchanges>`:

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Testar a política personalizada de Edição de Perfil usando Executar Agora
1.  Abra as **Configurações do Azure AD B2C** e acesse a **Estrutura de Experiência de Identidade**.
2.  Abra **B2C_1A_signup_signin**, a política personalizada da RP (terceira parte confiável) que você carregou. Selecione **Executar Agora**.
3.  Você deve conseguir entrar usando a conta do ADFS.

## <a name="download-the-complete-policy-files"></a>Baixar os arquivos da política completa
Opcional: recomendamos a criação de seu cenário usando seus próprios arquivos de política personalizada após a conclusão do passo a passo Introdução às políticas personalizadas. [Arquivos de exemplo de política apenas para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-adfs2016-app)
