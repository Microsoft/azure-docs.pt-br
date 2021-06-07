---
title: Configurar as informações de segurança de um prompt de entrada – Azure AD
description: Como configurar as informações de segurança para sua conta corporativa ou de estudante após isso ser solicitado a você na página de entrada da organização.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 04/07/2020
ms.author: curtand
ms.openlocfilehash: 016e0de26bf3a9513940be161cfecf6d1c2b9d02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88798492"
---
# <a name="set-up-your-security-info-from-a-sign-in-prompt"></a>Configurar as informações de segurança de um prompt de entrada

Você poderá seguir estas etapas se for solicitado a você que configure suas informações de segurança imediatamente após entrar na sua conta corporativa ou de estudante.

Você verá esse prompt apenas se não tiver configurado as informações de segurança exigidas pela sua organização. Se já tiver configurado suas informações de segurança mas quiser fazer alterações, você poderá seguir as etapas nos diversos artigos explicativos baseados em método. Para obter mais informações, confira [Adicionar ou atualizar sua visão de geral de informações de segurança](./security-info-setup-auth-app.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="security-verification-versus-password-reset-authentication"></a>Autenticação por redefinição de senha versus verificação de segurança

Métodos de informações de segurança são usados para a verificação de segurança de dois fatores e a redefinição de senha. No entanto, nem todos os métodos podem ser usados para ambos.

| Método | Usado para |
| ------ | -------- |
| Aplicativo autenticador | Autenticação por verificação de dois fatores e por redefinição de senha. |
| Mensagens de texto | Autenticação por verificação de dois fatores e por redefinição de senha. |
| Chamadas telefônicas | Autenticação por verificação de dois fatores e por redefinição de senha. |
| Chave de segurança | Autenticação por verificação de dois fatores e por redefinição de senha. |
| Conta de email | Somente autenticação por redefinição de senha. Você precisará escolher outro método de verificação de dois fatores. |
| Perguntas de segurança | Somente autenticação por redefinição de senha. Você precisará escolher outro método de verificação de dois fatores. |

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

    >[!Note]
    >Se esta for a primeira vez que está configurando o aplicativo Microsoft Authenticator, você poderá receber um prompt perguntando se deseja permitir que o aplicativo acesse sua câmera (iOS) ou que o aplicativo tire fotos e grave vídeo (Android). Você precisa selecionar **Permitir** para que o aplicativo autenticador possa acessar sua câmera para tirar uma foto do código QR na próxima etapa. Se não permitir acesso da câmera, você ainda poderá configurar o aplicativo autenticador, mas precisará adicionar as informações de código manualmente. Para obter informações sobre como adicionar o código manualmente, confira [Adicionar uma conta manualmente ao aplicativo](user-help-auth-app-add-account-manual.md).

5. Volte para a página **Configurar sua conta** em seu computador e selecione **Avançar**.

    A página **Digitalizar o código QR** será exibida.

    ![Digitalize o código QR usando o aplicativo Autenticador](media/security-info/securityinfo-prompt-auth-app-qrcode.png)

6. Digitalize o código fornecido com o leitor de código QR do aplicativo Microsoft Authenticator, que aparecerá em seu dispositivo móvel depois que você criar sua conta corporativa ou de estudante na etapa 5.

    O aplicativo autenticador adicionará sua conta corporativa ou de estudante com sucesso, sem a necessidade de qualquer informação adicional sobre você. No entanto, se o leitor de código QR não puder ler o código, você poderá selecionar **Não é possível verificar a imagem do QR** e inserir manualmente o código e a URL no aplicativo Microsoft Authenticator. Para obter mais informações sobre como adicionar um código manualmente, confira [Adicionar uma conta ao aplicativo manualmente](user-help-auth-app-add-account-manual.md).

7. Selecione **Avançar** na página **Digitalizar o código QR** em seu computador.

    Uma notificação será enviada ao aplicativo Microsoft Authenticator em seu dispositivo móvel, para testar a sua conta.

    ![Testar a sua conta com o aplicativo autenticador](media/security-info/securityinfo-prompt-test-app.png)

8. Aprove a notificação no aplicativo Microsoft Authenticator e selecione **Avançar**.

    ![Notificação de êxito, conexão do aplicativo à conta](media/security-info/securityinfo-prompt-auth-app-success.png)

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

    >[!Note]
    >Se a sua organização exigir o uso de senhas de aplicativo, poderá haver uma seção adicional neste assistente, em que será possível configurar isso. Se você vir uma terceira seção chamada **Senhas de aplicativo**, será necessário preenchê-la antes de poder concluir o assistente. Para ver as etapas necessárias para adicionar uma senha de aplicativo, consulte a seção [Gerenciar suas senhas de aplicativo](#manage-your-app-passwords) deste artigo.

### <a name="manage-your-app-passwords"></a>Gerenciar suas senhas de aplicativo

Alguns aplicativos, como o Outlook 2010, não dão suporte à verificação em duas etapas. Essa falta de suporte significa que, se você estiver usando a verificação em duas etapas, o aplicativo não funcionará. Para contornar este problema, você pode criar uma senha gerada automaticamente para usar com cada aplicativo que não seja o navegador, separado da sua senha normal.

>[!Note]
>Caso você não veja essa opção no assistente, isso significa que o administrador não a configurou. Se ela não estiver configurada, mas você souber que precisa usar senhas de aplicativo, siga as etapas em [Configurar senhas de aplicativo da página Informações de segurança](security-info-app-passwords.md).

Ao usar senhas de aplicativos, é importante lembrar:

- As senhas de aplicativos são geradas automaticamente e inseridas apenas uma vez por aplicativo.

- Há um limite de 40 senhas por usuário. Se você tentar criar um após esse limite, será solicitado que você exclua uma senha existente antes de poder criar a nova.

- Use uma senha de aplicativo por dispositivo, não por aplicativo. Por exemplo, crie uma única senha para todos os aplicativos em seu laptop e, em seguida, outra senha única para todos os aplicativos em sua área de trabalho.

#### <a name="to-add-app-passwords-in-the-sign-in-wizard"></a>Para adicionar senhas de aplicativo no assistente de entrada

1. Após concluir as seções anteriores do assistente, selecione **Avançar** e conclua a seção **Senhas de aplicativo**.

2. Digite o nome do aplicativo que necessita da senha, por exemplo `Outlook 2010` e, em seguida, selecione **Avançar**.

    ![Adicione o nome da senha de aplicativo no assistente](media/security-info/app-password-app-password.png)

3. Copie o código da senha da tela **Senha de aplicativo** e cole-o na área de **Senha** do aplicativo (neste exemplo, Outlook 2010).

    ![Página de senha do aplicativo, com a senha a ser copiada](media/security-info/app-password-copy-password.png)

4. Depois de copiar a senha e colar no aplicativo, volte para esse assistente para verificar se todas as informações do método de entrada estão corretas e, em seguida, selecione **Concluído**.

    ![Página de senha do aplicativo, com aviso de conclusão](media/security-info/app-password-complete.png)

## <a name="next-steps"></a>Próximas etapas

- Para alterar, excluir ou atualizar métodos de informações de segurança padrão, confira:

    - [Configurar informações de segurança de um aplicativo autenticador](security-info-setup-auth-app.md).

    - [Configurar informações de segurança para mensagens de texto](security-info-setup-text-msg.md).

    - [Configurar informações de segurança para usar chamadas telefônicas](security-info-setup-phone-number.md).

    - [Configurar informações de segurança para usar email](security-info-setup-email.md).

    - [Configurar informações de segurança para usar perguntas de segurança predefinidas](security-info-setup-questions.md).

- Para obter informações sobre como entrar usando seu método especificado, confira [Como entrar](user-help-sign-in.md).

- Redefina sua senha se você a perdeu ou esqueceu, do [portal de redefinição de senha](https://passwordreset.microsoftonline.com/) ou siga as etapas no artigo [Redefinir sua senha de trabalho ou escola](active-directory-passwords-update-your-own-password.md).

- Obter solução de problemas e ajuda para problemas de logon na [não pode entrar sua conta da Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artigo.