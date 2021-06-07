---
title: Personalização de idioma no Azure Active Directory B2C
description: Saiba mais sobre como personalizar a experiência de idioma nos seus fluxos dos usuários no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 418f0797343a64728c4e48084b09bd0e426cec62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101686403"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Personalização de idioma no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

A personalização de idioma no Azure AD B2C (Azure Active Directory B2C) permite que o fluxo de usuário acomode diferentes idiomas para atender às necessidades do cliente. A Microsoft fornece as traduções para [36 idiomas](#supported-languages), mas você também pode fornecer suas próprias traduções para qualquer idioma. Mesmo que sua experiência seja fornecida apenas para um único idioma, você pode personalizar qualquer texto nas páginas.

## <a name="how-language-customization-works"></a>Como funciona a personalização de idioma

Você usa a personalização de idioma para selecionar em quais idiomas o fluxo de usuários está disponível. Depois que o recurso estiver habilitado, você poderá fornecer o parâmetro da cadeia de caracteres de consulta `ui_locales`, a partir do seu aplicativo. Quando você chama o Azure AD B2C, sua página é traduzida para a localidade que você indicou. Esse tipo de configuração oferece controle total sobre os idiomas no fluxo do usuário e ignora as configurações de idioma do navegador do cliente.

Talvez você não precise desse nível de controle sobre quais idiomas seu cliente vê. Se você não fornecer um parâmetro `ui_locales` a experiência do cliente será determinada pelas configurações do navegador. Você ainda poderá controlar para quais idiomas o fluxo do usuário será traduzido, adicionando-o como um idioma com suporte. Se o navegador de um cliente estiver configurado para mostrar um idioma que você não deseja fornecer suporte, o idioma que você selecionou como padrão nas culturas com suporte será mostrado.

* **Idioma especificado por ui-locales**: após habilitar a personalização do idioma, o fluxo do usuário será traduzido para o idioma especificado aqui.
* **Idioma solicitado pelo navegador**: se nenhum parâmetro `ui_locales` for especificado, o fluxo do usuário será traduzido para o idioma solicitado pelo navegador, *se houver suporte para o idioma*.
* **Idioma padrão da política**: se o navegador não especificar um idioma ou especificar um que não tenha suporte, o fluxo do usuário será traduzido para o idioma padrão do fluxo do usuário.

> [!NOTE]
> Se você estiver usando atributos de usuário personalizados, precisará fornecer suas próprias traduções. Para obter mais informações, consulte [Personalizar as cadeias de caracteres](#customize-your-strings).

::: zone pivot="b2c-custom-policy"

A localização requer três etapas: 

1. Configurar a lista explícita de idiomas com suporte
1. Fornecer coleções e cadeias de caracteres específicas a um idioma
1. Editar a [definição de conteúdo](contentdefinitions.md) da página. 

::: zone-end 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="support-requested-languages-for-ui_locales"></a>Solicitação de suporte de idiomas para ui_locales

Políticas criadas antes da disponibilidade geral de personalização de idioma precisarão, primeiro, habilitar esse recurso. Políticas e fluxos dos usuários que foram criados após a personalização de idioma são habilitados por padrão.

Ao habilitar a personalização de idioma em um fluxo de usuário, é possível controlar o idioma do fluxo de usuário, adicionando o parâmetro `ui_locales`.

1. No locatário do Azure AD B2C, selecione **Fluxos dos usuários**.
1. Clique no fluxo de usuário que você quer habilitar para traduções.
1. Selecione os **Idiomas**.
1. Selecione **Habilitar personalização de idioma**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Selecionar quais idiomas no fluxo de usuários estão habilitados

Habilite um conjunto de idiomas para que o fluxo de usuário seja traduzido quando solicitado pelo navegador sem o parâmetro `ui_locales`.

1. Certifique-se de que o fluxo de usuário tenha a personalização de idioma habilitada a partir das instruções anteriores.
1. Na página **Idiomas** do fluxo de usuário, selecione um idioma que você quer dar suporte.
1. No painel de propriedades, altere **Habilitado** para **Sim**.
1. Selecione **Salvar** na parte superior do painel de propriedades.

>[!NOTE]
>Se um parâmetro `ui_locales` não for fornecido, a página será traduzida para o idioma do navegador do cliente somente se estiver habilitada.
>

## <a name="customize-your-strings"></a>Como personalizar suas cadeias de caracteres

A personalização de idioma permite personalizar qualquer cadeia de caracteres no fluxo de usuário.

1. Certifique-se de que o fluxo de usuário tenha a personalização de idioma habilitada a partir das instruções anteriores.
1. Na página **Idiomas** do fluxo de usuário, selecione o idioma que você quer personalizar.
1. Em **Arquivos de recursos de nível de página**, selecione a página que você quer editar.
1. Selecione **Baixar Padrões** (ou **Baixar substituições**, se esse idioma já foi editado anteriormente).

Essas etapas fornecem um arquivo JSON que você pode usar para começar a editar suas cadeias de caracteres.

### <a name="change-any-string-on-the-page"></a>Alterar qualquer cadeia de caractere na página

1. Abra o arquivo JSON baixado das instruções anteriores em um editor de JSON.
1. Localize o elemento que você deseja alterar. Você pode localizar `StringId` para a cadeia de caracteres que está procurando ou procurar o atributo `Value` que deseja alterar.
1. Atualize o atributo `Value` com o que você deseja exibir.
1. Para cada cadeia de caracteres que você deseja alterar, altere `Override` para `true`.
1. Salve o arquivo e carregue suas alterações. (É possível localizar o controle de upload no mesmo local de onde baixou o arquivo JSON.)

> [!IMPORTANT]
> Se você precisar substituir uma cadeia de caracteres, certifique-se de que o valor `Override` esteja definido para `true`. Se o valor não mudou, a entrada será ignorada.

### <a name="change-extension-attributes"></a>Alterar atributos de extensão

Se você quiser alterar a cadeia de caracteres de um atributo de usuário personalizado ou se deseja adicionar um ao JSON, ele estará no seguinte formato:

```json
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
  ]
}
```

Substitua `<ExtensionAttribute>` pelo nome do seu atributo de usuário personalizado.

Substitua `<ExtensionAttributeValue>` por uma cadeia de caracteres nova a ser exibida.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Fornecer uma lista de valores usando LocalizedCollections

Se você quiser fornecer uma lista configurada de valores para respostas, será necessário criar um atributo `LocalizedCollections`. `LocalizedCollections` é uma matriz de pares `Name` e `Value`. A ordem dos itens será a ordem em que elas são exibidas. Para adicionar `LocalizedCollections`, use o formato a seguir:

```json
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [
    {
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
        {
          "Name":"<Response1>",
          "Value":"<Value1>"
        },
        {
          "Name":"<Response2>",
          "Value":"<Value2>"
        }
      ]
    }
  ]
}
```

* `ElementId` é o atributo do usuário ao qual esse atributo `LocalizedCollections` é uma resposta.
* `Name` é o valor mostrado para o usuário.
* `Value` é o que é retornado na declaração quando essa opção é selecionada.

### <a name="upload-your-changes"></a>Carregamento das suas alterações

1. Após concluir as alterações no arquivo JSON, retorne para o locatário B2C.
1. Selecione **Fluxos dos Usuários** e clique no fluxo de usuário que você quer habilitar para as traduções.
1. Selecione os **Idiomas**.
1. Selecione o idioma para o qual você deseja traduzir.
1. Selecione a página onde você deseja fornecer traduções.
1. Selecione o ícone da pasta e selecione o arquivo JSON para upload.

As alterações são salvas no fluxo de usuário automaticamente.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Personalizar a interface do usuário da página usando personalização de idioma

Há duas maneiras de localizar o [conteúdo HTML](customize-ui-with-html.md). Uma maneira é ativar a [personalização de idioma](language-customization.md). Habilitar esse recurso permite que o Azure AD B2C encaminhe o parâmetro do OpenID Connect `ui-locales` para o seu ponto de extremidade. O servidor de conteúdo pode usar esse parâmetro para fornecer páginas HTML personalizadas que são específicas a um idioma.

Como alternativa, é possível extrair conteúdo de lugares diferentes com base na localidade que está sendo usada. No ponto de extremidade habilitado para CORS, é possível configurar uma estrutura de pastas para hospedar conteúdo para idiomas específicos. Você chamará adequadamente se usar o valor curinga `{Culture:RFC5646}`. Por exemplo, suponha que essa é a sua URI de página personalizada:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

Você pode carregar a página em `fr`. Quando a página efetua pulls de conteúdo HTML e CSS, ela efetua pulls de:

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Adicionar idiomas personalizados

Também é possível adicionar idiomas para os quais a Microsoft atualmente não oferece traduções. Será necessário fornecer as traduções para todas as cadeias de caracteres no fluxo de usuário. Códigos de idioma e localidade são limitados a esses no padrão ISO 639-1. O formato de código de localidade deve ser "ISO_639-1_code"-"CountryCode", por exemplo, `en-GB`. Para obter mais informações sobre formatos de identificação de localidade, confira https://docs.microsoft.com/openspecs/office_standards/ms-oe376/6c085406-a698-4e12-9d4d-c3b0ee3dbc4a

1. No locatário do Azure AD B2C, selecione **Fluxos dos usuários**.
2. Clique no fluxo de usuário no qual você quer adicionar idiomas personalizados e, em seguida, clique em **Idiomas**.
3. Selecione **Adicionar idioma personalizado** a partir da parte superior da página.
4. No painel de contexto aberto, identifique o idioma para o qual você está fornecendo traduções, inserindo um código de localidade válido.
5. Para cada página, você poderá baixar um conjunto de substituições para o idioma inglês e trabalhar nas traduções.
6. Após concluir os arquivos JSON, você poderá enviá-los para cada página.
7. Selecione **Habilitar** e o fluxo de usuário agora poderá mostrar esse idioma para seus usuários.
8. Salve o idioma.

>[!IMPORTANT]
>Você precisa habilitar os idiomas personalizados ou carregar substituições para ele antes de salvar.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="set-up-the-list-of-supported-languages"></a>Configurar uma lista dos idiomas com suporte

Abra o arquivo de extensões da sua política. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.

1. Pesquise o elemento [BuildingBlocks](buildingblocks.md). Se o elemento não existir, adicione-o.
1. Adicione o elemento `Localization` com os idiomas com suporte: Inglês (padrão) e espanhol.  


```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

## <a name="provide-language-specific-labels"></a>Fornecer rótulos específicos a um idioma

O [LocalizedResources](localization.md#localizedresources) do elemento `Localization` contém a lista de cadeias de caracteres localizadas. Um elemento de recursos localizados conta com um identificador que é usado para identificar exclusivamente os recursos localizados. Esse identificador é usado posteriormente no elemento de [definição de conteúdo](contentdefinitions.md).

Você configura elementos de recursos localizados para a definição de conteúdo e a qualquer idioma ao qual você deseje dar suporte. Para personalizar as páginas de inscrição ou de entrada unificadas para inglês e espanhol, adicione os seguintes elementos de `LocalizedResources` após o fechamento do elemento `</SupportedLanguages>`.

> [!NOTE]
> No exemplo a seguir, adicionamos o símbolo de tralha `#` no início de cada linha, para que você possa localizar facilmente os rótulos localizados na tela.

```xml
<!--Local account sign-up or sign-in page English-->
<Localization Enabled="true">
  ...
  <LocalizedResources Id="api.signuporsignin.en">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Email Address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Please enter your email</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Username</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Sign up now</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Please enter your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#Don't have an account?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#Forgot your password?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#OR</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#The user has forgotten their password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Sign in</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Sign in with your social account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Please enter your password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#The password you entered is not in the expected format.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Sign in with your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Sign in with your existing account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Please enter a valid email address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#We are having trouble signing you in. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Your password is incorrect.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#We can't seem to find your account.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Looks like you used an old password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#Invalid username or password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Your account has been locked. Contact your support person to unlock it, then try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Your account is temporarily locked to prevent unauthorized use. Try again later.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up or sign-in page Spanish-->
  <LocalizedResources Id="api.signuporsignin.es">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Este campo es obligatorio</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Registrarse ahora</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Escriba su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#¿No tiene una cuenta?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#¿Olvidó su contraseña?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#O</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#El usuario ha olvidado su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Iniciar sesión</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Iniciar sesión con su cuenta de redes sociales</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Escriba su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#La contraseña que ha escrito no está en el formato esperado.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Iniciar sesión con su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Iniciar sesión con su cuenta existente</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Escriba una dirección de correo electrónico válida</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#Tenemos problemas para iniciar su sesión. Vuelva a intentarlo más tarde.  </LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Su contraseña es incorrecta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#Parece que no podemos encontrar su cuenta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Parece que ha usado una contraseña antigua.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#El nombre de usuario o la contraseña no son válidos.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Se bloqueó su cuenta. Póngase en contacto con la persona responsable de soporte técnico para desbloquearla y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Su cuenta se bloqueó temporalmente para impedir un uso no autorizado. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page English-->
  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Email Address</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Email address that can be used to contact you.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Please enter a valid email address.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Enter new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirm New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirm new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Display Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Your display name.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Surname</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Your surname (also known as family name or last name).  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Given Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Your given name (also known as first name).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Create</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#One or more fields are filled out incorrectly. Please check your entries and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#The password entry fields do not match. Please enter the same password in both fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#A required field is missing. Please fill out all required fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#What is this?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Please provide the following details.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Please wait</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#This information is required.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Change e-mail</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Send new code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Send verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Verify code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#That code is expired. Please request a new code.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#You've made too many incorrect attempts. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#That code is incorrect. Please try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#Verification is necessary. Please click Send button.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#E-mail address verified. You can now continue.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Claim not verified: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#A user with the specified ID already exists. Please choose a different one.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Incorrect pattern for: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} has invalid input.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Missing required element: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error in validation by: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page Spanish-->
  <LocalizedResources Id="api.localaccountsignup.es">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Dirección de correo electrónico</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Introduzca una dirección de correo electrónico válida.  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#Nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Escriba la contraseña nueva</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#De 8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Nombre para mostrar</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Su nombre para mostrar.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Apellido</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Su apellido.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Nombre</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Su nombre (también conocido como nombre de pila).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Crear</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#Hay uno o varios campos rellenados de forma incorrecta. Compruebe las entradas y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#Los campos de entrada de contraseña no coinciden. Escriba la misma contraseña en ambos campos y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#Falta un campo obligatorio. Rellene todos los campos necesarios y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#¿Qué es esto?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Proporcione los siguientes detalles.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Espere</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#Esta información es obligatoria.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Cambiar correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Enviar nuevo código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Enviar código de comprobación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Comprobar código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#El código ha expirado. Solicite otro nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#Ha realizado demasiados intentos incorrectos. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#Ese código es incorrecto. Inténtelo de nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#Tenemos problemas para comprobar la dirección de correo electrónico. Escriba una dirección de correo electrónico válida y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#Ha habido demasiadas solicitudes para comprobar esta dirección de correo electrónico. Espere un poco y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Se ha enviado el código de verificación a su Bandeja de entrada. Cópielo en el siguiente cuadro de entrada.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Código de verificación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#La comprobación es obligatoria. Haga clic en el botón Enviar.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#Dirección de correo electrónico comprobada. Puede continuar.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Reclamación no comprobada: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Patrón incorrecto para: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} tiene una entrada no válida.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Falta un elemento obligatorio: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error en la validación de: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

## <a name="edit-the-content-definition-with-the-localization"></a>Editar a definição de conteúdo com a localização

Cole todo o conteúdo do elemento ContentDefinitions que você copiou como filho do elemento BuildingBlocks.

No exemplo a seguir, as cadeias de caracteres personalizadas em inglês (en) e espanhol (es) são adicionadas à página de inscrição ou de entrada, bem como à página de inscrição da conta local. **LocalizedResourcesReferenceId** para cada **LocalizedResourcesReference** é igual à localidade, mas você pode usar qualquer cadeia de caracteres como identificador. Para cada combinação de idioma e página, aponte para os **LocalizedResources** correspondentes criados anteriormente.

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.signuporsignin">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>

  <ContentDefinition Id="api.localaccountsignup">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>
</ContentDefinitions>
```

##  <a name="upload-and-test-your-updated-custom-policy"></a>Carregar e testar a política personalizada atualizada

### <a name="upload-the-custom-policy"></a>Carregar a política personalizada

1. Salve o arquivo de extensões.
1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
1. Pesquise e selecione **Azure AD B2C**.
1. Em **Políticas**, selecione **Identity Experience Framework**.
1. Selecione **Carregar política personalizada**.
1. Carregue o arquivo de extensões que você alterou anteriormente.

### <a name="test-the-custom-policy-by-using-run-now"></a>Teste a política personalizada usando a opção **Executar Agora**

1. Selecione a política que você carregou e, em seguida, selecione **Executar agora**.
1. Você deve ser capaz de ver a página de inscrição ou de entrada localizada.
1. Clique no link de inscrição e você poderá ver a página de inscrição localizada.
1. Mude o idioma padrão do navegador para espanhol. Ou você pode adicionar o parâmetro de cadeia de caracteres de consulta `ui_locales` à solicitação de autorização. Por exemplo: 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize&client_id=0239a9cc-309c-4d41-12f1-31299feb2e82&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&ui_locales=es
```

::: zone-end


## <a name="additional-information"></a>Informações adicionais

::: zone pivot="b2c-user-flow"

### <a name="page-ui-customization-labels-as-overrides"></a>Rótulos de personalização da interface do usuário da página como substituições

Ao habilitar a personalização de idioma, as edições anteriores para rótulos usando a personalização da interface do usuário da página são persistidas em um arquivo JSON para inglês (en). Você pode continuar a alterar os rótulos e outras cadeias de caracteres, carregando recursos de idiomas na personalização de idioma.

::: zone-end

### <a name="up-to-date-translations"></a>Atualizar traduções

A Microsoft está comprometida em fornecer as traduções mais atualizadas para seu uso. A Microsoft aprimora continuamente as traduções e as mantém em conformidade para você. A Microsoft identificará bugs e alterações na terminologia global e fará atualizações que funcionarão diretamente no fluxo de usuário.

### <a name="support-for-right-to-left-languages"></a>Suporte para idiomas da direita para a esquerda

A Microsoft atualmente não fornece suporte para idiomas escritos da direita para a esquerda. Você pode fazer isso usando localidades personalizadas e CSS para alterar a maneira como as cadeias de caracteres são exibidas. Se você precisar desse recurso, vote para ele nos [Comentários do Azure](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).

### <a name="social-identity-provider-translations"></a>Traduções de provedor de identidade social

A Microsoft fornece o parâmetro OIDC `ui_locales` para logons sociais. Mas alguns provedores de identidade social, incluindo o Facebook e o Google, não os consideram.

### <a name="browser-behavior"></a>Comportamento do navegador

Ambos Chrome e o Firefox solicitam o idioma definido. Se for um idioma com suporte, será exibido antes do padrão. Atualmente, o Microsoft Edge não solicita um idioma e exibe diretamente o idioma padrão.

## <a name="supported-languages"></a>Idiomas com suporte

O Azure AD B2C inclui suporte para os idiomas a seguir. Os idiomas do fluxo do usuário são fornecidos pelo Azure AD B2C. Os idiomas de notificação da MFA (Autenticação Multifator) são fornecidos pela [MFA do Azure AD](../active-directory/authentication/concept-mfa-howitworks.md).

| Linguagem              | Código de idioma | Fluxos de usuário         | Notificações da MFA  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Árabe                | ar            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Búlgaro             | bg            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Bangla                | bn            | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![X indicando não.](./media/user-flow-language-customization/no.png) |
| Catalão               | ca            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Tcheco                 | cs            | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Dinamarquês                | da            | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Alemão                | de            | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Grego                 | el            | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Inglês               | en            | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Espanhol               | es            | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Estoniano              | et            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Basco                | eu            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Finlandês               | fi            | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Francês                | fr            | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Galego              | gl            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Guzerate              | gu            | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![X indicando não.](./media/user-flow-language-customization/no.png) |
| Hebraico                | he            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Híndi                 | hi            | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Croata              | hr            | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Húngaro             | hu            | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Indonésio            | id            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Italiano               | it            | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Japonês              | ja            | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Cazaque                | kk            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| canarim               | kn            | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![X indicando não.](./media/user-flow-language-customization/no.png) |
| Coreano                | ko            | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Lituano            | lt            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Letão               | lv            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Malaiala             | ml            | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![X indicando não.](./media/user-flow-language-customization/no.png) |
| Marati               | mr            | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![X indicando não.](./media/user-flow-language-customization/no.png) |
| Malaio                 | ms            | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Norueguês Bokmal      | nb            | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![X indicando não.](./media/user-flow-language-customization/no.png) |
| Holandês                 | nl            | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Norueguês             | não            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Panjabi               | pa            | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![X indicando não.](./media/user-flow-language-customization/no.png) |
| Polonês                | pl            | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Português - Brasil   | pt-br         | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Português - Portugal | pt-pt         | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Romeno              | ro            | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Russo               | ru            | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Eslovaco                | sk            | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Esloveno             | sl            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Sérvio - Cirílico    | sr-cryl-cs    | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Sérvio - latino       | sr-latn-cs    | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Sueco               | sv            | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Tâmil                 | ta            | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![X indicando não.](./media/user-flow-language-customization/no.png) |
| Télugo                | te            | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![X indicando não.](./media/user-flow-language-customization/no.png) |
| Tailandês                  | th            | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Turco               | tr            | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Ucraniano             | uk            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Vietnamita            | vi            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Chinês - Simplificado  | zh-hans       | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |
| Chinês - Tradicional | zh-hant       | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) | ![Marca de seleção verde.](./media/user-flow-language-customization/yes.png) |


## <a name="next-steps"></a>Próximas etapas

::: zone pivot="b2c-user-flow"

Encontre mais informações sobre como personalizar a interface do usuário dos seus aplicativos em [Personalizar a interface do usuário do aplicativo no Azure Active Directory B2C](customize-ui-with-html.md).

::: zone-end 

::: zone pivot="b2c-custom-policy"

- Saiba mais sobre o elemento [localização](localization.md) na referência de IEF.
- Consulte a lista de [IDs de cadeia de caracteres de localização](localization-string-ids.md) disponível no Azure AD B2C.

::: zone-end 
