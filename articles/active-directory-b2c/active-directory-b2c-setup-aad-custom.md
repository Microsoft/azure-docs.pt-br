---
title: "Azure Active Directory B2C: adicionar um provedor do Azure AD usando políticas personalizadas | Microsoft Docs"
description: "Saiba mais sobre as políticas personalizadas do Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 31f0dfe5-1ad0-4a25-a53b-8acc71bcea72
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: parakhj
ms.openlocfilehash: 6c073d70debfdc3560405955d65fa9ccaa7d8b1f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-sign-in-by-using-azure-ad-accounts"></a>Azure Active Directory B2C: entrar usando contas do Azure AD

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como habilitar a entrada para usuários de uma organização específica do Azure AD (Azure Active Directory) por meio do uso de [políticas personalizadas](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas no artigo [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md).

As etapas incluem:

1. Criar um locatário do Azure Active Directory B2C (Azure AD B2C).
2. Criar um aplicativo Azure AD B2C.
3. Registrar dois aplicativos do mecanismo de políticas.
4. Configurar chaves.
5. Configurar o pacote de início.

## <a name="create-an-azure-ad-app"></a>Criar um aplicativo Azure AD

Para habilitar a entrada para usuários de uma organização específica do Azure AD, você precisa registrar um aplicativo no locatário organizacional do Azure AD.

>[!NOTE]
> Usamos "contoso.com" como o locatário do Azure AD da organização e "fabrikamb2c.onmicrosoft.com" como o locatário do Azure AD B2C nas instruções a seguir.

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Na barra superior, selecione sua conta. Na lista **Diretório**, escolha o locatário do Azure AD organizacional em que deseja registrar seu aplicativo (contoso.com).
1. Selecione **Mais serviços** no painel esquerdo e pesquise por “Registros do aplicativo”.
1. Selecione **Novo registro de aplicativo**.
1. Insira um nome para seu aplicativo (por exemplo, `Azure AD B2C App`).
1. Selecione **Aplicativo Web/API** como o tipo de aplicativo.
1. Para a **URL de Logon**, insira a URL a seguir, em que `yourtenant` é substituído pelo nome do seu locatário do Azure AD B2C (`fabrikamb2c.onmicrosoft.com`):

    ```
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. Salve a ID do aplicativo.
1. Selecione o aplicativo recém-criado.
1. Na folha **Configurações**, selecione **Chaves**.
1. Crie uma nova chave e salve-a. Você a usará nas etapas da próxima seção.

## <a name="add-the-azure-ad-key-to-azure-ad-b2c"></a>Adicionar a chave do Azure AD ao Azure AD B2C

Você precisa armazenar a chave do aplicativo de contoso.com em seu locatário do Azure AD B2C. Para fazer isso:
1. Vá até seu locatário do Azure AD B2C e abra **Configurações B2C** > **Identity Experience Framework** > **Chaves de Política**.
1. Selecione **+Adicionar**.
1. Selecione ou insira estas opções:
   * Selecione **Manual**.
   * Para o **Nome**, escolha um nome que corresponda ao nome do locatário do Azure AD (por exemplo, `ContosoAppSecret`).  O prefixo `B2C_1A_` será adicionado automaticamente ao nome da chave.
   * Cole a chave do aplicativo na caixa de texto **Segredo**.
   * Selecione **Assinatura**.
1. Selecione **Criar**.
1. Confirme que você criou a chave `B2C_1A_ContosoAppSecret`.


## <a name="add-a-claims-provider-in-your-base-policy"></a>Adicionar um provedor de declarações à política base

Se quiser que os usuários entrem usando o Azure AD, você precisará definir o Azure AD como um provedor de declarações. Em outras palavras, você precisa especificar um ponto de extremidade com o qual o Azure AD B2C se comunicará. O ponto de extremidade fornecerá um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado. 

Você pode definir o Azure AD como um provedor de declarações adicionando o Azure AD ao nó `<ClaimsProvider>` no arquivo de extensão de sua política:

1. Abra o arquivo de extensão (TrustFrameworkExtensions.xml) no diretório de trabalho.
1. Localize a seção `<ClaimsProviders>`. Caso ela não exista, adicione-a sob o nó raiz.
1. Adicione um novo nó `<ClaimsProvider>` da seguinte maneira:

    ```XML
    <ClaimsProvider>
        <Domain>Contoso</Domain>
        <DisplayName>Login using Contoso</DisplayName>
        <TechnicalProfiles>
            <TechnicalProfile Id="ContosoProfile">
                <DisplayName>Contoso Employee</DisplayName>
                <Description>Login with your Contoso account</Description>
                <Protocol Name="OpenIdConnect"/>
                <OutputTokenFormat>JWT</OutputTokenFormat>
                <Metadata>
                    <Item Key="METADATA">https://login.windows.net/contoso.com/.well-known/openid-configuration</Item>
                    <Item Key="ProviderName">https://sts.windows.net/00000000-0000-0000-0000-000000000000/</Item>
                    <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
                    <Item Key="IdTokenAudience">00000000-0000-0000-0000-000000000000</Item>
                    <Item Key="response_types">id_token</Item>
                    <Item Key="UsePolicyInRedirectUri">false</Item>
                </Metadata>
                <CryptographicKeys>
                    <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
                </CryptographicKeys>
                <OutputClaims>
                    <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="oid"/>
                    <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
                    <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
                    <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
                    <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
                    <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="contosoAuthentication" />
                    <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="AzureADContoso" />
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

1. Sob o nó `<ClaimsProvider>`, atualize o valor de `<Domain>` para um valor exclusivo que pode ser usado para distinguir de outros provedores de identidade.
1. Sob o nó `<ClaimsProvider>`, atualize o valor de `<DisplayName>` para um nome amigável do provedor de declarações. Esse valor não é usado atualmente.

### <a name="update-the-technical-profile"></a>Atualizar o perfil técnico

Para obter um token do ponto de extremidade do Azure AD, você precisa definir os protocolos que o Azure AD B2C deve usar para se comunicar com o Azure AD. Isso é feito no elemento `<TechnicalProfile>` do `<ClaimsProvider>`.
1. Atualize a ID do nó `<TechnicalProfile>`. Essa ID é usada para se referir a esse perfil técnico em outras partes da política.
1. Atualize o valor de `<DisplayName>`. Esse valor será exibido no botão de entrada em sua tela de entrada.
1. Atualize o valor de `<Description>`.
1. O Azure AD usa o protocolo OpenID Connect, portanto, verifique se o valor de `<Protocol>` é `"OpenIdConnect"`.

Você precisa atualizar a seção `<Metadata>` no arquivo XML mencionado anteriormente para refletir as definições de configuração do locatário específico do Azure AD. No arquivo XML, atualize os valores de metadados da seguinte maneira:

1. Defina `<Item Key="METADATA">` como `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`, em que `yourAzureADtenant` é o nome do locatário do Azure AD (contoso.com).
1. Abra o navegador e navegue para a URL `METADATA` que você acabou de atualizar.
1. No navegador, procure o objeto “emissor” e copie seu valor. Ele deverá ter esta aparência: `https://sts.windows.net/{tenantId}/`.
1. Cole o valor de `<Item Key="ProviderName">` no arquivo XML.
1. Defina `<Item Key="client_id">` como a ID do aplicativo no registro de aplicativo.
1. Defina `<Item Key="IdTokenAudience">` como a ID do aplicativo no registro de aplicativo.
1. Verifique se `<Item Key="response_types">` está definido como `id_token`.
1. Verifique se `<Item Key="UsePolicyInRedirectUri">` está definido como `false`.

Você também precisa vincular o segredo do Azure AD que você registrou no locatário do Azure AD B2C às informações de `<ClaimsProvider>` do Azure AD:

* Na seção `<CryptographicKeys>` do arquivo XML anterior, atualize o valor de `StorageReferenceId` para a ID de referência do segredo definido (por exemplo, `ContosoAppSecret`).

### <a name="upload-the-extension-file-for-verification"></a>Carregar o arquivo de extensão para verificação

A essa altura, você já terá configurado a política, de forma que o Azure AD B2C saiba como se comunicar com o diretório do Azure AD. Tente carregar o arquivo de extensão da política apenas para confirmar se ele não apresenta problemas até o momento. Para fazer isso:

1. Abra a folha **Todas as Políticas** no locatário do Azure AD B2C.
1. Marque a caixa **Substituir a política caso ela exista**.
1. Carregue o arquivo de extensão (TrustFrameworkExtensions.xml) e verifique se ele não falhou na validação.

## <a name="register-the-azure-ad-claims-provider-to-a-user-journey"></a>Registrar o provedor de declarações do Azure AD em um percurso do usuário

Agora, você precisa adicionar o provedor de identidade do Azure AD a um dos percursos do usuário. Neste ponto, o provedor de identidade foi definido, mas não está disponível em nenhuma das telas de inscrição/entrada. Para disponibilizá-lo, criaremos uma duplicata de um modelo de percurso do usuário existente e, depois, o modificaremos para que ele também tenha o provedor de identidade do Azure AD:

1. Abra o arquivo base da política (por exemplo, TrustFrameworkBase.xml).
1. Localize o elemento `<UserJourneys>` e copie todo o nó `<UserJourney>`, que inclui `Id="SignUpOrSignIn"`.
1. Abra o arquivo de extensão (por exemplo, TrustFrameworkExtensions.xml) e localize o elemento `<UserJourneys>`. Se o elemento não existir, adicione um.
1. Cole todo o nó `<UserJourney>` copiado como um filho do elemento `<UserJourneys>`.
1. Renomeie a ID do novo Percurso do Usuário (por exemplo, renomeie como `SignUpOrSignUsingContoso`).

### <a name="display-the-button"></a>Exibir o “botão”

O elemento `<ClaimsProviderSelection>` é análogo a um botão de provedor de identidade em uma tela de inscrição/conexão. Se você adicionar um elemento `<ClaimsProviderSelection>` do Azure AD, um novo botão será exibido quando um usuário chegar à página. Para adicionar este elemento:

1. Localize o nó `<OrchestrationStep>` que inclui `Order="1"` no Percurso do Usuário que você acabou de criar.
1. Adicione o seguinte:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

1. Defina `TargetClaimsExchangeId` com um valor apropriado. Recomendamos seguir a mesma convenção que os outros: *\[ClaimProviderName\]Exchange*.

### <a name="link-the-button-to-an-action"></a>Vincular o botão a uma ação

Agora que implementou um botão, você precisará vinculá-lo a uma ação. Nesse caso, a ação destina-se a que o Azure AD B2C se comunique com o Azure AD para receber um token. Vincule o botão a uma ação vinculando o perfil técnico ao provedor de declarações do Azure AD:

1. Localize o `<OrchestrationStep>` que inclui `Order="2"` no nó `<UserJourney>`.
1. Adicione o seguinte:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

1. Atualize `Id` para o mesmo valor de `TargetClaimsExchangeId` na seção anterior.
1. Atualize `TechnicalProfileReferenceId` como a ID do perfil técnico você criou anteriormente (ContosoProfile).

### <a name="upload-the-updated-extension-file"></a>Carregar o arquivo de extensão atualizado

Agora você concluiu a modificação do arquivo de extensão. Salve-o. Em seguida, salve e carregue o arquivo e verifique se todas as validações são bem-sucedidas.

### <a name="update-the-rp-file"></a>Atualizar o arquivo RP

Agora, você precisa atualizar o arquivo RP (terceira parte confiável) que iniciará o percurso do usuário que você acabou de criar:

1. Faça uma cópia de o SignUpOrSignIn.xml em diretório de trabalho e renomeie-o (por exemplo, como SignUpOrSignInWithAAD.xml).
1. Abra o novo arquivo e atualize o atributo `PolicyId` de `<TrustFrameworkPolicy>` com um valor exclusivo (por exemplo, SignUpOrSignInWithAAD). <br> Esse será o nome da política (por exemplo, B2C\_1A\_SignUpOrSignInWithAAD).
1. Modifique o atributo `ReferenceId` em `<DefaultUserJourney>` para que ele corresponda à ID do novo percurso do usuário criado (por exemplo, SignUpOrSignUsingContoso).
1. Salve as alterações e carregue o arquivo.

## <a name="troubleshooting"></a>Solucionar problemas

Teste a política personalizada que você acabou de carregar abrindo sua folha e clicando em **Executar agora**. Para diagnosticar problemas, leia sobre [solução de problemas](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Próximas etapas

Envie seus comentários para [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com).
