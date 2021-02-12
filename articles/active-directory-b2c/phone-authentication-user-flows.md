---
title: Configurar o provedor de identidade da conta local
titleSuffix: Azure AD B2C
description: Defina os tipos de identidade que você pode usar (email, nome de usuário, número de telefone) para autenticação de conta local ao configurar fluxos de usuário em seu locatário Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/01/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1ed0ca069f29413083356a3147d4b97f1b6ed981
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525188"
---
# <a name="set-up-phone-sign-up-and-sign-in-for-user-flows-preview"></a>Configurar a inscrição e a entrada do telefone para fluxos de usuário (visualização)

> [!NOTE]
> Os recursos de inscrição e conexão do telefone e de email de recuperação para fluxos de usuário estão em visualização pública.

Além de email e nome de usuário, você pode habilitar o número de telefone como uma opção de inscrição para todo o locatário adicionando inscrição e entrada no provedor de identidade da conta local. Depois de habilitar a inscrição e a entrada do telefone para contas locais, você pode adicionar a inscrição do telefone aos seus fluxos de usuário.

Configurar a inscrição e a entrada do telefone em um fluxo de usuário envolve as seguintes etapas:

- [Configure o locatário](#configure-phone-sign-up-and-sign-in-tenant-wide) de inscrição e entrada do telefone em todo o provedor de identidade da conta local para aceitar um número de telefone como a identidade de um usuário. 

- [Adicione a inscrição do telefone ao seu fluxo de usuário](#add-phone-sign-up-to-a-user-flow) para permitir que os usuários se inscrevam no seu aplicativo usando seu número de telefone.

- [Habilite o prompt de email de recuperação (versão prévia)](#enable-the-recovery-email-prompt-preview) para permitir que os usuários especifiquem um email que possa ser usado para recuperar sua conta quando eles não tiverem seu telefone.

A autenticação multifator (MFA) é desabilitada por padrão quando você configura um fluxo de usuário com inscrição no telefone. Você pode habilitar a MFA em fluxos de usuário com inscrição no telefone, mas como um número de telefone é usado como o identificador primário, o email de senha de uso único é a única opção disponível para o segundo fator de autenticação.

## <a name="configure-phone-sign-up-and-sign-in-tenant-wide"></a>Configurar a inscrição e a entrada do telefone em todo o locatário

A inscrição de email é habilitada por padrão nas configurações do provedor de identidade da conta local. Você pode alterar os tipos de identidade para os quais terá suporte em seu locatário selecionando ou desmarcando a inscrição, o nome de usuário ou o número de telefone de email.

1. Entre no [portal do Azure](https://portal.azure.com).

2. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário do Azure AD.

3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.

4. Em **Gerenciar**, selecione **Provedores de identidade**.

5. Na lista de provedores de identidade, selecione **Conta local**.

   ![Provedores de identidade selecione a conta local](media/phone-authentication-user-flows/identity-provider-local-account.png)

1. Na página **Configurar IDP local** , verifique se o **telefone** está selecionado como um dos tipos de identidade permitidos que os consumidores podem usar para criar suas contas locais em seu locatário de Azure ad B2C.

   ![Selecionar os tipos de identidade permitidos](media/phone-authentication-user-flows/configure-local-idp.png)

1. Selecione **Salvar**.

## <a name="add-phone-sign-up-to-a-user-flow"></a>Adicionar inscrição do telefone a um fluxo de usuário

Depois de ter adicionado a inscrição no telefone como uma opção de identidade para contas locais, você pode adicioná-lo aos fluxos de usuário, desde que eles sejam as versões de fluxo de usuário mais recentes **recomendadas** . Veja a seguir um exemplo que mostra como adicionar a inscrição do telefone a novos fluxos de usuário. Mas você também pode adicionar a inscrição do telefone a fluxos de usuário da versão recomendada existentes (selecione **fluxos dos usuários**  >  *nome do fluxo de usuário*  >  **provedores de identidade**  >  **inscrição no telefone local da conta**). 

Veja um exemplo que mostra como adicionar a inscrição por telefone a um novo fluxo de usuário.

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.

    ![Locatário do B2C, painel Diretório e Assinatura, portal do Azure](./media/phone-authentication-user-flows/directory-subscription-pane.png)

3. No portal do Azure, pesquise e selecione **Azure AD B2C**.
4. Em **Políticas**, selecione **Fluxos dos usuários** e, em seguida, selecione **Novo fluxo de usuário**.

    ![Página fluxos dos usuários no portal com o botão Novo fluxo de usuário realçado](./media/phone-authentication-user-flows/signup-signin-user-flow.png)

5. Na página **Criar um fluxo de usuário**, selecione o fluxo do usuário **Inscrever-se e entrar**.

    ![Página Selecione um fluxo de usuário com o fluxo Inscrever-se e entrar realçado](./media/phone-authentication-user-flows/select-user-flow-type.png)

6. Em **Selecionar uma versão**, selecione **Recomendado** e, em seguida, selecione **Criar**. ([Saiba mais](user-flow-versions.md) sobre as versões de fluxo de usuário.)

    ![Botão criar fluxo de usuário](./media/phone-authentication-user-flows/select-version.png)

7. Insira um **Nome** para o fluxo de usuário. Por exemplo, *signupsignin1*.
8. Na seção **provedores de identidade** , em **contas locais**, selecione **inscrição de telefone**.

   ![Opção de inscrição de telefone do fluxo do usuário selecionada](media/phone-authentication-user-flows/user-flow-phone-signup.png)

9. Em **provedores de identidade social**, selecione qualquer outro provedor de identidade que você queira permitir para esse fluxo de usuário.

   > [!NOTE]
   > A autenticação multifator (MFA) é desabilitada por padrão para fluxos de usuário de inscrição. Você pode habilitar a MFA para um fluxo de usuário de inscrição de telefone, mas como um número de telefone é usado como o identificador primário, o email de senha de uso único é a única opção disponível para o segundo fator de autenticação.

1. Na seção **atributos de usuário e declarações de token** , escolha as declarações e os atributos que você deseja coletar e enviar do usuário durante a inscrição. Por exemplo, selecione **Mostrar mais** e, depois, escolha atributos e declarações de **País/região**, **Nome de exibição** e **Código postal**. Selecione **OK**.

1. Selecione **Criar** para adicionar o fluxo de usuário. Um prefixo *B2C_1* é anexado automaticamente ao nome.

## <a name="enable-the-recovery-email-prompt-preview"></a>Habilitar o prompt de email de recuperação (visualização)

Quando você habilita a inscrição e a entrada do telefone para seus fluxos de usuário, também é uma boa ideia habilitar o recurso de email de recuperação. Com esse recurso, um usuário pode fornecer um endereço de email que pode ser usado para recuperar sua conta quando eles não têm seu telefone. Esse endereço de email é usado apenas para recuperação de conta. Ele não pode ser usado para entrar.

- Quando o prompt do email de recuperação está **ativado**, um usuário que está se inscrevendo pela primeira vez é solicitado a verificar um email de backup. Um usuário que não tenha fornecido um email de recuperação antes do é solicitado a verificar um email de backup durante a próxima entrada.

- Quando o email de recuperação estiver **desativado**, um usuário se inscrevendo ou entrando não será mostrado no prompt de email de recuperação.
 
Você pode habilitar o prompt de email de recuperação nas propriedades do fluxo de usuário.

> [!NOTE]
> Antes de começar, verifique se você [adicionou a inscrição do telefone ao seu fluxo de usuário](#add-phone-sign-up-to-a-user-flow) , conforme descrito acima.

### <a name="to-enable-the-recovery-email-prompt"></a>Para habilitar o prompt de email de recuperação

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.
3. No portal do Azure, pesquise e selecione **Azure AD B2C**.
4. Em Azure AD B2C, em **políticas**, selecione **fluxos de usuário**.
5. Selecione o fluxo de usuário na lista.
6. Em **Configurações**, selecione **Propriedades**.
7. Ao lado de **habilitar solicitação de email de recuperação para inscrição de número de telefone e entrar (versão prévia)**, selecione:

   - **Em** para mostrar o prompt de email de recuperação durante a inscrição e a entrada.
   - **Desativado** para ocultar o prompt de email de recuperação.

    ![Propriedades de fluxos de usuário com habilitar email de recuperação habilitado](./media/phone-authentication-user-flows/recovery-email-settings.png)

8. Selecione **Salvar**.

### <a name="to-test-the-recovery-email-prompt"></a>Para testar o prompt do email de recuperação

Depois de habilitar a inscrição e a entrada do telefone e o prompt de email de recuperação em seu fluxo de usuário, você poderá usar **executar fluxo de usuário** para testar a experiência do usuário.

1. Selecione **Políticas** > **Fluxos de usuário** e selecione o fluxo de usuário criado. Na página de visão geral do fluxo de usuário, selecione **Executar o fluxo de usuário**.

2. Para **Aplicativo**, selecione o aplicativo Web registrado na etapa 1. A **URL de resposta** deve mostrar `https://jwt.ms`.

3. Selecione **executar fluxo de usuário** e verifique o seguinte comportamento:

   - Um usuário que se inscreve pela primeira vez é solicitado a fornecer um email de recuperação. 
   - Um usuário que já se inscreveu, mas não forneceu um email de recuperação, é solicitado a fornecer um após a entrada.

4. Insira um endereço de email e, em seguida, selecione **enviar código de verificação**. Verifique se um código é enviado para a caixa de entrada de email que você forneceu. Recupere o código e insira-o na caixa **código de verificação** . Em seguida, selecione **verificar código**.

## <a name="next-steps"></a>Próximas etapas

- [Adicionar provedores de identidade externos](tutorial-add-identity-providers.md)
- [Criar um fluxo de usuário](tutorial-create-user-flows.md)