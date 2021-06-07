---
title: Configurar seu número de telefone como o método de verificação – Azure Active Directory
description: Como configurar sua página de Informações de segurança (versão preliminar) para verificar a identidade usando seu número de telefone e dispositivo móvel como método de verificação.
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
ms.openlocfilehash: 7500b6b146a627dcdebacd9d2e2c7c61bc43c105
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "83744459"
---
# <a name="set-up-a-phone-number-as-your-verification-method"></a>Configurar um número de telefone como o método de verificação

Siga estas instruções para adicionar os métodos de verificação em duas etapas e redefinição de senha. Depois da primeira configuração, você pode retornar para a página **Informações de segurança** a fim de adicionar, atualizar ou excluir suas informações de segurança.

Se for solicitado que você faça tais configurações imediatamente após entrar em sua conta corporativa ou de estudante, confira as etapas detalhadas no artigo [Configurar suas informações de segurança pela solicitação na página de entrada](security-info-setup-signin.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

> [!Note]
> Informações de segurança não suportam o uso de extensões de telefone. Mesmo se você adicionar o formato adequado, +1 4255551234X12345, as extensões serão removidas antes da chamada ser feita.
>
> Caso não veja a opção de telefone, talvez sua organização não permita que você use essa opção para verificação. Nesse caso, você precisará escolher outro método ou entrar em contato com o suporte técnico da sua organização para obter mais assistência.

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

## <a name="set-up-phone-calls-from-the-security-info-page"></a>Configurar chamadas telefônicas da página de Informações de segurança

Dependendo das configurações da sua organização, você poderá usar chamadas telefônicas como um de seus métodos de informações de segurança.

>[!Note]
>Se desejar receber uma mensagem de texto em vez de uma chamada telefônica, siga as etapas do artigo [Configurar informações de segurança para usar mensagens de texto](security-info-setup-text-msg.md).

### <a name="to-set-up-phone-calls"></a>Para configurar chamadas telefônicas

1. Entre em sua conta corporativa ou de estudante e vá para a página https://myaccount.microsoft.com/.

    ![Página Meu Perfil, com links para Informações de segurança em destaque](media/security-info/securityinfo-myprofile-page.png)

2. Escolha **Informações de segurança** no painel de navegação esquerdo ou pelo link no bloco **Informações de segurança**. Em seguida, escolha **Adicionar método** na página **Informações de segurança**.

    ![Página Informações de segurança com a opção Adicionar método em destaque](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na página **Adicionar método**, selecione **Telefone** na lista suspensa e selecione **Adicionar**.

    ![Caixa Adicionar método, com Telefone selecionado](media/security-info/securityinfo-myprofile-addphonetext.png)

4. Na página **Telefone**, digite o número de telefone do seu dispositivo móvel, escolha **Telefonar para mim** e, em seguida, selecione **Avançar**.

    ![Adicionar número de telefone e escolher chamadas telefônicas](media/security-info/securityinfo-myprofile-phonecall-addnumber.png)

5. Atenda à chamada telefônica de verificação, enviada para o número de telefone inserido, e siga as instruções.

    A página muda para mostrar seu sucesso.

    ![Notificação de êxito, conectando o número de telefone, a opção de receber chamadas telefônicas e sua conta](media/security-info/securityinfo-myprofile-phonetext-success.png)

    Suas informações de segurança são atualizadas e você pode usar chamadas telefônicas para verificar sua identidade ao usar redefinição de senha ou verificação de duas etapas. Se você quiser tornar chamadas telefônicas seu método padrão, confira a seção [Alterar seu método de informações de segurança padrão](#change-your-default-security-info-method) deste artigo.

## <a name="delete-phone-calls-from-your-security-info-methods"></a>Excluir chamadas telefônicas de seus métodos de informações de segurança

Se você não quiser mais usar chamadas telefônicas como um método de informações de segurança, poderá removê-lo da página **Informações de segurança**.

>[!Important]
>Se você excluir por engano chamadas telefônicas, não haverá como desfazer isso. Você precisará adicionar o método novamente seguindo as etapas na seção [Configurar chamadas telefônicas](#set-up-phone-calls-from-the-security-info-page) deste artigo.

### <a name="to-delete-phone-calls"></a>Para excluir chamadas telefônicas

1. Na página **Informações de segurança**, selecione o link **Excluir** ao lado da opção **Telefone**.

    ![Link para excluir o método de telefone das informações de segurança](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. Selecione **Sim** na caixa de confirmação para excluir o número de **Telefone**. Depois que o número do telefone tiver sido excluído, ele será removido das suas informações de segurança e desaparecerá da página **Informações de segurança**. Se **Telefone** for o método padrão, o padrão será alterado para outro método disponível.

## <a name="change-your-default-security-info-method"></a>Alterar o método de informações de segurança padrão

Se quiser que chamadas telefônicas sejam o método padrão usando ao entrar na sua conta corporativa ou de estudante usando verificação em duas etapas ou para solicitações de redefinição de senha, poderá definir isso na página **Informações de segurança**.

### <a name="to-change-your-default-security-info-method"></a>Para alterar o método de informações de segurança padrão

1. Na página **Informações de segurança**, selecione o link **Alterar** ao lado das informações do **Método de entrada padrão**.

    ![Link Alterar do método de entrada padrão](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Selecione **Telefone – chamada (*_seu_número_de_telefone_*)** na lista suspensa de métodos disponíveis e, em seguida, selecione **Confirmar**.

    ![Selecione o método de entrada padrão](media/security-info/securityinfo-myprofile-phonecall-changeddefault.png)

    O método padrão usado para alterações de entrada para **Telefone – chamada (*_seu_número_de_telefone_*)** .

## <a name="additional-security-info-methods"></a>Métodos de informações de segurança adicionais

Você tem opções adicionais de como sua organização entra em contato com você para confirmar sua identidade, com base no que você está tentando fazer. As opções incluem:

- **Aplicativo autenticador.** Faça o download e use um aplicativo autenticador para receber uma notificação de aprovação ou um código de aprovação gerado aleatoriamente para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como configurar e usar o aplicativo Microsoft Authenticator, consulte [Configurar informações de segurança para usar um aplicativo autenticador](security-info-setup-auth-app.md).

- **Texto de dispositivo móvel.** Insira o número do seu dispositivo móvel e receba um texto com um código que você usará para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como verificar sua identidade com uma mensagem de texto (SMS), consulte [Configurar informações de segurança para usar mensagens de texto (SMS)](security-info-setup-text-msg.md).

- **Chave de segurança.** Registre sua chave de segurança compatível com a Microsoft e use-a com um PIN para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como verificar sua identidade com uma chave de segurança, confira [Configurar informações de segurança para usar uma chave de segurança](security-info-setup-security-key.md).

- **Endereço de e-mail.** Digite seu endereço de e-mail do trabalho ou da escola para receber um e-mail para redefinir a senha. Essa opção não está disponível para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar seu e-mail, consulte [Configurar informações de segurança para usar o e-mail](security-info-setup-email.md).

- **Perguntas de segurança.** Responder a algumas perguntas de segurança criadas pelo seu administrador para sua organização. Essa opção só está disponível para a redefinição de senha e não para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar suas perguntas de segurança, consulte o artigo [Configurar informações de segurança para usar perguntas de segurança](security-info-setup-questions.md).

    >[!Note]
    >Se algumas dessas opções estiverem ausentes, é mais provável que sua organização não permita esses métodos. Se esse for o caso, você precisará escolher um método disponível ou entrar em contato com o administrador para obter mais ajuda.

## <a name="next-steps"></a>Próximas etapas

- Redefina sua senha se você a perdeu ou esqueceu, do [portal de redefinição de senha](https://passwordreset.microsoftonline.com/) ou siga as etapas no artigo [Redefinir sua senha de trabalho ou escola](active-directory-passwords-update-your-own-password.md).

- Obter solução de problemas e ajuda para problemas de logon na [não pode entrar sua conta da Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artigo.
