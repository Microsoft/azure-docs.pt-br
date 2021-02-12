---
title: IDs de cadeias de caracteres de localização – Azure Active Directory B2C | Microsoft Docs
description: Especifique as IDs de uma definição de conteúdo com uma ID de api.signuporsignin em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 11/09/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 81a1263d0eacbffa77e2e35e4594e23235394183
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108664"
---
# <a name="localization-string-ids"></a>IDs de cadeia de caracteres de localização

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O elemento **Localization** permite que você dê suporte a várias localidades ou idiomas na política para os percursos do usuário. Este artigo fornece uma lista de IDs de localização que você pode usar em sua política. Para se familiarizar com a localização da interface do usuário, confira [Localização](localization.md).

## <a name="sign-up-or-sign-in-page-elements"></a>Elementos das páginas de inscrição ou entrada

As IDs a seguir são usadas para uma definição de conteúdo com uma ID `api.signuporsignin` e um [perfil técnico autodeclarado](self-asserted-technical-profile.md).

| ID | Valor padrão | Versão de layout de página |
| -- | ------------- | ------ |
| **forgotpassword_link** | Esqueceu sua senha? | `All` |
| **createaccount_intro** | Não tem uma conta? | `All` |
| **button_signin** | Entrar | `All` |
| **social_intro** | Entre com sua conta de redes sociais | `All` |
| **remember_me** |Mantenha-me conectado. | `All` |
| **unknown_error** | Estamos com problemas para conectá-lo. Tente novamente mais tarde. | `All` |
| **divider_title** | OU | `All` |
| **local_intro_email** | Entre com sua conta existente | `< 2.0.0` |
| **logonIdentifier_email** | Endereço de Email | `< 2.0.0` |
| **requiredField_email** | Insira seu email | `< 2.0.0` |
| **invalid_email** | Insira um endereço de email válido | `< 2.0.0` |
| **email_pattern** | ^ [a-zA-Z0-9.! # $% & ' ' \* +/=? ^ \_ \` { \| } ~-] + @ [a-zA-Z0-9-] + (?: \\ . [ a-zA-Z0-9-] +) \* $ |`< 2.0.0` |
| **local_intro_username** | Entre com seu nome de usuário | `< 2.0.0` |
| **logonIdentifier_username** | Nome de Usuário | `< 2.0.0` |
| **requiredField_username** | Insira seu nome de usuário | `< 2.0.0` |
| **password** | Senha | `< 2.0.0` |
| **requiredField_password** | Digite sua senha | `< 2.0.0` |
| **createaccount_link** | Inscreva-se agora mesmo | `< 2.0.0` |
| **cancel_message** | O usuário esqueceu a senha | `< 2.0.0` |
| **invalid_password** | A senha digitada não está no formato esperado. | `< 2.0.0` |
| **createaccount_one_link** | Inscreva-se agora mesmo | `>= 2.0.0` |
| **createaccount_two_links** | Inscreva-se com {0} ou {1} | `>= 2.0.0` |
| **createaccount_three_links** | Inscreva-se com {0} , {1} ou {2} | `>= 2.0.0` |
| **local_intro_generic** | Entre com seu {0} | `>= 2.1.0` |
| **requiredField_generic** | Insira seu {0} | `>= 2.1.0` |
| **invalid_generic** | Insira um nome válido {0} | `>= 2.1.1` |
| **seção** | Entrar | `>= 2.1.1` |


> [!NOTE]
> * Espaços reservados como {0} serão preenchidos automaticamente com o `DisplayName` valor de `ClaimType` . 
> * Para saber como localizar `ClaimType` , consulte [exemplo de inscrição ou entrada](#signupsigninexample).

O exemplo a seguir mostra o uso de alguns dos elementos de interface do usuário na página de inscrição ou entrada:

:::image type="content" source="./media/localization-string-ids/localization-susi-2.png" alt-text="Captura de tela que mostra os elementos da página de inscrição ou entrada U X.":::

### <a name="sign-up-or-sign-in-identity-providers"></a>Provedores de identidade de inscrição ou entrada

A ID dos provedores de identidade é configurada no elemento **ClaimsExchange** do percurso do usuário. Para localizar o título do provedor de identidade, **ElementType** é definido como `ClaimsProvider`, enquanto **StringId** é definido como a ID do `ClaimsExchange`.

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="MicrosoftExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccount" />
  </ClaimsExchanges>
</OrchestrationStep>
```

O exemplo a seguir localiza o provedor de identidade do Facebook para árabe:

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

### <a name="sign-up-or-sign-in-error-messages"></a>Mensagens de erro de entrada ou inscrição

| ID | Valor padrão |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | A senha está incorreta. |
| **UserMessageIfPasswordExpired**| A senha expirou.|
| **UserMessageIfClaimsPrincipalDoesNotExist** | Não estamos encontrando a sua conta. |
| **UserMessageIfOldPasswordUsed** | Parece que você usou uma senha antiga. |
| **DefaultMessage** | Senha ou nome de usuário inválido. |
| **UserMessageIfUserAccountDisabled** | Sua conta foi bloqueada. Contate seu suporte para desbloqueá-la e tente novamente. |
| **UserMessageIfUserAccountLocked** | Sua conta está temporariamente bloqueada para impedir o uso não autorizado. Tente novamente depois. |
| **AADRequestsThrottled** | Há muitas solicitações no momento. Aguarde alguns instantes e tente novamente. |

<a name="signupsigninexample"></a>
### <a name="sign-up-or-sign-in-example"></a>Exemplo de inscrição ou entrada

```xml
<LocalizedResources Id="api.signuporsignin.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email Address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="heading">Sign in</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="social_intro">Sign in with your social account</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="local_intro_generic">Sign in with your {0}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_password">Please enter your password</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_generic">Please enter your {0}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_generic">Please enter a valid {0}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_one_link">Sign up now</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_two_links">Sign up with {0} or {1}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_three_links">Sign up with {0}, {1}, or {2}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">Forgot your password?</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_signin">Sign in</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="divider_title">OR</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="unknown_error">We are having trouble signing you in. Please try again later.</LocalizedString>
    <!-- Uncomment the remember_me only if the keep me signed in is activated. 
    <LocalizedString ElementType="UxElement" StringId="remember_me">Keep me signed in</LocalizedString> -->
    <LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">Your password is incorrect.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfPasswordExpired">Your password has expired.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">Looks like you used an old password.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">Invalid username or password.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">Your account has been locked. Contact your support person to unlock it, then try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">Your account is temporarily locked to prevent unauthorized use. Try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Elementos da interface do usuário de páginas autodeclaradas e de entrada

Veja a seguir as IDs de uma definição de conteúdo com uma ID `api.localaccountsignup` ou qualquer definição de conteúdo que comece com, como e e o `api.selfasserted` `api.selfasserted.profileupdate` `api.localaccountpasswordreset` [perfil técnico autodeclarado](self-asserted-technical-profile.md).

| ID | Valor padrão |
| -- | ------------- |
| **ver_sent** | O código de verificação foi enviado para: |
| **ver_but_default** | Padrão |
| **cancel_message** | O usuário cancelou a inserção de informações autodeclaradas |
| **preloader_alt** | Aguarde |
| **ver_but_send** | Enviar código de verificação |
| **alert_yes** | Sim |
| **error_fieldIncorrect** | Um ou mais campos estão preenchidos incorretamente. Verifique suas entradas e tente novamente. |
| **year** | Ano |
| **verifying_blurb** | Aguarde enquanto processamos suas informações. |
| **button_cancel** | Cancelar |
| **ver_fail_no_retry** | Você fez muitas tentativas incorretas. Tente novamente mais tarde. |
| **month** | Month |
| **ver_success_msg** | Endereço de email verificado. Agora, você pode continuar. |
| **months** | Janeiro, fevereiro, março, abril, maio, junho, julho, agosto, setembro, outubro, novembro, dezembro |
| **ver_fail_server** | Estamos com problemas para verificar seu endereço de email. Insira um endereço de email válido e tente novamente. |
| **error_requiredFieldMissing** | Um campo obrigatório está ausente. Preencha todos os campos obrigatórios e tente novamente. |
| **initial_intro** | Forneça os seguintes detalhes. |
| **ver_but_resend** | Enviar novo código |
| **button_continue** | Criar |
| **error_passwordEntryMismatch** | Os campos de entrada de senha não correspondem. Insira a mesma senha nos dois campos e tente novamente. |
| **ver_incorrect_format** | Formato incorreto. |
| **ver_but_edit** | Alterar email |
| **ver_but_verify** | Verificar código |
| **alert_no** | Não |
| **ver_info_msg** | O código de verificação foi enviado para sua caixa de entrada. Copie-o para a caixa de entrada abaixo. |
| **day** | Dia |
| **ver_fail_throttled** | Houve muitas solicitações para verificar este endereço de email. Aguarde alguns instantes e tente novamente. |
| **helplink_text** | O que é isso? |
| **ver_fail_retry** | Esse código está incorreto. Tente novamente. |
| **alert_title** | Cancelar Inserção de Detalhes |
| **required_field** | Estas informações são necessárias. |
| **alert_message** | Tem certeza de que deseja cancelar a inserção de detalhes? |
| **ver_intro_msg** | A verificação é necessária. Clique no botão Enviar. |
| **ver_input** | Código de verificação |

### <a name="sign-up-and-self-asserted-pages-error-messages"></a>Mensagens de erro de páginas autodeclaradas e de entrada

| ID | Valor padrão |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | Já existe um usuário com a ID especificada. Escolha uma ID diferente. |
| **UserMessageIfClaimNotVerified** | Declaração não verificada: {0} |
| **UserMessageIfIncorrectPattern** | Padrão incorreto para: {0} |
| **UserMessageIfMissingRequiredElement** | Elemento obrigatório ausente: {0} |
| **UserMessageIfValidationError** | Erro na validação por: {0} |
| **UserMessageIfInvalidInput** | {0} tem uma entrada inválida. |
| **ServiceThrottled** | Há muitas solicitações no momento. Aguarde alguns instantes e tente novamente. |

O exemplo a seguir mostra o uso de alguns dos elementos de interface do usuário na página de inscrição:

![Página de inscrição com seus nomes de elementos de interface do usuário rotulados](./media/localization-string-ids/localization-sign-up.png)

O exemplo a seguir mostra o uso de alguns dos elementos de interface do usuário na página de entrada após o usuário clicar no botão e envio do código de verificação:

![Elementos de experiência do usuário da verificação de email na página de inscrição](./media/localization-string-ids/localization-email-verification.png)

## <a name="sign-up-and-self-asserted-pages-example"></a>Exemplo de inscrição e páginas autodeclaradas

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email Address</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Email address that can be used to contact you.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">New Password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">Enter new password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">Confirm New Password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">Confirm new password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">Display Name</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">Your display name.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">Surname</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">Your surname (also known as family name or last name).</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">Given Name</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">Your given name (also known as first name).</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">One or more fields are filled out incorrectly. Please check your entries and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">The password entry fields do not match. Please enter the same password in both fields and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">A required field is missing. Please fill out all required fields and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="helplink_text">What is this?</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="initial_intro">Please provide the following details.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="preloader_alt">Please wait</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="required_field">This information is required.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_edit">Change e-mail</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_resend">Send new code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_send">Send verification code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_verify">Verify code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">That code is expired. Please request a new code.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">You've made too many incorrect attempts. Please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">That code is incorrect. Please try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_server">We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_info_msg">Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_input">Verification code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">Verification is necessary. Please click Send button.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_success_msg">E-mail address verified. You can now continue.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">Claim not verified: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">A user with the specified ID already exists. Please choose a different one.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">Incorrect pattern for: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">{0} has invalid input.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">Missing required element: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">Error in validation by: {0}</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="phone-factor-authentication-page-user-interface-elements"></a>Elementos da interface do usuário da página autenticação com fator de telefone

Veja a seguir as IDs de uma definição de conteúdo com uma ID `api.phonefactor` e o [perfil técnico de fator de telefone](phone-factor-technical-profile.md).

| ID | Valor padrão |
| -- | ------------- |
| **button_verify** | Ligar Para Mim |
| **country_code_label** | Código do país |
| **cancel_message** | O usuário cancelou a autenticação multifator |
| **text_button_send_second_code** | envie um novo código |
| **code_pattern** | \\d{6} |
| **intro_mixed** | Temos o seguinte número no registro para você. Podemos enviar um código via SMS ou ligar para autenticá-lo. |
| **intro_mixed_p** | Temos os seguintes números no registro para você. Escolha um número para que possamos telefonr ou enviar um código via SMS para autenticá-lo. |
| **button_verify_code** | Verificar Código |
| **requiredField_code** | Insira o código de verificação recebido |
| **invalid_code** | Insira o código de 6 dígitos recebido |
| **button_cancel** | Cancelar |
| **local_number_input_placeholder_text** | Número de telefone |
| **button_retry** | Repetir |
| **alternative_text** | Não estou com meu telefone |
| **intro_phone_p** | Temos os seguintes números no registro para você. Escolha um número para que possamos ligar para autenticá-lo. |
| **intro_phone** | Temos o seguinte número no registro para você. Telefonaremos para autenticá-lo. |
| **enter_code_text_intro** | Insira seu código de verificação abaixo ou  |
| **intro_entry_phone** | Insira um número abaixo para que possamos ligar para autenticá-lo. |
| **intro_entry_sms** | Insira um número abaixo para que possamos enviar um código via SMS para autenticá-lo. |
| **button_send_code** | Enviar Código |
| **invalid_number** | Insira um número de telefone válido |
| **intro_sms** | Temos o seguinte número no registro para você. Enviaremos um código via SMS para autenticá-lo. |
| **intro_entry_mixed** | Insira um número abaixo para que possamos enviar um código via SMS ou ligar para autenticá-lo. |
| **number_pattern** | ^\\+(?:[0-9][\\x20-]?){6,14}[0-9]$ |
| **intro_sms_p** |Temos os seguintes números no registro para você. Escolha um número para que possamos ou enviar um código via SMS para autenticá-lo. |
| **requiredField_countryCode** | Selecione o código do seu país/região |
| **requiredField_number** | Insira seu número de telefone |
| **country_code_input_placeholder_text** |País ou região |
| **number_label** | Número do telefone |
| **error_tryagain** | O número de telefone que você forneceu está ocupado ou não disponível. Verifique o número e tente novamente. |
| **error_incorrect_code** | O código de verificação inserido não corresponde aos nossos registros. Tente novamente ou solicite um novo código. |
| **countryList** | Consulte [a lista de países](#phone-factor-authentication-page-example). |
| **error_448** | O número de telefone que você forneceu está inacessível. |
| **error_449** | O usuário excedeu o número de novas tentativas. |
| **verification_code_input_placeholder_text** | Código de verificação |

O exemplo a seguir mostra o uso de alguns dos elementos de interface do usuário na página de registro com MFA:

![Elementos de UX do registro de autenticação do fator de telefone](./media/localization-string-ids/localization-mfa1.png)

O exemplo a seguir mostra o uso de alguns dos elementos de interface do usuário na página de validação com MFA:

![Elementos de experiência de validação da autenticação do fator de telefone](./media/localization-string-ids/localization-mfa2.png)

## <a name="phone-factor-authentication-page-example"></a>Exemplo de página de autenticação do fator de telefone

```xml
<LocalizedResources Id="api.phonefactor.en">
  <LocalizedStrings>
    <LocalizedString ElementType="UxElement" StringId="button_verify">Call Me</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="country_code_label">Country Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="cancel_message">The user has canceled multi-factor authentication</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="text_button_send_second_code">Send a new code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="code_pattern">\d{6}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_mixed">We have the following number on record for you. We can send a code via SMS or phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_mixed_p">We have the following numbers on record for you. Choose a number that we can phone or send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_verify_code">Verify Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_code">Please enter the verification code you received</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_code">Please enter the 6-digit code you received</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="local_number_input_placeholder_text">Phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_retry">Retry</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="alternative_text">I don't have my phone</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_phone_p">We have the following numbers on record for you. Choose a number that we can phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_phone">We have the following number on record for you. We will phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="enter_code_text_intro">Enter your verification code below, or</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_phone">Enter a number below that we can phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_sms">Enter a number below that we can send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_send_code">Send Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_number">Please enter a valid phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_sms">We have the following number on record for you. We will send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_mixed">Enter a number below that we can send a code via SMS or phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="number_pattern">^\+(?:[0-9][\x20-]?){6,14}[0-9]$</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_sms_p">We have the following numbers on record for you. Choose a number that we can send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_countryCode">Please select your country code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_number">Please enter your phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="country_code_input_placeholder_text">Country or region</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="number_label">Phone Number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_tryagain">The phone number you provided is busy or unavailable. Please check the number and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_incorrect_code">The verification code you have entered does not match our records. Please try again, or request a new code.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="countryList">{"DEFAULT":"Country/Region","AF":"Afghanistan","AX":"Åland Islands","AL":"Albania","DZ":"Algeria","AS":"American Samoa","AD":"Andorra","AO":"Angola","AI":"Anguilla","AQ":"Antarctica","AG":"Antigua and Barbuda","AR":"Argentina","AM":"Armenia","AW":"Aruba","AU":"Australia","AT":"Austria","AZ":"Azerbaijan","BS":"Bahamas","BH":"Bahrain","BD":"Bangladesh","BB":"Barbados","BY":"Belarus","BE":"Belgium","BZ":"Belize","BJ":"Benin","BM":"Bermuda","BT":"Bhutan","BO":"Bolivia","BQ":"Bonaire","BA":"Bosnia and Herzegovina","BW":"Botswana","BV":"Bouvet Island","BR":"Brazil","IO":"British Indian Ocean Territory","VG":"British Virgin Islands","BN":"Brunei","BG":"Bulgaria","BF":"Burkina Faso","BI":"Burundi","CV":"Cabo Verde","KH":"Cambodia","CM":"Cameroon","CA":"Canada","KY":"Cayman Islands","CF":"Central African Republic","TD":"Chad","CL":"Chile","CN":"China","CX":"Christmas Island","CC":"Cocos (Keeling) Islands","CO":"Colombia","KM":"Comoros","CG":"Congo","CD":"Congo (DRC)","CK":"Cook Islands","CR":"Costa Rica","CI":"Côte d'Ivoire","HR":"Croatia","CU":"Cuba","CW":"Curaçao","CY":"Cyprus","CZ":"Czech Republic","DK":"Denmark","DJ":"Djibouti","DM":"Dominica","DO":"Dominican Republic","EC":"Ecuador","EG":"Egypt","SV":"El Salvador","GQ":"Equatorial Guinea","ER":"Eritrea","EE":"Estonia","ET":"Ethiopia","FK":"Falkland Islands","FO":"Faroe Islands","FJ":"Fiji","FI":"Finland","FR":"France","GF":"French Guiana","PF":"French Polynesia","TF":"French Southern Territories","GA":"Gabon","GM":"Gambia","GE":"Georgia","DE":"Germany","GH":"Ghana","GI":"Gibraltar","GR":"Greece","GL":"Greenland","GD":"Grenada","GP":"Guadeloupe","GU":"Guam","GT":"Guatemala","GG":"Guernsey","GN":"Guinea","GW":"Guinea-Bissau","GY":"Guyana","HT":"Haiti","HM":"Heard Island and McDonald Islands","HN":"Honduras","HK":"Hong Kong SAR","HU":"Hungary","IS":"Iceland","IN":"India","ID":"Indonesia","IR":"Iran","IQ":"Iraq","IE":"Ireland","IM":"Isle of Man","IL":"Israel","IT":"Italy","JM":"Jamaica","JP":"Japan","JE":"Jersey","JO":"Jordan","KZ":"Kazakhstan","KE":"Kenya","KI":"Kiribati","KR":"Korea","KW":"Kuwait","KG":"Kyrgyzstan","LA":"Laos","LV":"Latvia","LB":"Lebanon","LS":"Lesotho","LR":"Liberia","LY":"Libya","LI":"Liechtenstein","LT":"Lithuania","LU":"Luxembourg","MO":"Macao SAR","MK":"North Macedonia","MG":"Madagascar","MW":"Malawi","MY":"Malaysia","MV":"Maldives","ML":"Mali","MT":"Malta","MH":"Marshall Islands","MQ":"Martinique","MR":"Mauritania","MU":"Mauritius","YT":"Mayotte","MX":"Mexico","FM":"Micronesia","MD":"Moldova","MC":"Monaco","MN":"Mongolia","ME":"Montenegro","MS":"Montserrat","MA":"Morocco","MZ":"Mozambique","MM":"Myanmar","NA":"Namibia","NR":"Nauru","NP":"Nepal","NL":"Netherlands","NC":"New Caledonia","NZ":"New Zealand","NI":"Nicaragua","NE":"Niger","NG":"Nigeria","NU":"Niue","NF":"Norfolk Island","KP":"North Korea","MP":"Northern Mariana Islands","NO":"Norway","OM":"Oman","PK":"Pakistan","PW":"Palau","PS":"Palestinian Authority","PA":"Panama","PG":"Papua New Guinea","PY":"Paraguay","PE":"Peru","PH":"Philippines","PN":"Pitcairn Islands","PL":"Poland","PT":"Portugal","PR":"Puerto Rico","QA":"Qatar","RE":"Réunion","RO":"Romania","RU":"Russia","RW":"Rwanda","BL":"Saint Barthélemy","KN":"Saint Kitts and Nevis","LC":"Saint Lucia","MF":"Saint Martin","PM":"Saint Pierre and Miquelon","VC":"Saint Vincent and the Grenadines","WS":"Samoa","SM":"San Marino","ST":"São Tomé and Príncipe","SA":"Saudi Arabia","SN":"Senegal","RS":"Serbia","SC":"Seychelles","SL":"Sierra Leone","SG":"Singapore","SX":"Sint Maarten","SK":"Slovakia","SI":"Slovenia","SB":"Solomon Islands","SO":"Somalia","ZA":"South Africa","GS":"South Georgia and South Sandwich Islands","SS":"South Sudan","ES":"Spain","LK":"Sri Lanka","SH":"St Helena, Ascension, Tristan da Cunha","SD":"Sudan","SR":"Suriname","SJ":"Svalbard","SZ":"Swaziland","SE":"Sweden","CH":"Switzerland","SY":"Syria","TW":"Taiwan","TJ":"Tajikistan","TZ":"Tanzania","TH":"Thailand","TL":"Timor-Leste","TG":"Togo","TK":"Tokelau","TO":"Tonga","TT":"Trinidad and Tobago","TN":"Tunisia","TR":"Turkey","TM":"Turkmenistan","TC":"Turks and Caicos Islands","TV":"Tuvalu","UM":"U.S. Outlying Islands","VI":"U.S. Virgin Islands","UG":"Uganda","UA":"Ukraine","AE":"United Arab Emirates","GB":"United Kingdom","US":"United States","UY":"Uruguay","UZ":"Uzbekistan","VU":"Vanuatu","VA":"Vatican City","VE":"Venezuela","VN":"Vietnam","WF":"Wallis and Futuna","YE":"Yemen","ZM":"Zambia","ZW":"Zimbabwe"}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_448">The phone number you provided is unreachable.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_449">User has exceeded the number of retry attempts.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_code_input_placeholder_text">Verification code</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="strongAuthenticationPhoneNumber" StringId="DisplayName">Phone Number</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>

```

## <a name="verification-display-control-user-interface-elements"></a>Elementos da interface do usuário do controle de exibição de verificação

Veja a seguir as IDs de um [controle de exibição de verificação](display-control-verification.md) com a [versão de layout de página](page-layout.md) 2.1.0 ou superior.

| ID | Valor padrão |
| -- | ------------- |
|intro_msg| A verificação é necessária. Clique no botão Enviar.|
|success_send_code_msg | O código de verificação foi enviado para sua caixa de entrada. Copie-o para a caixa de entrada abaixo.|
|failure_send_code_msg | Estamos com problemas para verificar seu endereço de email. Insira um endereço de email válido e tente novamente.|
|success_verify_code_msg | Endereço de email verificado. Agora, você pode continuar.|
|failure_verify_code_msg | Estamos com problemas para verificar seu endereço de email. Tente novamente.|
|but_send_code | Enviar código de verificação|
|but_verify_code | Verificar o código|
|but_send_new_code | Enviar novo código|
|but_change_claims | Alterar email|

### <a name="verification-display-control-example"></a>Exemplo de controle de exibição de verificação

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
   <!-- Display control UI elements-->
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="intro_msg">Verification is necessary. Please click Send button.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_send_code_msg">Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_send_code_msg">We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_verify_code_msg">E-mail address verified. You can now continue.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_verify_code_msg">We are having trouble verifying your email address. Please try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_code">Send verification code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_verify_code">Verify code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_new_code">Send new code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_change_claims">Change e-mail</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="verification-display-control-user-interface-elements-deprecated"></a>Elementos da interface do usuário do controle de exibição de verificação (preterido)

Veja a seguir as IDs de um [controle de exibição de verificação](display-control-verification.md) com a [versão de layout de página](page-layout.md) 2.0.0.

| ID | Valor padrão |
| -- | ------------- |
|verification_control_but_change_claims |Alterar |
|verification_control_fail_send_code |Falha ao enviar o código. Tente novamente mais tarde. |
|verification_control_fail_verify_code |Falha ao verificar o código. Tente novamente mais tarde. |
|verification_control_but_send_code |Enviar Código |
|verification_control_but_send_new_code |Enviar novo código |
|verification_control_but_verify_code |Verificar Código |
|verification_control_code_sent| O código de verificação foi enviado. Copie-o para a caixa de entrada abaixo. |

### <a name="verification-display-control-example-deprecated"></a>Exemplo de controle de exibição de verificação (preterido)

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_change_claims">Change</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_send_code">Failed to send the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_verify_code">Failed to verify the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_code">Send Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_new_code">Send New Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_verify_code">Verify Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_code_sent">Verification code has been sent. Please copy it to the input box below.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="restful-service-error-messages"></a>Mensagens de erro do serviço RESTful

A seguir estão as IDs para mensagens de erro de [perfil técnico do serviço RESTful](restful-technical-profile.md) :

| ID | Valor padrão |
| -- | ------------- |
|DefaultUserMessageIfRequestFailed | Falha ao estabelecer conexão com o ponto de extremidade do serviço RESTful. URL do serviço RESTful: {0} |
|UserMessageIfCircuitOpen | {0} URL do serviço RESTful: {1} |
|UserMessageIfDnsResolutionFailed | Falha ao resolver o nome de host do ponto de extremidade do serviço RESTful. URL do serviço RESTful: {0} |
|UserMessageIfRequestTimeout | Falha ao estabelecer conexão com o ponto de extremidade do serviço RESTful dentro do tempo limite em {0} segundos. URL do serviço RESTful: {1} |


### <a name="restful-service-example"></a>Exemplo de serviço RESTful

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="DefaultUserMessageIfRequestFailed">Failed to establish connection to restful service end point.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfCircuitOpen">Unable to connect to the restful service end point.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfDnsResolutionFailed">Failed to resolve the hostname of the restful service endpoint.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfRequestTimeout">Failed to establish connection to restful service end point within timeout limit.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="azure-ad-mfa-error-messages"></a>Mensagens de erro do Azure AD MFA

A seguir estão as IDs para uma mensagem de erro do [perfil técnico do Azure ad MFA](multi-factor-auth-technical-profile.md) :

| ID | Valor padrão |
| -- | ------------- |
|UserMessageIfCouldntSendSms | Não é possível enviar SMS para o telefone. Tente outro número de telefone. |
|UserMessageIfInvalidFormat | Seu número de telefone não está em um formato válido. Corrija-o e tente novamente.|
|UserMessageIfMaxAllowedCodeRetryReached | Código errado inserido muitas vezes. Tente novamente mais tarde.|
|UserMessageIfServerError | Não é possível usar o serviço do MFA. Tente novamente mais tarde.|
|UserMessageIfThrottled | Sua solicitação foi limitada. Tente novamente mais tarde.|
|UserMessageIfWrongCodeEntered|Código errado inserido. Tente novamente.|

### <a name="azure-ad-mfa-example"></a>Exemplo de MFA do Azure AD

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfCouldntSendSms">Cannot Send SMS to the phone, please try another phone number.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidFormat">Your phone number is not in a valid format, please correct it and try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxAllowedCodeRetryReached">Wrong code entered too many times, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfServerError">Cannot use MFA service, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfThrottled">Your request has been throttled, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfWrongCodeEntered">Wrong code entered, please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="azure-ad-sspr"></a>SSPR do Azure AD

A seguir estão as IDs para mensagens de erro do [perfil técnico do Azure ad SSPR](aad-sspr-technical-profile.md) :

| ID | Valor padrão |
| -- | ------------- |
|UserMessageIfChallengeExpired | O código expirou.|
|UserMessageIfInternalError | O serviço de email encontrou um erro interno, tente novamente mais tarde.|
|UserMessageIfThrottled | Você enviou muitas solicitações, tente novamente mais tarde.|
|UserMessageIfVerificationFailedNoRetry | Você excedeu o número máximo de tentativas de verificação.|
|UserMessageIfVerificationFailedRetryAllowed | A verificação falhou, tente novamente.|


### <a name="azure-ad-sspr-example"></a>Exemplo de SSPR do Azure AD

```XML
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInternalError">We are having trouble verifying your email address. Please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfThrottled">There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfChallengeExpired">That code is expired. Please request a new code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedNoRetry">You've made too many incorrect attempts. Please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="one-time-password-error-messages"></a>Mensagens de erro de senha avulsa

Veja a seguir as IDs para mensagens de erro de um [perfil técnico de senha avulsa](one-time-password-technical-profile.md)

| ID | Valor padrão |
| -- | ------------- |
|UserMessageIfMaxRetryAttempted |A verificação de senha avulsa fornecida excedeu o número máximo de tentativas |
|UserMessageIfSessionDoesNotExist |A sessão de verificação de senha avulsa expirou |
|UserMessageIfSessionConflict |A sessão de verificação de senha avulsa tem conflito |
|UserMessageIfInvalidCode |A senha avulsa fornecida para verificação está incorreta |
|UserMessageIfVerificationFailedRetryAllowed |Esse código está incorreto. Tente novamente. | 

### <a name="one-time-password-example"></a>Exemplo de senha de uso único

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidCode">You have entered the wrong code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="claims-transformations-error-messages"></a>Mensagens de erro de transformações de declarações

Veja a seguir as IDs para mensagens de erro de transformações de declarações:

| ID | Transformação de declarações | Valor padrão |
| -- | ------------- |------------- |
|UserMessageIfClaimsTransformationBooleanValueIsNotEqual |[AssertBooleanClaimIsEqualToValue](boolean-transformations.md#assertbooleanclaimisequaltovalue) | A comparação de valor de declaração booliana falhou para o tipo de declaração "inputClaim".| 
|DateTimeGreaterThan |[AssertDateTimeIsGreaterThan](date-transformations.md#assertdatetimeisgreaterthan) | A comparação de arquivo falhou: O operando esquerdo fornecido é maior que o operando direito.|
|UserMessageIfClaimsTransformationStringsAreNotEqual |[AssertStringClaimsAreEqual](string-transformations.md#assertstringclaimsareequal) | A comparação de valor de declaração falhou usando StringComparison "OrdinalIgnoreCase".|

### <a name="claims-transformations-example"></a>Exemplo de transformações de declarações

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Your email address hasn't been verified.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DateTimeGreaterThan">Expiration date must be greater that the current date.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationStringsAreNotEqual">The email entry fields do not match. Please enter the same email address in both fields and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos para obter exemplos de localização:

- [Personalização de idioma com política personalizada no Azure Active Directory B2C](language-customization.md)
- [Personalização de idioma com fluxos de usuário no Azure Active Directory B2C](language-customization.md)
