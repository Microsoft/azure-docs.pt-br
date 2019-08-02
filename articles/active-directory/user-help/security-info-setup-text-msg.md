---
title: Configurar informações de segurança (versão prévia) para usar mensagens de texto – Azure Active Directory | Microsoft Docs
description: Como configurar suas informações de segurança para verificar sua identidade usando mensagens de texto e seu dispositivo móvel.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c1c6aadcf8fb247a7c2a53cef7d1ed0c85cf374
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382839"
---
# <a name="set-up-security-info-preview-to-use-text-messaging"></a>Configurar informações de segurança (versão prévia) para usar mensagens de texto

Siga estas instruções para adicionar os métodos de verificação em duas etapas e redefinição de senha. Depois da primeira configuração, você pode retornar para a página **Informações de segurança** para adicionar, atualizar ou excluir suas informações de segurança.

Se for solicitado que você faça tais configurações imediatamente após entrar em sua conta corporativa ou de estudante, confira as etapas detalhadas no artigo [Configurar suas informações de segurança pela solicitação na página de entrada](security-info-setup-signin.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Caso não veja a opção de telefone, talvez sua organização não permita que você use um número de telefone para verificação. Nesse caso, você precisará escolher outro método ou entrar em contato com o administrador para obter ajuda.

## <a name="set-up-text-messages-from-the-security-info-page"></a>Configurar mensagens de texto usando a página de Informações de segurança

Dependendo das configurações da sua organização, você poderá usar mensagens de texto como um de seus métodos de informações de segurança. A opção de mensagem de texto faz parte da opção de telefone, portanto, você configurará tudo da mesma maneira que faria para o seu número de telefone, mas, em vez de fazer com que a Microsoft ligue para você, optará por usar uma mensagem de texto.

>[!Note]
>Se você deseja receber uma chamada telefônica em vez de uma mensagem de texto, siga as etapas no artigo [Configurar informações de segurança para usar chamadas telefônicas](security-info-setup-phone-number.md).

### <a name="to-set-up-text-messages"></a>Para configurar mensagens de texto

1. Entre em sua conta corporativa ou de estudante e vá para a página https://myprofile.microsoft.com/.

    ![Página Meu Perfil, com links para Informações de segurança em destaque](media/security-info/securityinfo-myprofile-page.png)

2. Escolha **Informações de segurança** no painel de navegação esquerdo ou pelo link no bloco **Informações de segurança**. Em seguida, escolha **Adicionar método** na página **Informações de segurança**.

    ![Página Informações de segurança com a opção Adicionar método em destaque](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na página **Adicionar método**, selecione **Telefone** na lista suspensa e selecione **Adicionar**.

    ![Caixa Adicionar método, com Telefone selecionado](media/security-info/securityinfo-myprofile-addphonetext.png)

4. Na página **Telefone**, digite o número de telefone do seu dispositivo móvel, escolha **Enviar-me um código por texto** e, em seguida, selecione **Avançar**.

    ![Adicionar número de telefone e escolher mensagens de texto](media/security-info/securityinfo-myprofile-phonetext-addnumber.png)

5. Digite o código enviado a você por meio de mensagem de texto para seu dispositivo móvel e, em seguida, selecione **Avançar**.

    ![Adicionar número de telefone e escolher mensagens de texto](media/security-info/securityinfo-myprofile-phonetext-entercode.png)

    A página muda para mostrar seu sucesso.

    ![Notificação de êxito, conectando o número de telefone, a escolha para receber mensagens de texto e sua conta](media/security-info/securityinfo-myprofile-phonetext-success.png)

    Suas informações de segurança são atualizadas e você pode usar mensagens de texto para verificar sua identidade ao usar redefinição de senha ou verificação de duas etapas. Se você quiser tornar mensagens de texto seu método padrão, confira a seção [Alterar seu método de informações de segurança padrão](#change-your-default-security-info-method) deste artigo.

## <a name="delete-text-messaging-from-your-security-info-methods"></a>Excluir mensagens de texto de seus métodos de informações de segurança

Se você não quiser mais usar mensagens de texto como um método de informações de segurança, poderá removê-lo da página **Informações de segurança**.

>[!Important]
>Se você excluir mensagens de texto por engano, não haverá como desfazer isso. Você precisará adicionar o método novamente seguindo as etapas na seção [Configurar mensagens de texto](#set-up-text-messages-from-the-security-info-page) deste artigo.

### <a name="to-delete-text-messaging"></a>Para excluir mensagens de texto

1. Na página **Informações de segurança**, selecione o link **Excluir** ao lado da opção **Telefone**.

    ![Link para excluir o método de mensagens de texto e telefone das informações de segurança](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. Selecione **Sim** na caixa de confirmação para excluir o número de **Telefone**. Depois que o número do telefone tiver sido excluído, ele será removido das suas informações de segurança e desaparecerá da página **Informações de segurança**. Se **Telefone** for o método padrão, o padrão será alterado para outro método disponível.

## <a name="change-your-default-security-info-method"></a>Alterar o método de informações de segurança padrão

Se você quiser que o sistema de mensagens de texto seja o método padrão usado ao entrar em sua conta corporativa ou de estudante usando a verificação de dois fatores ou para solicitações de redefinição de senha, você poderá defini-lo na página **informações de segurança** .

### <a name="to-change-your-default-security-info-method"></a>Para alterar o método de informações de segurança padrão

1. Na página **Informações de segurança**, selecione o link **Alterar** ao lado das informações do **Método de entrada padrão**.

    ![Link Alterar do método de entrada padrão](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Selecione **Telefone – texto ( *_seu_número_de_telefone_* )** na lista suspensa de métodos disponíveis e, em seguida, selecione **Confirmar**.

    ![Selecione o método de entrada padrão](media/security-info/securityinfo-myprofile-phonetext-changeddefault.png)

    O método padrão usado para alterações de entrada para **Telefone – texto ( *_seu_número_de_telefone_* )** .

## <a name="additional-security-info-methods"></a>Métodos de informações de segurança adicionais

Você tem opções adicionais de como sua organização entra em contato com você para confirmar sua identidade, com base no que você está tentando fazer. As opções incluem:

- **Aplicativo autenticador.** Faça o download e use um aplicativo autenticador para receber uma notificação de aprovação ou um código de aprovação gerado aleatoriamente para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como configurar e usar o aplicativo Microsoft Authenticator, consulte [Configurar informações de segurança para usar um aplicativo autenticador](security-info-setup-auth-app.md).

- **Dispositivo móvel ou chamada telefônica de trabalho.** Insira seu número de dispositivos móveis e receber um telefonema para a redefinição de senha ou a verificação em duas etapas. Para obter instruções passo a passo sobre como verificar sua identidade com um número de telefone, consulte [configurar informações de segurança para usar chamadas telefônicas](security-info-setup-phone-number.md).

- **Chave de segurança.** Registre sua chave de segurança compatível com a Microsoft e use-a junto com um PIN para verificação em duas etapas ou redefinição de senha. Para obter instruções detalhadas sobre como verificar sua identidade com uma chave de segurança, consulte [configurar informações de segurança para usar uma chave de segurança](security-info-setup-security-key.md).

- **Endereço de e-mail.** Digite seu endereço de e-mail do trabalho ou da escola para receber um e-mail para redefinir a senha. Essa opção não está disponível para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar seu e-mail, consulte [Configurar informações de segurança para usar o e-mail](security-info-setup-email.md).

- **Perguntas de segurança.** Responder a algumas perguntas de segurança criadas pelo seu administrador para sua organização. Essa opção só está disponível para a redefinição de senha e não para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar suas perguntas de segurança, consulte o artigo [Configurar informações de segurança para usar perguntas de segurança](security-info-setup-questions.md).

    >[!Note]
    >Se algumas dessas opções estiverem ausentes, é mais provável que sua organização não permita esses métodos. Se esse for o caso, você precisará escolher um método disponível ou entrar em contato com o administrador para obter mais ajuda.

## <a name="next-steps"></a>Próximas etapas

- Redefina sua senha se você a perdeu ou esqueceu, do [portal de redefinição de senha](https://passwordreset.microsoftonline.com/) ou siga as etapas no artigo [Redefinir sua senha de trabalho ou escola](user-help-reset-password.md).

- Obter solução de problemas e ajuda para problemas de logon na [não pode entrar sua conta da Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artigo.
