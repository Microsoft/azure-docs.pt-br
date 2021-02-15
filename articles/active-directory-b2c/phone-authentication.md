---
title: Inscrição e entrada do telefone com políticas personalizadas
titleSuffix: Azure AD B2C
description: Envie uma OTP (senha de uso único) em mensagens de texto para os telefones dos usuários do aplicativo com políticas personalizadas no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2600ea3488c643bcf215b058425de42cd439dcff
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98660260"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c"></a>Configurar a inscrição e a entrada do telefone com políticas personalizadas no Azure AD B2C

A inscrição e a entrada do telefone no Azure Active Directory B2C (Azure AD B2C) permite que os usuários se inscrevam e entrem em seus aplicativos usando uma senha de uso único (OTP) enviada em uma mensagem de texto para seu telefone. As senhas de uso único podem ajudar a minimizar o risco de seus usuários esquecerem ou tiverem suas senhas comprometidas.

Siga as etapas neste artigo para usar as políticas personalizadas para permitir que seus clientes se inscrevam e entrem em seus aplicativos usando uma senha de uso único enviada ao seu telefone.

## <a name="pricing"></a>Preços

As senhas de uso único são enviadas aos usuários usando mensagens de texto SMS, e você pode ser cobrado por cada mensagem enviada. Para obter informações sobre preços, consulte a seção **encargos separados** de [preços Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="user-experience-for-phone-sign-up-and-sign-in"></a>Experiência do usuário para inscrição e entrada no telefone

Com a inscrição e a entrada do telefone, o usuário pode se inscrever no aplicativo usando um número de telefone como seu identificador principal. A experiência do usuário final durante a inscrição e a entrada é descrita abaixo.

> [!NOTE]
> É altamente recomendável que você inclua informações de consentimento em sua experiência de inscrição e de entrada semelhante ao texto de exemplo abaixo. Este texto de exemplo é apenas para fins informativos. Consulte o manual de monitoramento de código curto no [site do CTIA](https://www.ctia.org/programs) e consulte seus próprios especialistas legais ou de conformidade para obter orientação sobre sua configuração de texto final e recursos para atender às suas necessidades de conformidade:
>
> *Ao fornecer seu número de telefone, você concorda em receber uma senha de uso único enviada por mensagem de texto para ajudá-lo a entrar para *&lt; Inserir: &gt; o nome do aplicativo*. Taxas de mensagem e de dados padrão podem ser aplicadas.*
>
> *&lt;Inserir: um link para sua política de privacidade&gt;*<br/>*&lt;Inserir: um link para seus termos de serviço&gt;*

Para adicionar suas próprias informações de consentimento, personalize o exemplo a seguir. Inclua-o no `LocalizedResources` para o ContentDefinition usado pela página autodeclarada com o controle de exibição (o arquivo de *Phone_Email_Base.xml* no [pacote de início de inscrição e entrada do telefone][starter-pack-phone]):

```xml
<LocalizedResources Id="phoneSignUp.en">        
    <LocalizedStrings>
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_msg_intro">By providing your phone number, you consent to receiving a one-time passcode sent by text message to help you sign into {insert your application name}. Standard message and data rates may apply.</LocalizedString>          
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_1_text">Privacy Statement</LocalizedString>                
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_1_url">{insert your privacy statement URL}</LocalizedString>          
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_2_text">Terms and Conditions</LocalizedString>             
    <LocalizedString ElementType="DisplayControl" ElementId="phoneControl" StringId="disclaimer_link_2_url">{insert your terms and conditions URL}</LocalizedString>          
    <LocalizedString ElementType="UxElement" StringId="initial_intro">Please verify your country code and phone number</LocalizedString>        
    </LocalizedStrings>      
</LocalizedResources>
   ```

### <a name="phone-sign-up-experience"></a>Experiência de inscrição no telefone

Se o usuário ainda não tiver uma conta para seu aplicativo, ele poderá criar uma escolhendo o link **inscrever-se agora** . Uma página de inscrição é exibida, onde o usuário seleciona seu **país**, insere seu número de telefone e seleciona **enviar código**.

![O usuário inicia a inscrição no telefone](media/phone-authentication/phone-signup-start.png)

Um código de verificação único é enviado para o número de telefone do usuário. O usuário insere o **código de verificação** na página de inscrição e seleciona **verificar código**. (Se o usuário não conseguiu recuperar o código, ele pode selecionar **Enviar novo código**.)

![O usuário verifica o código durante a inscrição no telefone](media/phone-authentication/phone-signup-verify-code.png)

O usuário insere qualquer outra informação solicitada na página de inscrição. Por exemplo, **nome de exibição**, **nome fornecido** e **sobrenome** (país e número de telefone permanecem preenchidos). Se o usuário quiser usar um número de telefone diferente, ele poderá escolher **alterar número** para reiniciar a inscrição. Quando terminar, o usuário seleciona **continuar**.

![O usuário fornece informações adicionais](media/phone-authentication/phone-signup-additional-info.png)

Em seguida, o usuário será solicitado a fornecer um email de recuperação. O usuário insere seu endereço de email e seleciona **enviar código de verificação**. Um código é enviado para a caixa de entrada de email do usuário, que pode ser recuperada e inserida no **código de verificação** . Em seguida, o usuário seleciona **verificar código**. 

Depois que o código é verificado, o usuário seleciona **criar** para criar sua conta. Ou, se o usuário quiser usar um endereço de email diferente, ele poderá escolher **alterar email**.

![O usuário cria uma conta](media/phone-authentication/email-verification.png)

### <a name="phone-sign-in-experience"></a>Experiência de entrada no telefone

Se o usuário tiver uma conta existente com número de telefone como seu identificador, o usuário inserirá seu número de telefone e selecionará **continuar**. Eles confirmam o país e o número de telefone selecionando **continuar** e um código de verificação ocasional é enviado para o telefone. O usuário insere o código de verificação e seleciona **continuar** para entrar.

![Experiência do usuário de entrada no telefone](media/phone-authentication/phone-signin-screens.png)

## <a name="deleting-a-user-account"></a>Excluindo uma conta de usuário

Em alguns casos, você pode precisar excluir um usuário e os dados associados de seu diretório Azure AD B2C. Para obter detalhes sobre como excluir uma conta de usuário por meio do portal do Azure, consulte [estas instruções](/microsoft-365/compliance/gdpr-dsr-azure#step-5-delete). 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]



## <a name="prerequisites"></a>Pré-requisitos

Você precisa dos seguintes recursos em vigor antes de configurar a OTP.

* [Locatário do Azure AD B2C](tutorial-create-tenant.md)
* [Aplicativo Web registrado](tutorial-register-applications.md) em seu locatário
* [Políticas personalizadas](custom-policy-get-started.md) carregadas para seu locatário

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>Obter o pacote de início de entrada & conexão por telefone

Comece atualizando os arquivos de política personalizada de inscrição e entrada do telefone para trabalhar com seu locatário Azure AD B2C.

1. Localize os [arquivos de política personalizada de inscrição e entrada do telefone][starter-pack-phone] no clone local do repositório do pacote inicial ou baixe-os diretamente. Os arquivos de política XML estão localizados no seguinte diretório:

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. Em cada arquivo, substitua a cadeia de caracteres `yourtenant` pelo nome do seu locatário de Azure ad B2C. Por exemplo, se o nome do seu locatário B2C for *contosob2c*, todas as instâncias de `yourtenant.onmicrosoft.com` se tornarão `contosob2c.onmicrosoft.com` .

1. Conclua as etapas na seção [adicionar IDs de aplicativo à política personalizada](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) de introdução [às políticas personalizadas no Azure Active Directory B2C](custom-policy-get-started.md). Nesse caso, atualize `/phone-number-passwordless/` **`Phone_Email_Base.xml`** com as **IDs de aplicativo (cliente)** dos dois aplicativos que você registrou ao concluir os pré-requisitos, *IdentityExperienceFramework* e *ProxyIdentityExperienceFramework*.

## <a name="upload-the-policy-files"></a>Carregar os arquivos de política

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até o locatário do Azure ad B2C.
1. Em **Políticas**, selecione **Identity Experience Framework**.
1. Selecione **Carregar política personalizada**.
1. Carregue os arquivos de política na seguinte ordem:
    1. *Phone_Email_Base.xml*
    1. *SignUpOrSignInWithPhone.xml*
    1. *SignUpOrSignInWithPhoneOrEmail.xml*
    1. *ProfileEditPhoneOnly.xml*
    1. *ProfileEditPhoneEmail.xml*
    1. *ChangePhoneNumber.xml*
    1. *PasswordResetEmail.xml*

À medida que você carrega cada arquivo, o Azure adiciona o prefixo `B2C_1A_` .

## <a name="test-the-custom-policy"></a>Teste a política personalizada

1. Em **políticas personalizadas**, selecione **B2C_1A_SignUpOrSignInWithPhone**.
1. Em **Selecionar aplicativo**, selecione o aplicativo *webapp1* que você registrou ao concluir os pré-requisitos.
1. Para **selecionar URL de resposta**, escolha `https://jwt.ms` .
1. Selecione **executar agora** e se inscrever usando um endereço de email ou um número de telefone.
1. Selecione **executar agora** novamente e entre com a mesma conta para confirmar que você tem a configuração correta.

## <a name="get-user-account-by-phone-number"></a>Obter conta de usuário por número de telefone

Um usuário que se inscreve com um número de telefone, sem um endereço de email de recuperação, é registrado no diretório Azure AD B2C com seu número de telefone como o nome de entrada. Para alterar o número de telefone, sua equipe de suporte ou assistência técnica deve primeiro localizar sua conta e, em seguida, atualizar seu número de telefone.

Você pode encontrar um usuário pelo número de telefone (nome de entrada) usando [Microsoft Graph](microsoft-graph-operations.md):

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+{phone number}' and c/issuer eq '{tenant name}.onmicrosoft.com')
```

Por exemplo:

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+450334567890' and c/issuer eq 'contosob2c.onmicrosoft.com')
```

## <a name="next-steps"></a>Próximas etapas

Você pode encontrar o pacote de início de política personalizada de inscrição e entrada do telefone (e outros pacotes de início) no GitHub: [Azure-Samples/Active-Directory-B2C-Custom-Policy-starterpack/Scenarios/número de telefone-sem senha][starter-pack-phone] os arquivos de política do pacote inicial usam perfis técnicos da autenticação multifator e as transformações de declarações de número de telefone:
* [Definir um perfil técnico de autenticação multifator do Azure AD](multi-factor-auth-technical-profile.md)
* [Definir transformações de declarações de número de telefone](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
