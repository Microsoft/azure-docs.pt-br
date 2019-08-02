---
title: Configurar informações de segurança (versão prévia) para usar seu endereço de email - Azure Active Directory | Microsoft Docs
description: Como configurar suas informações de segurança para confirmar a identidade usando seu endereço de email.
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
ms.openlocfilehash: ec93574a8da051b5cf77a37af1030d0c22dfc4a9
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382906"
---
# <a name="set-up-security-info-preview-to-use-your-email-address"></a>Configurar informações de segurança (versão prévia) para usar seu endereço de email

Você pode seguir estas etapas para adicionar seu método de redefinição de senha. Depois da primeira configuração, você pode retornar para a página **Informações de segurança** a fim de adicionar, atualizar ou excluir suas informações de segurança.

Depois de configurar o método de redefinição de senha, também deverá configurar o método de verificação de dois fatores usando um [aplicativo autenticador](security-info-setup-auth-app.md), [mensagens de texto](security-info-setup-text-msg.md) ou uma [chamada telefônica](security-info-setup-phone-number.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="set-up-your-email-address-from-the-security-info-page"></a>Configurar o endereço de email na página Informações de segurança

Dependendo das configurações de sua organização, você poderá usar seu endereço de email como um dos métodos de informações de segurança.

>[!Note]
>Recomendamos o uso de um endereço de email que não exija sua senha de rede para fazer o acesso. Se você não vir a opção de e-mail, é possível que sua organização não permita que você use um e-mail para verificação. Se esse for o caso, você precisará escolher outro método ou entrar em contato com o administrador para obter mais ajuda.

### <a name="to-set-up-your-email-address"></a>Para configurar seu endereço de email

1. Entre em sua conta corporativa ou de estudante e vá para a página https://myprofile.microsoft.com/.

    ![Página Meu Perfil, com links para Informações de segurança em destaque](media/security-info/securityinfo-myprofile-page.png)

2. Escolha **Informações de segurança** no painel de navegação esquerdo ou pelo link no bloco **Informações de segurança**. Em seguida, escolha **Adicionar método** na página **Informações de segurança**.

    ![Página Informações de segurança com a opção Adicionar método em destaque](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na página **Adicionar método**, escolha **Email** na lista suspensa e escolha **Adicionar**.

    ![Caixa Adicionar método, com o email selecionado](media/security-info/securityinfo-myprofile-addemail.png)

4. Na página **Email**, digite seu endereço de email (por exemplo, alain@gmail.com) e escolha **Avançar**.

    ![Adicionar número de telefone e escolher chamadas telefônicas](media/security-info/securityinfo-myprofile-emailaddress.png)

    >[!Important]
    >Este endereço de e-mail não pode ser seu e-mail corporativo ou escolar.

5. Digite o código enviado para o endereço de email especificado e escolha **Avançar**.

    ![Adicionar número de telefone e escolher mensagens de texto](media/security-info/securityinfo-myprofile-emailcode.png)

    As informações de segurança são atualizadas e você pode usar seu endereço de email para verificar a identidade ao usar a redefinição de senha.

## <a name="delete-your-email-address-from-your-security-info-methods"></a>Excluir o endereço de email de seus métodos de informações de segurança

Se você não quer mais usar seu endereço de email como um método de informações de segurança, poderá excluí-lo na página **Informações de segurança**.

>[!Important]
>Se você excluir o endereço de email por engano, não será possível desfazer a ação. Você precisará adicionar o método novamente seguindo as etapas na seção [Configurar seu endereço de email](#set-up-your-email-address-from-the-security-info-page) deste artigo.

### <a name="to-delete-your-email-address"></a>Para excluir o endereço de email

1. Na página **Informações de segurança**, escolha o link **Excluir** ao lado da opção **Email**.

    ![Link para excluir o método de telefone das informações de segurança](media/security-info/securityinfo-myprofile-emaildelete.png)

2. Escolha **Sim** na caixa de confirmação para excluir a conta de **Email**. Depois que a conta de email for excluída, ela será removida das informações de segurança e desaparecerá da página **Informações de segurança**.

## <a name="additional-security-info-methods"></a>Métodos de informações de segurança adicionais

Você tem opções adicionais de como sua organização entra em contato com você para confirmar sua identidade, com base no que você está tentando fazer. As opções incluem:

- **Aplicativo autenticador.** Faça o download e use um aplicativo autenticador para receber uma notificação de aprovação ou um código de aprovação gerado aleatoriamente para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como configurar e usar o aplicativo Microsoft Authenticator, consulte [Configurar informações de segurança para usar um aplicativo autenticador](security-info-setup-auth-app.md).

- **Texto de dispositivo móvel.** Insira o número do seu dispositivo móvel e receba um texto com um código que você usará para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como verificar sua identidade com uma mensagem de texto (SMS), consulte [Configurar informações de segurança para usar mensagens de texto (SMS)](security-info-setup-text-msg.md).

- **Chamada de telefone do dispositivo móvel ou trabalho.** Insira seu número de dispositivos móveis e receber um telefonema para a redefinição de senha ou a verificação em duas etapas. Para obter instruções passo a passo sobre como verificar sua identidade com um número de telefone, consulte [configurar informações de segurança para usar chamadas telefônicas](security-info-setup-phone-number.md).

- **Chave de segurança.** Registre sua chave de segurança compatível com a Microsoft e use-a junto com um PIN para verificação em duas etapas ou redefinição de senha. Para obter instruções detalhadas sobre como verificar sua identidade com uma chave de segurança, consulte [configurar informações de segurança para usar uma chave de segurança](security-info-setup-security-key.md).

- **Perguntas de segurança.** Responder a algumas perguntas de segurança criadas pelo seu administrador para sua organização. Essa opção só está disponível para a redefinição de senha e não para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar suas perguntas de segurança, consulte o artigo [Configurar informações de segurança para usar perguntas de segurança](security-info-setup-questions.md).

    >[!Note]
    >Se algumas dessas opções estiverem ausentes, é mais provável que sua organização não permita esses métodos. Se esse for o caso, você precisará escolher um método disponível ou entrar em contato com o administrador para obter mais ajuda.

## <a name="next-steps"></a>Próximas etapas

- Redefina sua senha se você a perdeu ou esqueceu, do [portal de redefinição de senha](https://passwordreset.microsoftonline.com/) ou siga as etapas no artigo [Redefinir sua senha de trabalho ou escola](user-help-reset-password.md).

- Obter solução de problemas e ajuda para problemas de logon na [não pode entrar sua conta da Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artigo.
