---
title: "Azure Active Directory B2C: Como adicionar seus próprios atributos a políticas personalizadas e usar na edição de perfil | Microsoft Docs"
description: "Um passo a passo sobre como usar propriedades de extensão, atributos personalizados e incluí-los na interface de usuário"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: joroja
ms.openlocfilehash: f3e4eb6fedf850dbb827fd2a10593249d2f17ef1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-creating-and-using-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C: Como criar e usar atributos personalizados em uma política e edição de perfil personalizado

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Neste artigo, você criará um atributo personalizado no seu diretório do Azure AD B2C e usará esse novo atributo como uma declaração personalizada de percurso do usuário de edição de perfil.

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas no artigo [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-active-directory-b2c-using-custom-policies"></a>Use atributos personalizados para coletar informações sobre seus clientes no Azure Active Directory B2C usando políticas personalizadas
O diretório do Azure Active Directory (Azure AD) B2C é fornecido com um conjunto interno de atributos: Nome, Sobrenome, Cidade e CEP, entre outros atributos.  Geralmente, você precisa criar seus próprios atributos.  Por exemplo:
* Um aplicativo voltado para o cliente precisa manter um atributo, como "LoyaltyNumber".
* Um provedor de identidade tem um identificador exclusivo do usuário que deve ser salvo como "uniqueUserGUID".
* Um percurso do usuário personalizado precisa manter o estado do usuário, como "migrationStatus".

Com o Azure AD B2C, você pode estender o conjunto de atributos armazenados em cada conta de usuário. Você também pode ler e gravar esses atributos usando a [API do Graph do Azure AD](active-directory-b2c-devquickstarts-graph-dotnet.md).

Propriedades de extensão estendem o esquema dos objetos de usuário no diretório.  A propriedade de extensão de termos, atributo personalizado e declaração personalizada, se referem à mesma coisa no contexto deste artigo, e o nome varia dependendo do contexto (aplicativo, objeto, política).

As propriedades de extensão só podem ser registradas em um objeto do aplicativo, ainda que contenham dados de um usuário. A propriedade é anexada ao aplicativo. O objeto do aplicativo precisa obter acesso de gravação para registrar uma propriedade de extensão. É possível gravar 100 propriedades de extensão (entre TODOS os tipos e TODOS os aplicativos) em um único objeto. As propriedades de extensão são adicionadas para o tipo de diretório de destino e tornam-se imediatamente acessíveis no locatário de diretório do Azure AD B2C.
Se o aplicativo for excluído, tanto as propriedades de extensão quanto os dados contidos nelas para todos os usuários serão removidos. Se uma propriedade de extensão for excluída pelo aplicativo, ela será removida nos objetos do diretório de destino, e os dados valores serão excluídos.

As propriedades de extensão existem apenas no contexto de um aplicativo registrado no locatário. O id de objeto desse Aplicativo deve ser incluído no TechnicalProfile que o usa.

>[!NOTE]
>O diretório do Azure AD B2C geralmente inclui um aplicativo Web chamado `b2c-extensions-app`.  Este aplicativo é usado principalmente pelas políticas internas b2c para as declarações personalizadas criadas por meio do portal do Azure.  Recomenda-se que apenas usuários avançados usem este aplicativo para registrar extensões para as políticas personalizadas b2c.  As instruções para isso são incluídas na seção Próximas etapas neste artigo.


## <a name="creating-a-new-application-to-store-the-extension-properties"></a>Criação de um aplicativo novo para armazenar as propriedades de extensão

1. Abra uma sessão de navegação e navegue até o [Portal do Azure](https://portal.azure.com) e entre com as credenciais administrativas do diretório do B2C que deseja configurar.
1. Clique em **Azure Active Directory** no menu de navegação à esquerda. Talvez seja necessário localizá-lo, para isso, selecione Mais serviços>.
1. Selecione **Registros de aplicativo** e clique em **Novo registro de aplicativo**
1. Forneça as seguintes entradas recomendadas:
  * Especifique um nome para o aplicativo Web: **WebApp-GraphAPI-DirectoryExtensions**
  * Tipo de aplicativo: API/Aplicativo Web
  * Logon URL:https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions
1. Selecione **Criar. A conclusão bem-sucedida aparece nas **notificações**
1. Selecione o aplicativo Web criado recentemente: **WebApp-GraphAPI-DirectoryExtensions**
1. Selecione as configurações: **Permissões necessárias**
1. Selecione a API **Azure Active Directory do Windows**
1. Coloque uma marca de seleção em Permissões de Aplicativo: **Ler e gravar dados do diretório** e **Salvar**
1. Escolha **Conceder permissões** e confirme **Sim**.
1. Copie para a área de transferência e salve os seguintes identificadores de WebApp-GraphAPI-DirectoryExtensions>Settings>Properties>
*  **ID do aplicativo**. Exemplo: `103ee0e6-f92d-4183-b576-8c3739027780`
* **ID de objeto**. Exemplo: `80d8296a-da0a-49ee-b6ab-fd232aa45201`



## <a name="modifying-your-custom-policy-to-add-the-applicationobjectid"></a>Modificar a política personalizada para adicionar o ApplicationObjectId

```xml
    <ClaimsProviders>
        <ClaimsProvider>
              <DisplayName>Azure Active Directory</DisplayName>
            <TechnicalProfile Id="AAD-Common">
              <DisplayName>Azure Active Directory</DisplayName>
              <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
              <!-- Provide objectId and appId before using extension properties. -->
              <Metadata>
                <Item Key="ApplicationObjectId">insert objectId here</Item>
                <Item Key="ClientId">insert appId here</Item>
              </Metadata>
            <!-- End of changes -->
              <CryptographicKeys>
                <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
              </CryptographicKeys>
              <IncludeInSso>false</IncludeInSso>
              <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
            </TechnicalProfile>
        </ClaimsProvider>
    </ClaimsProviders>
```

>[!NOTE]
>O <TechnicalProfile Id="AAD-Common"> é conhecido como "comum", pois seus elementos são incluídos e reutilizados em todos os TechnicalProfiles do Azure Active Directory usando o elemento: `<IncludeTechnicalProfile ReferenceId="AAD-Common" />`

>[!NOTE]
>Às vezes, quando o TechnicalProfile grava pela primeira vez para a propriedade de extensão recém-criada, pode ocorrer um erro ocasional.  A propriedade de extensão é criada na primeira vez que ela é usada.  

## <a name="using-the-new-extension-property--custom-attribute-in-a-user-journey"></a>Como usar a nova propriedade de extensão / atributo personalizado em um percurso do usuário


1. Abra o arquivo da terceira parte confiável (RP) que descreve o seu percurso do usuário de edição da política.  Se você estiver começando, pode ser aconselhável baixar a versão já configurada do arquivo RP-PolicyEdit diretamente na seção Política de Personalização do Azure B2C no portal do Azure.  Como alternativa, abra o arquivo XML da pasta de armazenamento.
2. Adicione uma declaração personalizada `loyaltyId`.  Ao incluir uma declaração personalizada no elemento `<RelyingParty>`, ela será indicada como parâmetro para o TechnicalProfiles de percurso do usuário, além de ser incluída no token para o aplicativo.
```xml
<RelyingParty>
   <DefaultUserJourney ReferenceId="ProfileEdit" />
   <TechnicalProfile Id="PolicyProfile">
     <DisplayName>PolicyProfile</DisplayName>
     <Protocol Name="OpenIdConnect" />
     <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
       <OutputClaim ClaimTypeReferenceId="city" />

       <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />

     </OutputClaims>
     <SubjectNamingInfo ClaimType="sub" />
   </TechnicalProfile>
 </RelyingParty>
 ```
3. Adicione uma definição de declaração ao arquivo de política de extensão `TrustFrameworkExtensions.xml` dentro do elemento `<ClaimsSchema>`, conforme mostrado.
```xml
<ClaimsSchema>
        <ClaimType Id="extension_loyaltyId">
            <DisplayName>Loyalty Identification Tag</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Your loyalty number from your membership card</UserHelpText>
            <UserInputType>TextBox</UserInputType>
        </ClaimType>
</ClaimsSchema>
```
4. Adicione a mesma definição de declaração ao arquivo de política base `TrustFrameworkBase.xml`.  
>Geralmente, não é necessário adicionar uma definição `ClaimType` nos arquivos de extensões e de base, no entanto, como as próximas etapas adicionarão o extension_loyaltyId ao TechnicalProfiles no Arquivo Base, o validador de política rejeitará o upload do arquivo base sem ela.
>Poderá ser útil rastrear a execução do percurso do usuário chamado "ProfileEdit" no arquivo TrustFrameworkBase.xml.  Pesquise o percurso do usuário do mesmo nome no seu editor e observe que a orquestração da etapa 5 invoca o TechnicalProfileReferenceID="SelfAsserted-ProfileUpdate".  Pesquise e inspecione esse TechnicalProfile para se familiarizar com o fluxo.
5. Adicione o loyaltyId como declaração de entrada e saída no TechnicalProfile "SelfAsserted-ProfileUpdate"
```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
          <DisplayName>User ID signup</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ContentDefinitionReferenceId">api.selfasserted.profileupdate</Item>
          </Metadata>
          <IncludeInSso>false</IncludeInSso>
          <InputClaims>

            <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
            <InputClaim ClaimTypeReferenceId="userPrincipalName" />

            <!-- Optional claims. These claims are collected from the user and can be modified. Any claim added here should be updated in the
                 ValidationTechnicalProfile referenced below so it can be written to directory after being updateed by the user, i.e. AAD-UserWriteProfileUsingObjectId. -->
            <InputClaim ClaimTypeReferenceId="givenName" />
            <InputClaim ClaimTypeReferenceId="surname" />
            <InputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
          </InputClaims>
          <OutputClaims>
            <!-- Required claims -->
            <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />

            <!-- Optional claims. These claims are collected from the user and can be modified. Any claim added here should be updated in the
                 ValidationTechnicalProfile referenced below so it can be written to directory after being updateed by the user, i.e. AAD-UserWriteProfileUsingObjectId. -->
            <OutputClaim ClaimTypeReferenceId="givenName" />
            <OutputClaim ClaimTypeReferenceId="surname" />
            <OutputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="AAD-UserWriteProfileUsingObjectId" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
```
6. Adicione a declaração no TechnicalProfile "AAD-UserWriteProfileUsingObjectId" para manter o valor da declaração na propriedade de extensão para o usuário atual no diretório.
```xml
<TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
          <Metadata>
            <Item Key="Operation">Write</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">false</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
          </Metadata>
          <IncludeInSso>false</IncludeInSso>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
          </InputClaims>
          <PersistedClaims>
            <!-- Required claims -->
            <PersistedClaim ClaimTypeReferenceId="objectId" />

            <!-- Optional claims -->
            <PersistedClaim ClaimTypeReferenceId="givenName" />
            <PersistedClaim ClaimTypeReferenceId="surname" />
            <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />

          </PersistedClaims>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
```
7. Adicione a declaração no TechnicalProfile "AAD-UserReadUsingObjectId" para ler o valor do atributo de extensão sempre que um usuário fizer logon. Até o momento, os TechnicalProfiles só foram alterados no fluxo de contas locais.  Se quiser o novo atributo no fluxo de uma conta social/federada, será necessário alterar um conjunto diferente de TechnicalProfiles. Confira as Próximas etapas.

```xml
<!-- The following technical profile is used to read data after user authenticates. -->
     <TechnicalProfile Id="AAD-UserReadUsingObjectId">
       <Metadata>
         <Item Key="Operation">Read</Item>
         <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
       </Metadata>
       <IncludeInSso>false</IncludeInSso>
       <InputClaims>
         <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
       </InputClaims>
       <OutputClaims>
         <!-- Optional claims -->
         <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
         <OutputClaim ClaimTypeReferenceId="displayName" />
         <OutputClaim ClaimTypeReferenceId="otherMails" />
         <OutputClaim ClaimTypeReferenceId="givenName" />
         <OutputClaim ClaimTypeReferenceId="surname" />
         <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
       </OutputClaims>
       <IncludeTechnicalProfile ReferenceId="AAD-Common" />
     </TechnicalProfile>
```


>[!IMPORTANT]
>O elemento IncludeTechnicalProfile adiciona todos os elementos do AAD comum para esse TechnicalProfile.

## <a name="test-the-custom-policy-using-run-now"></a>Teste a política personalizada usando a opção "Executar Agora"
1. Abra a **Folha B2C do Azure AD** e navegue até **Identity Experience Framework > Políticas personalizadas**.
1. Selecione a política personalizada carregada e clique no botão **Executar agora**.
1. Você deverá conseguir se inscrever usando um endereço de email.

O token de id enviado novamente para o seu aplicativo inclui a propriedade de extensão nova como uma declaração personalizada precedida por extension_loyaltyId. Confira o exemplo.

```json
{
  "exp": 1493585187,
  "nbf": 1493581587,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493581587,
  "auth_time": 1493581587,
  "extension_loyaltyId": "abc",
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Próximas etapas

### <a name="add-the-new-claim-to-the-flows-for-social-account-logins-by-changing-the-technicalprofiles-listed-below-these-two-technicalprofiles-are-used-by-socialfederated-account-logins-to-write-and-read-the-user-data-using-the-alternativesecurityid-as-the-locator-of-the-user-object"></a>Adicione a nova declaração aos fluxos para logons de conta social alterando os TechnicalProfiles listados abaixo. Esses dois TechnicalProfiles são usados por logons de conta social/federados para gravar e ler os dados do usuário usando o alternativeSecurityId como o localizador do objeto de usuário.
```xml
  <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

  <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
```

Usando os mesmos atributos de extensão entre políticas internas e personalizadas.
Quando você adiciona atributos de extensão (também conhecido como atributos personalizados) por meio da experiência do portal, esses atributos são registrados usando o **b2c-extensions-app que existe em cada locatário B2C.  Para usar esses atributos de extensão em sua política personalizada:
1. Em seu locatário B2C no portal.azure.com, navegue até **Azure Active Directory** e selecione **Registros de aplicativo**
2. Localize seu **b2c-extensions-app** e selecione-o
3. Em "Essentials" registre a **ID do aplicativo** e a **ID do objeto**
4. Inclua-os em seus metadados de perfil técnico comuns do AAD da seguinte maneira:

```xml
    <ClaimsProviders>
        <ClaimsProvider>
              <DisplayName>Azure Active Directory</DisplayName>
            <TechnicalProfile Id="AAD-Common">
              <DisplayName>Azure Active Directory</DisplayName>
              <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
              <!-- Provide objectId and appId before using extension properties. -->
              <Metadata>
                <Item Key="ApplicationObjectId">insert objectId here</Item> <!-- This is the "Object ID" from the "b2c-extensions-app"-->
                <Item Key="ClientId">insert appId here</Item> <!--This is the "Application ID" from the "b2c-extensions-app"-->
              </Metadata>
```

Para manter a consistência com a experiência do portal, crie esses atributos usando a interface do usuário do portal *antes* de usá-los em suas políticas personalizadas.  Quando você cria um atributo "ActivationStatus" no portal, é necessário fazer referência a ele da seguinte maneira:

```
extension_ActivationStatus in the custom policy
extension_<app-guid>_ActivationStatus via the Graph API.
```


## <a name="reference"></a>Referência

* O **Perfil técnico (TP)** é um tipo de elemento que pode ser pensado como uma *função* que define o nome de um ponto de extremidade, seus metadados, seu protocolo e os detalhes da troca de declarações que o Identity Experience Framework deve executar.  Quando essa *função* é chamada em uma etapa de orquestração ou de outro TechnicalProfile, o InputClaims e o OutputClaims são fornecidos como parâmetros pelo chamador.


* Para tratamento completo em propriedades de extensão, confira o artigo [EXTENSÕES DE ESQUEMA DE DIRETÓRIO | CONCEITOS DA API DO GRAPH](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)

>[!NOTE]
>Atributos de extensão na API do Graph são nomeados usando a convenção `extension_ApplicationObjectID_attributename`. Políticas personalizadas se referem aos atributos de extensões como extension_attributename, omitindo o ApplicationObjectId no XML
