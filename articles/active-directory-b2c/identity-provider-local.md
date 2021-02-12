---
title: Configurar Azure AD B2C provedor de identidade da conta local
titleSuffix: Azure AD B2C
description: Definir os tipos de identidade que o usa pode usar para se inscrever ou entrar (email, nome de usuário, número de telefone) em seu locatário de Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/19/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: e6b520a32755379fce5326e25b90f135b0d67755
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98574292"
---
# <a name="set-up-the-local-account-identity-provider"></a>Configurar o provedor de identidade da conta local

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure AD B2C fornece várias maneiras em que os usuários podem autenticar um usuário. Os usuários podem entrar em uma conta local usando nome de usuário e senha, verificação de telefone (também conhecida como senha sem autenticação) ou provedores de identidade social. A inscrição de email é habilitada por padrão nas configurações do provedor de identidade da conta local. 

Este artigo descreve como os usuários criam suas contas locais para esse locatário Azure AD B2C. Para identidades sociais ou corporativas, em que a identidade do usuário é gerenciada por um provedor de identidade federado, como o Facebook e o Google, consulte [Adicionar um provedor de identidade](add-identity-provider.md).

## <a name="email-sign-in"></a>Entrada de email

Com a opção de email, os usuários podem entrar/acompanhar seu endereço de email e senha:

- Para **entrar**, os usuários são solicitados a fornecer seu email e senha.
- **Inscreva-se**, os usuários serão solicitados a fornecer um endereço de email, que será verificado na inscrição (opcional) e se tornará sua ID de logon. Em seguida, o usuário insere todas as outras informações solicitadas na página de inscrição, por exemplo, nome de exibição, nome e sobrenome. Em seguida, selecione continuar para criar a conta.
- **Redefinição de senha**, os usuários devem inserir e verificar seus emails, após o qual, o usuário pode redefinir a senha

![Experiência de inscrição ou entrada de email](./media/identity-provider-local/local-account-email-experience.png)

## <a name="username-sign-in"></a>Entrada de nome de usuário

Com a opção de usuário, os usuários podem se inscrever/entrar e obter um nome de usuário e uma senha:

- **Entrada**: os usuários são solicitados a fornecer seu nome de usuário e senha.
- **Inscrição**: os usuários serão solicitados a fornecer um nome de usuário, que se tornará sua ID de logon. Os usuários também serão solicitados a fornecer um endereço de email, que será verificado na inscrição. O endereço de email será usado durante um fluxo de redefinição de senha. O usuário insere todas as outras informações solicitadas na página de inscrição, por exemplo, nome de exibição, nome e sobrenome. O usuário seleciona continuar para criar a conta.
- **Redefinição de senha**: os usuários devem inserir seu nome de usuário e o endereço de email associado. O endereço de email deve ser verificado, após o qual o usuário pode redefinir a senha.

![Experiência de inscrição ou entrada de nome de usuário](./media/identity-provider-local/local-account-username-experience.png)

## <a name="phone-sign-in-preview"></a>Entrada pelo telefone (versão prévia)

A autenticação sem senha é um tipo de autenticação em que um usuário não precisa entrar com sua senha. Com a inscrição e a entrada do telefone, o usuário pode se inscrever no aplicativo usando um número de telefone como seu identificador de logon primário. O usuário terá a seguinte experiência durante a inscrição e a entrada:

- **Entrada**: se o usuário tiver uma conta existente com número de telefone como seu identificador, o usuário inserirá seu número de telefone e selecionará *entrar*. Eles confirmam o país e o número de telefone selecionando *continuar* e um código de verificação ocasional é enviado para o telefone. O usuário insere o código de verificação e seleciona *continuar* para entrar.
- **Inscreva-** se: se o usuário ainda não tiver uma conta para seu aplicativo, ele poderá criar uma clicando no link *inscrever-se agora* . 
    1. Uma página de inscrição é exibida, onde o usuário seleciona seu *país*, insere seu número de telefone e seleciona *enviar código*. 
    1. Um código de verificação único é enviado para o número de telefone do usuário. O usuário insere o *código de verificação* na página de inscrição e seleciona *verificar código*. (Se o usuário não puder recuperar o código, ele poderá selecionar *Enviar novo código*). 
    1. O usuário insere todas as outras informações solicitadas na página de inscrição, por exemplo, nome de exibição, nome e sobrenome. Depois selecione Continuar.
    1. Em seguida, o usuário será solicitado a fornecer um **email de recuperação**. O usuário insere seu endereço de email e seleciona *enviar código de verificação*. Um código é enviado para a caixa de entrada de email do usuário, que pode ser recuperada e inserida no código de verificação. Em seguida, o usuário seleciona verificar código.
    1. Depois que o código é verificado, o usuário seleciona *criar* para criar sua conta. 

![Experiência de inscrição ou entrada no telefone](./media/identity-provider-local/local-account-phone-experience.png)

### <a name="pricing"></a>Preços

As senhas de uso único são enviadas para seus usuários usando mensagens de texto SMS. Dependendo do operador de rede móvel, você poderá ser cobrado por cada mensagem enviada. Para obter informações sobre preços, consulte a seção **encargos separados** de [preços Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

> [!NOTE]
> A autenticação multifator (MFA) é desabilitada por padrão quando você configura um fluxo de usuário com inscrição no telefone. Você pode habilitar a MFA em fluxos de usuário com inscrição no telefone, mas como um número de telefone é usado como o identificador primário, o email de senha de uso único é a única opção disponível para o segundo fator de autenticação.

### <a name="phone-recovery"></a>Recuperação de telefone

Quando você habilita a inscrição e a entrada do telefone para seus fluxos de usuário, também é uma boa ideia habilitar o recurso de email de recuperação. Com esse recurso, um usuário pode fornecer um endereço de email que pode ser usado para recuperar sua conta quando eles não têm seu telefone. Esse endereço de email é usado apenas para recuperação de conta. Ele não pode ser usado para entrar.

- Quando o prompt do email de recuperação está **ativado**, um usuário que está se inscrevendo pela primeira vez é solicitado a verificar um email de backup. Um usuário que não tenha fornecido um email de recuperação antes do é solicitado a verificar um email de backup durante a próxima entrada.

- Quando o email de recuperação estiver **desativado**, um usuário se inscrevendo ou entrando não será mostrado no prompt de email de recuperação.
 
As capturas de tela a seguir demonstram o fluxo de recuperação do telefone:

![Fluxo do usuário de recuperação de telefone](./media/identity-provider-local/local-account-change-phone-flow.png)


## <a name="phone-or-email-sign-in-preview"></a>Entrada de telefone ou email (versão prévia)

Você pode optar por combinar a [entrada pelo telefone](#phone-sign-in-preview)e a entrada de [email](#email-sign-in). Na página inscrever-se ou entrar, o usuário pode digitar um número de telefone ou endereço de email. Com base na entrada do usuário, Azure AD B2C leva o usuário para o fluxo correspondente. 

![Experiência de inscrição ou entrada de email ou de telefone](./media/identity-provider-local/local-account-phone-and-email-experience.png)

::: zone pivot="b2c-user-flow"

## <a name="configure-local-account-identity-provider-settings"></a>Definir configurações do provedor de identidade da conta local

Você pode configurar os provedores de identidade locais disponíveis para serem usados dentro de um fluxo de usuário habilitando ou desabilitando os provedores (email, nome de usuário ou número de telefone).  Você pode ter mais de um provedor de identidade local habilitado no nível do locatário.

Um fluxo de usuário só pode ser configurado para usar um dos provedores de identidade de conta local a qualquer momento. Cada fluxo de usuário pode ter um provedor de identidade de conta local diferente definido, se mais de um tiver sido habilitado no nível do locatário.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário do Azure AD.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Em **Gerenciar**, selecione **Provedores de identidade**.
1. Na lista de provedores de identidade, selecione **Conta local**.
1. Na página **Configurar IDP local** , selecione pelo menos um dos tipos de identidade permitidos que os consumidores podem usar para criar suas contas locais em seu locatário de Azure ad B2C.
1. Clique em **Salvar**.

## <a name="configure-your-user-flow"></a>Configurar seu fluxo de usuário

1. No menu à esquerda da portal do Azure, selecione **Azure ad B2C**.
1. Em **Políticas**, selecione **Fluxos de usuários (políticas)** .
1. Selecione o fluxo de usuário para o qual você gostaria de configurar a experiência de inscrição e de entrada.
1. Selecionar **provedores de identidade**
1. Nas **contas locais**, selecione uma das seguintes opções: **inscrição de email**,  **inscrição de ID de usuário**, inscrição de **telefone**, **inscrição de telefone/email** ou **nenhuma**.

### <a name="enable-the-recovery-email-prompt"></a>Habilitar o prompt de email de recuperação

Se você escolher a opção **inscrição no telefone**, inscrição **por telefone/email** , habilite o prompt email de recuperação.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.
1. No portal do Azure, pesquise e selecione **Azure AD B2C**.
1. Em Azure AD B2C, em **políticas**, selecione **fluxos de usuário**.
1. Selecione o fluxo de usuário na lista.
1. Em **Configurações**, selecione **Propriedades**.
1. Ao lado de **habilitar solicitação de email de recuperação para inscrição de número de telefone e entrar (versão prévia)**, selecione:
   - **Em** para mostrar o prompt de email de recuperação durante a inscrição e a entrada.
   - **Desativado** para ocultar o prompt de email de recuperação.
1. Clique em **Salvar**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="get-the-starter-pack"></a>Obter o pacote de início

As políticas personalizadas são um conjunto de arquivos XML que você carrega em seu locatário Azure AD B2C para definir percursos do usuário. Fornecemos pacotes de início com várias políticas predefinidas. Baixe o pacote inicial relevante: 

- [Entrada de email](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts)
- [Entrada de nome de usuário](https://github.com/azure-ad-b2c/samples/tree/master/policies/username-signup-or-signin)
- [Entrada pelo telefone](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless). Selecione a política de terceira parte confiável [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhone.xml) . 
- [Entrada de telefone ou email](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless). Selecione a política de terceira parte confiável [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhoneOrEmail.xml) .

Depois de baixar o pacote inicial.

1. Em cada arquivo, substitua a cadeia de caracteres `yourtenant` pelo nome do seu locatário de Azure ad B2C. Por exemplo, se o nome do seu locatário B2C for *contosob2c*, todas as instâncias de `yourtenant.onmicrosoft.com` se tornarão `contosob2c.onmicrosoft.com` .

1. Conclua as etapas na seção [adicionar IDs de aplicativo à política personalizada](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) de introdução [às políticas personalizadas no Azure Active Directory B2C](custom-policy-get-started.md). Por exemplo, atualize `/phone-number-passwordless/` **`Phone_Email_Base.xml`** com as **IDs de aplicativo (cliente)** dos dois aplicativos que você registrou ao concluir os pré-requisitos, *IdentityExperienceFramework* e *ProxyIdentityExperienceFramework*.
1. Carregar os arquivos de política

::: zone-end

## <a name="next-steps"></a>Próximas etapas

- [Adicionar provedores de identidade externos](tutorial-add-identity-providers.md)
- [Criar um fluxo de usuário](tutorial-create-user-flows.md)
