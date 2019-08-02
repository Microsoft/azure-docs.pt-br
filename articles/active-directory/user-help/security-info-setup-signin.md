---
title: Configurar informações de segurança (versão prévia) do prompt de entrada – Azure Active Directory | Microsoft Docs
description: Como configurar informações de segurança para sua conta corporativa ou de estudante se isso for solicitado a você na página de entrada da organização.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: overview
ms.date: 02/13/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: c20256fb712d9381ba2adc90e2e68ce4fdc8911a
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382785"
---
# <a name="set-up-your-security-info-preview-from-the-sign-in-page-prompt"></a>Configure suas informações de segurança (versão prévia) no aviso na página de entrada

Você poderá seguir estas etapas se for solicitado a você que configure suas informações de segurança imediatamente após entrar na sua conta corporativa ou de estudante.

Você verá esse prompt apenas se não tiver configurado as informações de segurança exigidas pela sua organização. Se já tiver configurado suas informações de segurança mas quiser fazer alterações, você poderá seguir as etapas nos diversos artigos explicativos baseados em método. Para obter mais informações, confira [Adicionar ou atualizar sua visão de geral de informações de segurança](security-info-add-update-methods-overview.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="sign-in-to-your-work-or-school-account"></a>Entrar em sua conta corporativa ou de estudante

Depois de entrar na sua conta corporativa ou de estudante, você verá um aviso solicitando que você forneça mais informações antes de permitir seu acesso à conta.

![Aviso solicitando mais informações](media/security-info/securityinfo-prompt.png)

## <a name="set-up-your-security-info-using-the-wizard"></a>Configurar suas informações de segurança usando o assistente

Siga estas etapas para configurar suas informações de segurança para sua conta corporativa ou de estudante por meio do aviso.

>[!Important]
>Isso é apenas um exemplo do processo. Dependendo dos requisitos da sua organização, o administrador pode ter definido métodos de verificação diferentes que você precisará configurar durante esse processo. Neste exemplo, estamos exigindo dois métodos, o aplicativo Microsoft Authenticator e um número de telefone celular para mensagens de texto ou chamadas de verificação.

1. Depois de selecionar **Avançar** no aviso, um **assistente Mantenha sua conta segura** é exibido, mostrando o primeiro método que o administrador e a organização exigem que você configure. Neste exemplo, trata-se do aplicativo Microsoft Authenticator.

   > [!Note]
   > Se você não quiser usar o aplicativo Microsoft Authenticator, selecione o link **Desejo usar um aplicativo autenticador diferente**.
   >
   > Se a sua organização permitir que você escolha um método que não seja o aplicativo autenticador, você poderá selecionar o link **Desejo configurar um método diferente**.

    ![Assistente Mantenha sua conta segura, mostrando a página de download do aplicativo de autenticação](media/security-info/securityinfo-prompt-get-auth-app.png)

2. Selecione **Baixar agora** para baixar e instalar o aplicativo Microsoft Authenticator em seu dispositivo móvel e, em seguida, selecione **Avançar**. Para obter mais informações sobre como baixar e instalar o aplicativo, confira [Baixar e instalar o aplicativo Microsoft Authenticator](user-help-auth-app-download-install.md).

    ![Assistente Mantenha sua conta segura, mostrando a configuração do autenticado de sua página de conta](media/security-info/securityinfo-prompt-auth-app-setup-acct.png)

3. Permaneça na página **Configurar sua conta** enquanto configura o aplicativo Microsoft Authenticator em seu dispositivo móvel.

4. Abra o aplicativo Microsoft Authenticator, permita notificações (se solicitado), selecione **Adicionar conta** pelo ícone **Personalizar e controlar** no canto superior direito e, em seguida, selecione **Conta corporativa ou de estudante**.

5. Volte para a página **Configurar sua conta** em seu computador e selecione **Avançar**.

    A página **Digitalizar o código QR** será exibida.

    ![Digitalize o código QR usando o aplicativo Autenticador](media/security-info/securityinfo-prompt-auth-app-qrcode.png)

6. Digitalize o código fornecido com o leitor de código QR do aplicativo Microsoft Authenticator, que aparecerá em seu dispositivo móvel depois que você criar sua conta corporativa ou de estudante na etapa 5.

    O aplicativo autenticador adicionará sua conta corporativa ou de estudante com sucesso, sem a necessidade de qualquer informação adicional sobre você. No entanto, se o leitor de código QR não puder ler o código, você poderá selecionar **Não é possível verificar o link do código QR** e inserir manualmente o código e o URL no aplicativo Microsoft Authenticator. Para obter mais informações sobre como adicionar um código manualmente, confira [Adicionar uma conta ao aplicativo manualmente](user-help-auth-app-add-account-manual.md).

7. Selecione **Avançar** na página **Digitalizar o código QR** em seu computador.

    Uma notificação será enviada ao aplicativo Microsoft Authenticator em seu dispositivo móvel, para testar a sua conta.

    ![Testar a sua conta com o aplicativo autenticador](media/security-info/securityinfo-prompt-test-app.png)

8. Aprove a notificação no aplicativo Microsoft Authenticator e selecione **Avançar**.

    ![Notificação de êxito, conexão do aplicativo à conta](media/security-info/securityinfo-prompt-auth-app-success.png).

    Por padrão, suas informações de segurança são atualizadas para usar o aplicativo Microsoft Authenticator a fim de verificar sua identidade ao usar a verificação em duas etapas ou a redefinição de senha.

9. Na página de configuração **Telefone**, escolha se deseja receber uma mensagem de texto ou uma chamada telefônica e, em seguida, selecione **Avançar**. Para os fins deste exemplo, estamos usando mensagens de texto, portanto, você deve usar um número de telefone para um dispositivo que pode aceitar mensagens de texto.

    ![Começar a configurar seu número de telefone para mensagens de texto](media/security-info/securityinfo-prompt-text-msg.png)

    Uma mensagem de texto é enviada ao número de telefone. Se preferir receber uma chamada telefônica, o processo será o mesmo. No entanto, você receberá uma chamada telefônica com instruções, em vez de uma mensagem de texto.

10. Insira o código fornecido pela mensagem de texto enviada ao seu dispositivo móvel e, em seguida, selecione **Avançar**.

    ![Testar a sua conta com a mensagem de texto](media/security-info/securityinfo-prompt-text-msg-enter-code.png)

11. Examine a notificação de êxito e, em seguida, selecione **Concluído**.

    ![Notificação de sucesso](media/security-info/securityinfo-prompt-call-answered-success.png)

    Suas informações de segurança são atualizadas para usar mensagens de texto como um método de backup para confirmar sua identidade ao usar a verificação em duas etapas ou a redefinição de senha.

12. Examine a página **Sucesso** para confirmar que você configurou com êxito o aplicativo Microsoft Authenticator e um método de telefone (mensagem de texto ou chamada telefônica) para suas informações de segurança e, em seguida, selecione **Concluído**.

    ![Página Assistente concluído com êxito](media/security-info/securityinfo-prompt-setup-success.png)

## <a name="next-steps"></a>Próximas etapas

- Para alterar, excluir ou atualizar métodos de informações de segurança padrão, confira:

    - [Configurar informações de segurança de um aplicativo autenticador](security-info-setup-auth-app.md).

    - [Configurar informações de segurança para mensagens de texto](security-info-setup-text-msg.md).

    - [Configurar informações de segurança para usar chamadas telefônicas](security-info-setup-phone-number.md).

    - [Configurar informações de segurança para usar email](security-info-setup-email.md).

    - [Configurar informações de segurança para usar perguntas de segurança predefinidas](security-info-setup-questions.md).

- Para obter informações sobre como entrar usando seu método especificado, confira [Como entrar](user-help-sign-in.md).

- Redefina sua senha se você a perdeu ou esqueceu, do [portal de redefinição de senha](https://passwordreset.microsoftonline.com/) ou siga as etapas no artigo [Redefinir sua senha de trabalho ou escola](user-help-reset-password.md).

- Obter solução de problemas e ajuda para problemas de logon na [não pode entrar sua conta da Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artigo.
