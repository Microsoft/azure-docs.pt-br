---
title: Configurar o aplicativo Microsoft Authenticator como método de verificação – Azure AD
description: Como configurar sua página de Informações de segurança (versão prévia) para verificar sua identidade usando o aplicativo Microsoft Authenticator como seu método de verificação.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 02/13/2019
ms.author: curtand
ms.openlocfilehash: c947bee0b702797a86d1e038f74c6c10e2b23eb4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103197484"
---
# <a name="set-up-the-microsoft-authenticator-app-as-your-verification-method"></a>Configurar o aplicativo Microsoft Authenticator como método de verificação

Siga estas instruções para adicionar os métodos de verificação em duas etapas e redefinição de senha. Depois da primeira configuração, você pode retornar para a página **Informações de segurança** a fim de adicionar, atualizar ou excluir suas informações de segurança.

Se for solicitado que você faça tais configurações imediatamente após entrar em sua conta corporativa ou de estudante, confira as etapas detalhadas no artigo [Configurar suas informações de segurança pela solicitação na página de entrada](security-info-setup-signin.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
> Se você não vir a opção do aplicativo autenticador, talvez sua organização não permita que você use essa opção para verificação. Nesse caso, você precisará escolher outro método ou entrar em contato com o suporte técnico da sua organização para obter mais assistência.

## <a name="security-vs-password-reset-verification"></a>Segurança versus verificação de redefinição de senha

Métodos de informações de segurança são usados para a verificação de segurança de dois fatores e a redefinição de senha. No entanto, nem todos os métodos podem ser usados para ambos.

| Método | Usado para |
| ------ | -------- |
| Aplicativo autenticador | Autenticação por verificação de dois fatores e por redefinição de senha. |
| Mensagens de texto | Autenticação por verificação de dois fatores e por redefinição de senha. |
| Chamadas telefônicas | Autenticação por verificação de dois fatores e por redefinição de senha. |
| Chave de segurança | Autenticação por verificação de dois fatores e por redefinição de senha. |
| Conta de email | Somente autenticação por redefinição de senha. Você precisará escolher outro método de verificação de dois fatores. |
| Perguntas de segurança | Somente autenticação por redefinição de senha. Você precisará escolher outro método de verificação de dois fatores. |

## <a name="set-up-the-microsoft-authenticator-app-from-the-security-info-page"></a>Configurar o aplicativo Microsoft Authenticator pela página de Informações de segurança

Dependendo das configurações da sua organização, você poderá usar um aplicativo de autenticação como um dos seus métodos de informações de segurança. Não é obrigatório usar o aplicativo Microsoft Authenticator. Você poderá escolher um aplicativo diferente durante o processo de configuração. No entanto, este artigo usa o aplicativo Microsoft Authenticator.

> [!IMPORTANT]
> Se você tiver configurado o aplicativo Microsoft Authenticator em cinco dispositivos diferentes ou se tiver usado cinco tokens de hardware, não conseguirá configurar um sexto, e poderá ver a seguinte mensagem de erro:
> 
> **Você não pode configurar Microsoft Authenticator porque você já tem cinco aplicativos autenticadores ou tokens de hardware. Entre em contato com seu administrador para excluir um de seus aplicativos autenticadores ou tokens de hardware.**

### <a name="to-set-up-the-microsoft-authenticator-app"></a>Configurar o aplicativo Microsoft Authenticator

1. Entre em sua conta corporativa ou de estudante e vá para a página https://myaccount.microsoft.com/.

    ![Página Meu Perfil, com links para Informações de segurança em destaque](media/security-info/securityinfo-myprofile-page.png)

2. Selecione **informações de segurança** no menu à esquerda ou usando o link no painel **informações de segurança** . Se você já tiver se registrado, será solicitada a verificação de dois fatores. Em seguida, selecione **Adicionar método** no painel **informações de segurança** .

    ![Página Informações de segurança com a opção Adicionar método em destaque](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na página **Adicionar método**, selecione **Aplicativo autenticador** na lista suspensa e selecione **Adicionar**.

    ![Caixa Adicionar método, com o aplicativo Authenticator selecionado](media/security-info/securityinfo-myprofile-addauthapp.png)

4. Na página **Começar baixando o aplicativo**, selecione **Baixar agora** para baixar e instalar o aplicativo Microsoft Authenticator em seu dispositivo móvel e, em seguida, selecione **Avançar**.

    Para obter mais informações sobre como baixar e instalar o aplicativo, confira [Baixar e instalar o aplicativo Microsoft Authenticator](user-help-auth-app-download-install.md).

    ![Página Começar baixando o aplicativo](media/security-info/securityinfo-myprofile-getauthapp.png)

   > [!Note]
   > Se você não quiser usar o aplicativo Microsoft Authenticator, selecione o link **Desejo usar um aplicativo autenticador diferente**.
   >
   > Se a sua organização permitir que você escolha um método que não seja o aplicativo autenticador, você poderá selecionar o link **Desejo configurar um método diferente**.

5. Permaneça na página **Configurar sua conta** enquanto configura o aplicativo Microsoft Authenticator em seu dispositivo móvel.

    ![Página Configurar o aplicativo autenticador](media/security-info/securityinfo-myprofile-setupauthapp.png)

6. Abra o aplicativo Microsoft Authenticator, permita notificações (se solicitado), selecione **Adicionar conta** pelo ícone **Personalizar e controlar** no canto superior direito e, em seguida, selecione **Conta corporativa ou de estudante**.

    >[!Note]
    >Se esta for a primeira vez que está configurando o aplicativo Microsoft Authenticator, você poderá receber um prompt perguntando se deseja permitir que o aplicativo acesse sua câmera (iOS) ou que o aplicativo tire fotos e grave vídeo (Android). Você precisa selecionar **Permitir** para que o aplicativo autenticador possa acessar sua câmera para tirar uma foto do código QR na próxima etapa. Se não permitir acesso da câmera, você ainda poderá configurar o aplicativo autenticador, mas precisará adicionar as informações de código manualmente. Para obter informações sobre como adicionar o código manualmente, confira [Adicionar uma conta manualmente ao aplicativo](user-help-auth-app-add-account-manual.md).

7. Volte para a página **Configurar sua conta** em seu computador e selecione **Avançar**.

    A página **Digitalizar o código QR** será exibida.

    ![Digitalize o código QR usando o aplicativo Autenticador](media/security-info/securityinfo-myprofile-qrcodeauthapp.png)

8. Digitalize o código fornecido com o leitor de código QR do aplicativo Microsoft Authenticator, que aparecerá em seu dispositivo móvel depois que você criar sua conta corporativa ou de estudante na etapa 6.

    O aplicativo autenticador adicionará sua conta corporativa ou de estudante com sucesso, sem a necessidade de qualquer informação adicional sobre você. No entanto, se o leitor de código QR não puder ler o código, você poderá selecionar **Não é possível verificar o link do código QR** e inserir manualmente o código e o URL no aplicativo Microsoft Authenticator. Para obter mais informações sobre como adicionar um código manualmente, confira [Adicionar uma conta ao aplicativo manualmente](user-help-auth-app-add-account-manual.md).

9. Selecione **Avançar** na página **Digitalizar o código QR** em seu computador.

    Uma notificação será enviada ao aplicativo Microsoft Authenticator em seu dispositivo móvel, para testar a sua conta.

    ![Testar a sua conta com o aplicativo autenticador](media/security-info/securityinfo-myprofile-tryitauthapp.png)

10. Aprove a notificação no aplicativo Microsoft Authenticator e selecione **Avançar**.

     ![Notificação de êxito, conexão do aplicativo à conta](media/security-info/securityinfo-myprofile-successauthapp.png)

     Por padrão, suas informações de segurança são atualizadas para usar o aplicativo Microsoft Authenticator a fim de verificar sua identidade ao usar a verificação em duas etapas ou a redefinição de senha.

## <a name="delete-your-authenticator-app-from-your-security-info-methods"></a>Excluir seu aplicativo autenticador de seus métodos de informações de segurança

Se você não deseja mais usar seu aplicativo autenticador como um método de informações de segurança, é possível excluí-lo pela página **Informações de segurança**. Isso funciona para todos os aplicativos autenticadores, não apenas para o aplicativo Microsoft Authenticator. Depois de excluir o aplicativo, você precisará entrar no aplicativo autenticador em seu dispositivo móvel e excluir a conta.

>[!Important]
>Ao excluir um aplicativo autenticador por engano, não será possível desfazer essa ação. Você precisará adicionar o aplicativo autenticador novamente, seguindo as etapas da seção [Configurar o aplicativo autenticador](#set-up-the-microsoft-authenticator-app-from-the-security-info-page) deste artigo.

### <a name="to-delete-the-authenticator-app"></a>Excluir o aplicativo autenticador

1. Na página **Informações de segurança**, selecione o link **Excluir** ao lado do aplicativo autenticador.

    ![Link para excluir o aplicativo autenticador nas informações de segurança](media/security-info/securityinfo-myprofile-deleteauthapp.png)

2. Selecione **Sim** na caixa de confirmação para excluir o aplicativo autenticador. Depois que o aplicativo autenticador for excluído, ele será removido das suas informações de segurança e desaparecerá da página **Informações de segurança**. Se o aplicativo autenticador for o método padrão, o padrão será alterado para outro método disponível.

3. Abra o aplicativo autenticador em seu dispositivo móvel, selecione **Editar contas** e, em seguida, exclua sua conta corporativa ou de estudante do aplicativo autenticador.

    Sua conta será completamente excluída do aplicativo autenticador para solicitações de redefinição de senha e verificação em duas etapas.

## <a name="change-your-default-security-info-method"></a>Alterar o método de informações de segurança padrão

Se quiser que o aplicativo autenticador seja o método padrão para entrar na sua conta corporativa ou de estudante usando verificação em duas etapas ou para solicitações de redefinição de senha, você poderá defini-lo na página **Informações de segurança**.

### <a name="to-change-your-default-security-info-method"></a>Para alterar o método de informações de segurança padrão

1. Na página **Informações de segurança**, selecione o link **Alterar** ao lado das informações do **Método de entrada padrão**.

    ![Link Alterar do método de entrada padrão](media/security-info/securityinfo-myprofile-changedefaultauthapp.png)

2. Selecione **Microsoft Authenticator – notificação** na lista suspensa dos métodos disponíveis. Se não estiver usando o aplicativo Microsoft Authenticator, selecione a opção **Aplicativo autenticador ou token de hardware**.

    ![Selecione o método de entrada padrão](media/security-info/securityinfo-myprofile-defaultauthapp.png)

3. Selecione **Confirmar**.

    O método padrão usado para entrada é alterado para o aplicativo Microsoft Authenticator.

## <a name="additional-security-info-methods"></a>Métodos de informações de segurança adicionais

Você tem opções adicionais de como sua organização entra em contato com você para confirmar sua identidade, com base no que você está tentando fazer. As opções incluem:

- **Texto de dispositivo móvel.** Insira o número do seu dispositivo móvel e receba um texto com um código que você usará para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como verificar sua identidade com uma mensagem de texto (SMS), consulte [Configurar informações de segurança para usar mensagens de texto (SMS)](security-info-setup-text-msg.md).

- **Chamada de telefone do dispositivo móvel ou trabalho.** Insira seu número de dispositivos móveis e receber um telefonema para a redefinição de senha ou a verificação em duas etapas. Para obter instruções passo a passo sobre como verificar sua identidade com um número de telefone, consulte [configurar informações de segurança para usar chamadas telefônicas](security-info-setup-phone-number.md).

- **Chave de segurança.** Registre sua chave de segurança compatível com a Microsoft e use-a com um PIN para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como verificar sua identidade com uma chave de segurança, confira [Configurar informações de segurança para usar uma chave de segurança](security-info-setup-security-key.md).

- **Endereço de e-mail.** Digite seu endereço de e-mail do trabalho ou da escola para receber um e-mail para redefinir a senha. Essa opção não está disponível para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar seu e-mail, consulte [Configurar informações de segurança para usar o e-mail](security-info-setup-email.md).

- **Perguntas de segurança.** Responder a algumas perguntas de segurança criadas pelo seu administrador para sua organização. Essa opção só está disponível para a redefinição de senha e não para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar suas perguntas de segurança, consulte o artigo [Configurar informações de segurança para usar perguntas de segurança](security-info-setup-questions.md).

    >[!Note]
    >Se algumas dessas opções estiverem ausentes, é mais provável que sua organização não permita esses métodos. Se esse for o caso, você precisará escolher um método disponível ou entrar em contato com o administrador para obter mais ajuda.

## <a name="next-steps"></a>Próximas etapas

- Entre usando o aplicativo Microsoft Authenticator, seguindo as etapas no artigo [Entrar usando a verificação em duas etapas ou as informações de segurança](security-info-setup-signin.md).

- Redefina sua senha se você a perdeu ou esqueceu, do [portal de redefinição de senha](https://passwordreset.microsoftonline.com/) ou siga as etapas no artigo [Redefinir sua senha de trabalho ou escola](active-directory-passwords-update-your-own-password.md).

- Obter solução de problemas e ajuda para problemas de logon na [não pode entrar sua conta da Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artigo.
