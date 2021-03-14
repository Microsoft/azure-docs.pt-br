---
title: Verificação de email personalizada com Mailjet
titleSuffix: Azure AD B2C
description: Saiba como integrar com o Mailjet para personalizar o email de verificação enviado aos seus clientes quando eles se inscreverem para usar seus aplicativos habilitados para Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/10/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6643d44e1b90bb18f5893ba07a4291601b588664
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "102631210"
---
# <a name="custom-email-verification-with-mailjet"></a>Verificação de email personalizada com Mailjet

Use o email personalizado no Azure Active Directory B2C (Azure AD B2C) para enviar email personalizado aos usuários que se inscrevem para usar seus aplicativos. Usando o [DisplayControls](display-controls.md) (atualmente em visualização) e o provedor de email de terceiros Mailjet, você pode usar seu próprio modelo de email e *de:* endereço e assunto, bem como a localização de suporte e configurações de OTP (senha de uso único) personalizadas.

A verificação de email personalizada requer o uso de um provedor de email de terceiros, como [Mailjet](https://Mailjet.com), [SendGrid](./custom-email-sendgrid.md)ou [SparkPost](https://sparkpost.com), uma API REST personalizada ou qualquer provedor de email baseado em http (incluindo o seu próprio). Este artigo descreve como configurar uma solução que usa Mailjet.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-mailjet-account"></a>Criar uma conta do Mailjet

Se você ainda não tiver uma, comece Configurando uma conta do Mailjet (os clientes do Azure podem desbloquear 6.000 emails com um limite de 200 emails/dia). 

1. Siga as instruções de instalação em [criar uma conta do Mailjet](https://www.mailjet.com/guides/azure-mailjet-developer-resource-user-guide/enabling-mailjet/).
1. Para poder enviar emails, [registre e valide](https://www.mailjet.com/guides/azure-mailjet-developer-resource-user-guide/enabling-mailjet/#how-to-configure-mailjet-for-use) seu endereço de email ou domínio do remetente.
2. Navegue até a [página de gerenciamento de chaves de API](https://app.mailjet.com/account/api_keys). Registre a chave de **API** e a **chave secreta** para uso em uma etapa posterior. Ambas as chaves são geradas automaticamente quando sua conta é criada.  

## <a name="create-azure-ad-b2c-policy-key"></a>Criar chave de política de Azure AD B2C

Em seguida, armazene a chave de API Mailjet em uma chave de política de Azure AD B2C para que suas políticas façam referência.

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Verifique se você está usando o diretório que contém seu locatário do Azure AD B2C. Selecione o filtro **Diretório + assinatura** no menu superior e escolha o diretório do Azure AD B2C.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Na página **visão geral** , selecione **Identity Experience Framework**.
1. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
1. Para **Opções**, escolha **manual**.
1. Insira um **Nome** para a chave de política. Por exemplo, `MailjetApiKey`. O prefixo `B2C_1A_` será adicionado automaticamente ao nome da chave.
1. Em **segredo**, insira sua **chave de API** do Mailjet que você registrou anteriormente.
1. Para **Uso de chave**, selecione **Assinatura**.
1. Selecione **Criar**.
1. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
1. Para **Opções**, escolha **manual**.
1. Insira um **Nome** para a chave de política. Por exemplo, `MailjetSecretKey`. O prefixo `B2C_1A_` será adicionado automaticamente ao nome da chave.
1. Em **segredo**, insira sua **chave secreta** do Mailjet que você registrou anteriormente.
1. Para **Uso de chave**, selecione **Assinatura**.
1. Selecione **Criar**.

## <a name="create-a-mailjet-template"></a>Criar um modelo Mailjet

Com uma conta do Mailjet criada e a chave de API do Mailjet armazenada em uma chave de política Azure AD B2C, crie um [modelo transacional dinâmico](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/)Mailjet.

1. No site do Mailjet, abra a página [modelos transacionais](https://app.mailjet.com/templates/transactional) e selecione **criar um novo modelo**.
1. Selecione **codificando-o em HTML** e, em seguida, selecione **código do zero**.
1. Insira um nome de modelo exclusivo como `Verification email` e, em seguida, selecione **criar**.
1. No editor de HTML, Cole o modelo HTML a seguir ou use o seu próprio. Os `{{var:otp:""}}` `{{var:email:""}}` parâmetros e serão substituídos dinamicamente pelo valor de senha de uso único e pelo endereço de email do usuário.

    ```HTML
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" dir="ltr" lang="en"><head id="Head1">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"><title>Contoso demo account email verification code</title><meta name="ROBOTS" content="NOINDEX, NOFOLLOW">
       <!-- Template B O365 -->
       <style>
           table td {border-collapse:collapse;margin:0;padding:0;}
       </style>
    </head>
    <body dir="ltr" lang="en">
       <table width="100%" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
            <tr>
               <td valign="top" width="50%"></td>
               <td valign="top">
                  <!-- Email Header -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en" style="border-left:1px solid #e3e3e3;border-right: 1px solid #e3e3e3;">
                   <tr style="background-color: #0072C6;">
                       <td width="1" style="background:#0072C6; border-top:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-top:1px solid #e3e3e3;border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="310" valign="middle" style="border-top:1px solid #e3e3e3; border-bottom:1px solid #e3e3e3;padding:12px 0;">
                           <h1 style="line-height:20pt;font-family:Segoe UI Light; font-size:18pt; color:#ffffff; font-weight:normal;">
                            <span id="HeaderPlaceholder_UserVerificationEmailHeader"><font color="#FFFFFF">Verify your email address</font></span>
                           </h1>
                       </td>
                       <td width="24" style="border-top: 1px solid #e3e3e3;border-bottom: 1px solid #e3e3e3;">&nbsp;</td>
                   </tr>
                  </table>
                  <!-- Email Content -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
                   <tr>
                       <td width="1" style="background:#e3e3e3;"></td>
                       <td width="24">&nbsp;</td>
                       <td id="PageBody" width="640" valign="top" colspan="2" style="border-bottom:1px solid #e3e3e3;padding:10px 0 20px;border-bottom-style:hidden;">
                           <table cellpadding="0" cellspacing="0" border="0">
                               <tr>
                                   <td width="630" style="font-size:10pt; line-height:13pt; color:#000;">
                                       <table cellpadding="0" cellspacing="0" border="0" width="100%" style="" dir="ltr" lang="en">
                                           <tr>
                                               <td>

       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence1">Thanks for verifying your {{var:email:""}} account!</span>
       </div>
       <br>
       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333; font-weight: bold">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence2">Your code is: {{var:otp:""}}</span>
       </div>
       <br>
       <br>

                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
                                                   Sincerely,
                                                   </div>
                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; font-style:italic; color:#333;">
                                                       Contoso
                                                   </div>
                                               </td>
                                           </tr>
                                       </table>
                                   </td>
                               </tr>
                           </table>

                       </td>

                       <td width="1">&nbsp;</td>
                       <td width="1"></td>
                       <td width="1">&nbsp;</td>
                       <td width="1" valign="top"></td>
                       <td width="29">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3;"></td>
                   </tr>
                   <tr>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td id="PageFooterContainer" width="585" valign="top" colspan="6" style="border-bottom:1px solid #e3e3e3;padding:0px;">


                       </td>

                       <td width="29" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                   </tr>
                  </table>

               </td>
               <td valign="top" width="50%"></td>
           </tr>
       </table>
    <img src="https://mucp.api.account.microsoft.com/m/v2/v?d=AIAACWEPFYXYIUTJIJVV4ST7XLBHVI5MLLYBKJAVXHBDTBHUM5VBSVVPTTVRWDFIXJ5JQTHYOH5TUYIPO4ZAFRFK52UAMIS3UNIPPI7ZJNDZPRXD5VEJBN4H6RO3SPTBS6AJEEAJOUYL4APQX5RJUJOWGPKUABY&amp;i=AIAACL23GD2PFRFEY5YVM2XQLM5YYWMHFDZOCDXUI2B4LM7ETZQO473CVF22PT6WPGR5IIE6TCS6VGEKO5OZIONJWCDMRKWQQVNP5VBYAINF3S7STKYOVDJ4JF2XEW4QQVNHMAPQNHFV3KMR3V3BA4I36B6BO7L4VQUHQOI64EOWPLMG5RB3SIMEDEHPILXTF73ZYD3JT6MYOLAZJG7PJJCAXCZCQOEFVH5VCW2KBQOKRYISWQLRWAT7IINZ3EFGQI2CY2EMK3FQOXM7UI3R7CZ6D73IKDI" width="1" height="1"></body>
    </html>
    ```

1. Expanda **Editar assunto** na parte superior esquerda
    1. Para **assunto**, insira um valor padrão para o assunto. Mailjet usa esse valor quando a API não contém um parâmetro Subject.
    1. Para o **nome**, digite o nome da sua empresa.
    1. Para o **endereço**, selecione seu endereço de email
    1. Clique em **Salvar**.
1. No canto superior direito, selecione **salvar & publicar** e, em seguida **, sim, publicar alterações**
1. Registre a **ID do modelo** do modelo criado para uso em uma etapa posterior. Você especifica essa ID ao [Adicionar a transformação declarações](#add-the-claims-transformation).


## <a name="add-azure-ad-b2c-claim-types"></a>Adicionar tipos de declaração de Azure AD B2C

Em sua política, adicione os seguintes tipos de declaração ao `<ClaimsSchema>` elemento no `<BuildingBlocks>` .

Esses tipos de declarações são necessários para gerar e verificar o endereço de email usando um código de OTP (senha de uso único).

```XML
<!--
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="Otp">
      <DisplayName>Secondary One-time password</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="emailRequestBody">
      <DisplayName>Mailjet request body</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="VerificationCode">
      <DisplayName>Secondary Verification Code</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Enter your email verification code</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  <!-- 
  </ClaimsSchema>
</BuildingBlocks> -->
```

## <a name="add-the-claims-transformation"></a>Adicionar a transformação de declarações

Em seguida, você precisa de uma transformação declarações para gerar uma declaração de cadeia de caracteres JSON que será o corpo da solicitação enviada para Mailjet.

A estrutura do objeto JSON é definida pelas IDs na notação de ponto de InputParameters e TransformationClaimTypes do InputClaims. Os números na notação de ponto implicam matrizes. Os valores são provenientes dos valores de InputClaims e das propriedades de "valor" de InputParameters. Para obter mais informações sobre transformações de declarações JSON, consulte [transformações de declarações JSON](json-transformations.md).

Adicione a transformação de declarações a seguir ao `<ClaimsTransformations>` elemento no `<BuildingBlocks>` . Faça as seguintes atualizações para o XML de transformação de declarações:

* Atualize o `Messages.0.TemplateID` valor InputParameter com a ID do modelo transacional Mailjet criado anteriormente em [criar um modelo de Mailjet](#create-a-mailjet-template).
* Atualize o `Messages.0.From.Email` valor do endereço. Use um endereço de email válido para ajudar a impedir que o email de verificação seja marcado como spam.
* Atualize o valor do `Messages.0.Subject` parâmetro de entrada da linha de assunto com uma linha de assunto apropriada para sua organização.

```XML
<!-- 
<BuildingBlocks>
  <ClaimsTransformations> -->
    <ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.To.0.Email" />
        <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="Messages.0.Variables.otp" />
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.Variables.email" />
      </InputClaims>
      <InputParameters>
        <!-- Update the template_id value with the ID of your Mailjet template. -->
        <InputParameter Id="Messages.0.TemplateID" DataType="int" Value="1234567"/>
        <InputParameter Id="Messages.0.TemplateLanguage" DataType="boolean" Value="true"/>

        <!-- Update with an email appropriate for your organization. -->
        <InputParameter Id="Messages.0.From.Email" DataType="string" Value="my_email@mydomain.com"/>

        <!-- Update with a subject line appropriate for your organization. -->
        <InputParameter Id="Messages.0.Subject" DataType="string" Value="Contoso account email verification code"/>
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim"/>
      </OutputClaims>
    </ClaimsTransformation>
  <!--
  </ClaimsTransformations>
</BuildingBlocks> -->
```

## <a name="add-datauri-content-definition"></a>Adicionar definição de conteúdo DataUri

Abaixo das transformações de declarações no `<BuildingBlocks>` , adicione o seguinte [ContentDefinition](contentdefinitions.md) para fazer referência ao URI de dados da versão 2.1.0:

```XML
<!--
<BuildingBlocks> -->
  <ContentDefinitions>
   <ContentDefinition Id="api.localaccountsignup">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.0</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.localaccountpasswordreset">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.0</DataUri>
    </ContentDefinition>
  </ContentDefinitions>
<!--
</BuildingBlocks> -->
```

## <a name="create-a-displaycontrol"></a>Criar um DisplayControl

Um controle de exibição de verificação é usado para verificar o endereço de email com um código de verificação que é enviado ao usuário.

Este exemplo de controle de exibição está configurado para:

1. Colete o `email` tipo de declaração de endereço do usuário.
1. Aguarde até que o usuário forneça o `verificationCode` tipo de declaração com o código enviado ao usuário.
1. Retorne o `email` para o perfil técnico autodeclarado que tem uma referência a esse controle de exibição.
1. Usando a `SendCode` ação, gere um código de OTP e envie um email com o código de OTP para o usuário.

   ![Enviar ação de email do código de verificação](media/custom-email-mailjet/display-control-verification-email-action-01.png)

Em definições de conteúdo, ainda em `<BuildingBlocks>` , adicione o seguinte [DisplayControl](display-controls.md) do tipo [VerificationControl](display-control-verification.md) à sua política.

```XML
<!--
<BuildingBlocks> -->
  <DisplayControls>
    <DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
      <DisplayClaims>
        <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
      </DisplayClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" />
      </OutputClaims>
      <Actions>
        <Action Id="SendCode">
          <ValidationClaimsExchange>
            <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
            <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendOtp" />
          </ValidationClaimsExchange>
        </Action>
        <Action Id="VerifyCode">
          <ValidationClaimsExchange>
            <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
          </ValidationClaimsExchange>
        </Action>
      </Actions>
    </DisplayControl>
  </DisplayControls>
<!--
</BuildingBlocks> -->
```

## <a name="add-otp-technical-profiles"></a>Adicionar perfis técnicos de OTP

O `GenerateOtp` perfil técnico gera um código para o endereço de email. O `VerifyOtp` perfil técnico verifica o código associado ao endereço de email. Você pode alterar a configuração do formato e a expiração da senha de uso único. Para obter mais informações sobre perfis técnicos de OTP, consulte [definir um perfil técnico de senha de uso único](one-time-password-technical-profile.md).

Adicione os seguintes perfis técnicos ao `<ClaimsProviders>` elemento.

```XML
<!--
<ClaimsProviders> -->
  <ClaimsProvider>
    <DisplayName>One time password technical profiles</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="GenerateOtp">
        <DisplayName>Generate one time password</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
          <Item Key="Operation">GenerateCode</Item>
          <Item Key="CodeExpirationInSeconds">1200</Item>
          <Item Key="CodeLength">6</Item>
          <Item Key="CharacterSet">0-9</Item>
          <Item Key="ReuseSameCode">true</Item>
          <Item Key="NumRetryAttempts">5</Item>
        </Metadata>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
        </InputClaims>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="otp" PartnerClaimType="otpGenerated" />
        </OutputClaims>
      </TechnicalProfile>

      <TechnicalProfile Id="VerifyOtp">
        <DisplayName>Verify one time password</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
          <Item Key="Operation">VerifyCode</Item>
        </Metadata>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
          <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="otpToVerify" />
        </InputClaims>
      </TechnicalProfile>
     </TechnicalProfiles>
  </ClaimsProvider>
<!--
</ClaimsProviders> -->
```

## <a name="add-a-rest-api-technical-profile"></a>Adicionar um perfil técnico da API REST

Esse perfil técnico da API REST gera o conteúdo do email (usando o formato Mailjet). Para obter mais informações sobre perfis técnicos RESTful, consulte [definir um perfil técnico RESTful](restful-technical-profile.md).

Assim como acontece com os perfis técnicos de OTP, adicione os seguintes perfis técnicos ao `<ClaimsProviders>` elemento.

```XML
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="sendOtp">
      <DisplayName>Use email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.mailjet.com/v3.1/send</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">emailRequestBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_MailjetApiKey" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_MailjetSecretKey" />
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="GenerateEmailRequestBody" />
      </InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="emailRequestBody" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="make-a-reference-to-the-displaycontrol"></a>Fazer uma referência para o DisplayControl

Na etapa final, adicione uma referência para o DisplayControl que você criou. Substitua seus `LocalAccountSignUpWithLogonEmail` `LocalAccountDiscoveryUsingEmailAddress` perfis técnicos existentes e autodeclarados pelo seguinte. Se você usou uma versão anterior da política de Azure AD B2C. Esses perfis técnicos usam `DisplayClaims` com uma referência para o DisplayControl..

Para obter mais informações, consulte [perfil técnico autodeclarado](restful-technical-profile.md) e [DisplayControl](display-controls.md).

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <Metadata>
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
        <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      </DisplayClaims>
    </TechnicalProfile>
    <TechnicalProfile Id="LocalAccountDiscoveryUsingEmailAddress">
      <Metadata>
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
      </DisplayClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="optional-localize-your-email"></a>Adicional Localize seu email

Para localizar o email, você deve enviar cadeias de caracteres localizadas para Mailjet ou seu provedor de email. Por exemplo, você pode localizar o assunto do email, o corpo, a mensagem de código ou a assinatura do email. Para fazer isso, você pode usar a transformação declarações [GetLocalizedStringsTransformation](string-transformations.md) para copiar cadeias de caracteres localizadas em tipos de declaração. A `GenerateEmailRequestBody` transformação declarações, que gera a carga JSON, usa declarações de entrada que contêm as cadeias de caracteres localizadas.

1. Em sua política, defina as seguintes declarações de cadeia de caracteres: assunto, mensagem, codeIntro e assinatura.
1. Defina uma transformação de declarações [GetLocalizedStringsTransformation](string-transformations.md) para substituir os valores de cadeia de caracteres localizados nas declarações da etapa 1.
1. Altere a `GenerateEmailRequestBody` transformação de declarações para usar declarações de entrada com o trecho de código XML a seguir.
1. Atualize seu modelo Mailjet para usar parâmetros dinâmicos no lugar de todas as cadeias de caracteres que serão localizadas por Azure AD B2C.

    ```XML
    <ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
        <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
        <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
        <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.To.0.Email" />
        <InputClaim ClaimTypeReferenceId="subject" TransformationClaimType="Messages.0.Subject" />
        <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="Messages.0.Variables.otp" />
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.Variables.email" />
        <InputClaim ClaimTypeReferenceId="message" TransformationClaimType="Messages.0.Variables.otpmessage" />
        <InputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="Messages.0.Variables.otpcodeIntro" />
        <InputClaim ClaimTypeReferenceId="signature" TransformationClaimType="Messages.0.Variables.otpsignature" />
      </InputClaims>
      <InputParameters>
        <!-- Update the template_id value with the ID of your Mailjet template. -->
        <InputParameter Id="Messages.0.TemplateID" DataType="int" Value="1234567"/>
        <InputParameter Id="Messages.0.TemplateLanguage" DataType="boolean" Value="true"/>

        <!-- Update with an email appropriate for your organization. -->
        <InputParameter Id="Messages.0.From.Email" DataType="string" Value="my_email@mydomain.com"/>
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim"/>
      </OutputClaims>
    </ClaimsTransformation>
    ```

1. Adicione o seguinte elemento de [localização](localization.md) .

    ```xml
    <!--
    <BuildingBlocks> -->
      <Localization Enabled="true">
        <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
          <SupportedLanguage>en</SupportedLanguage>
          <SupportedLanguage>es</SupportedLanguage>
        </SupportedLanguages>
        <LocalizedResources Id="api.custom-email.en">
          <LocalizedStrings>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for validating the account</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
          </LocalizedStrings>
          </LocalizedStrings>
        </LocalizedResources>
        <LocalizedResources Id="api.custom-email.es">
          <LocalizedStrings>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sinceramente</LocalizedString>
          </LocalizedStrings>
        </LocalizedResources>
      </Localization>
    <!--
    </BuildingBlocks> -->
    ```

1. Adicione referências aos elementos LocalizedResources atualizando o elemento [ContentDefinitions](contentdefinitions.md) .

    ```xml
    <!--
    <BuildingBlocks> -->
      <ContentDefinitions>
        <ContentDefinition Id="api.localaccountsignup">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.0</DataUri>
          <LocalizedResourcesReferences MergeBehavior="Prepend">
            <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
            <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
          </LocalizedResourcesReferences>
        </ContentDefinition>
        <ContentDefinition Id="api.localaccountpasswordreset">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.0</DataUri>
          <LocalizedResourcesReferences MergeBehavior="Prepend">
            <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
            <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
          </LocalizedResourcesReferences>
        </ContentDefinition>
      </ContentDefinitions>
    <!--
    </BuildingBlocks> -->
    ```

1. Por fim, adicione a seguinte transformação de declarações de entrada para os `LocalAccountSignUpWithLogonEmail` `LocalAccountDiscoveryUsingEmailAddress` perfis técnicos e.

    ```xml
    <InputClaimsTransformations>
      <InputClaimsTransformation ReferenceId="GetLocalizedStringsForEmail" />
    </InputClaimsTransformations>
    ```
    
## <a name="optional-localize-the-ui"></a>Adicional Localize a interface do usuário

O elemento Localization permite que você dê suporte a várias localidades ou idiomas na política para os percursos do usuário. O suporte à localização em políticas permite que você forneça cadeias de caracteres específicas do idioma para ambos os [elementos da interface do usuário do controle de exibição de verificação](localization-string-ids.md#verification-display-control-user-interface-elements)e mensagens de erro de senha de [uma vez](localization-string-ids.md#one-time-password-error-messages). Adicione o seguinte localizador a seu LocalizedResources. 

```XML
<LocalizedResources Id="api.custom-email.en">
  <LocalizedStrings>
    ...
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
    <!-- Claims-->
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="DisplayName">Verification Code</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="UserHelpText">Verification code received in the email.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="AdminHelpText">Verification code received in the email.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Eamil</LocalizedString>
    <!-- Email validation error messages-->
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidCode">You have entered the wrong code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">The verification has failed, please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

Depois de adicionar as cadeias de caracteres localizadas, remova os metadados de mensagens de erro de validação de OTP dos perfis técnicos LocalAccountSignUpWithLogonEmail e LocalAccountDiscoveryUsingEmailAddress.

## <a name="next-steps"></a>Próximas etapas

Você pode encontrar um exemplo de uma política de verificação de email personalizada no GitHub:

- [Verificação de email personalizada-DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)
- Para obter informações sobre como usar uma API REST personalizada ou qualquer provedor de email SMTP baseado em HTTP, consulte [definir um perfil técnico RESTful em uma Azure ad B2C política personalizada](restful-technical-profile.md).
