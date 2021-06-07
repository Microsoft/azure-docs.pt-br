---
title: Configurar perguntas de segurança como seu método de verificação – Azure Active Directory
description: Como configurar sua página de informações de segurança (versão prévia) para verificar sua identidade usando perguntas de segurança predefinidas como seu método de verificação.
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
ms.openlocfilehash: 0c55f15d12ddc51573f59f56c742a8cac525af0b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "83744428"
---
# <a name="set-up-security-questions-as-your-verification-method"></a>Configurar perguntas de segurança como seu método de verificação

Você pode seguir estas etapas para adicionar seu método de redefinição de senha. Depois da primeira configuração, você pode retornar para a página **Informações de segurança** a fim de adicionar, atualizar ou excluir suas informações de segurança.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Caso não veja as perguntas de segurança, talvez sua organização não permita usar essa opção como método de redefinição de senha. Nesse caso, você precisará escolher outro método ou entrar em contato com o suporte técnico da sua organização para obter mais ajuda.

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

## <a name="set-up-your-security-questions-from-the-security-info-page"></a>Configurar suas perguntas de segurança na página de Informações de segurança

Dependendo das configurações da sua organização, você poderá escolher e responder a algumas perguntas de segurança como um de seus métodos de informações de segurança. Seu administrador configura o número de perguntas de segurança que você precisa escolher e responder.

Se você usar perguntas de segurança, é recomendável usá-las em conjunto com outro método. As perguntas de segurança podem ser menos seguras do que outros métodos porque algumas pessoas podem conhecer as respostas para suas perguntas.

> [!Note]
> As questões de segurança são armazenadas de maneira privada e segura em um objeto de usuário no diretório e só podem ser respondidas por você durante o registro. Não há como seu administrador ler ou modificar suas perguntas ou respostas.
>
> Se você não vir a opção de perguntas de segurança, é possível que sua organização não permita que você use questões de segurança para verificação. Se esse for o caso, você precisará escolher outro método ou entrar em contato com o administrador para obter mais ajuda.
>
> As contas de administrador não têm permissão para usar perguntas de segurança como método de redefinição de senha. Se você estiver conectado como uma conta de nível de administrador, essas opções não serão exibidas.

### <a name="to-set-up-your-security-questions"></a>Para configurar suas perguntas de segurança

1. Entre em sua conta corporativa ou de estudante e vá para a página https://myaccount.microsoft.com/.

    ![Página Meu Perfil, com links para Informações de segurança em destaque](media/security-info/securityinfo-myprofile-page.png)

2. Escolha **Informações de segurança** no painel de navegação esquerdo ou pelo link no bloco **Informações de segurança**. Em seguida, escolha **Adicionar método** na página **Informações de segurança**.

    ![Página Informações de segurança com a opção Adicionar método em destaque](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na página **Adicionar um método**, selecione **Perguntas de segurança** na lista suspensa e, em seguida, selecione **Adicionar**.

    ![Adicionar caixa de método com perguntas de segurança selecionadas](media/security-info/securityinfo-myprofile-addquestions.png)

4. Na página **Perguntas de segurança**, escolha e responda às suas perguntas de segurança e, em seguida, selecione **Salvar**.

    ![Adicionar número de telefone e escolher chamadas telefônicas](media/security-info/securityinfo-myprofile-securityquestions.png)

    Suas informações de segurança são atualizadas e você pode usar suas perguntas de segurança para verificar sua identidade ao usar a redefinição de senha.

## <a name="delete-security-questions-from-your-security-info-methods"></a>Excluir perguntas de segurança de seus métodos de informações de segurança

Se você não quiser mais usar suas perguntas de segurança como um método de informações de segurança, poderá removê-las da página **Informações de segurança**.

>[!Important]
>Se você excluir por engano suas perguntas de segurança, não haverá como desfazer isso. Você precisará adicionar o método novamente seguindo as etapas na seção [Configurar suas perguntas de segurança](#set-up-your-security-questions-from-the-security-info-page) deste artigo.

### <a name="to-delete-your-security-questions"></a>Para excluir suas perguntas de segurança

1. Na página **Informações de segurança**, selecione o link **Excluir** ao lado da opção **Perguntas de segurança**.

    ![Link para excluir o método de telefone das informações de segurança](media/security-info/securityinfo-myprofile-questionsdelete.png)

2. Selecione **Sim** da caixa de confirmação para excluir suas **perguntas de segurança**. Depois que suas perguntas de segurança terem sido excluídas, o método será removido das informações de segurança e desaparecerá da página **Informações de segurança**.

## <a name="additional-security-info-methods"></a>Métodos de informações de segurança adicionais

Você tem opções adicionais de como sua organização entra em contato com você para confirmar sua identidade, com base no que você está tentando fazer. As opções incluem:

- **Aplicativo autenticador.** Faça o download e use um aplicativo autenticador para receber uma notificação de aprovação ou um código de aprovação gerado aleatoriamente para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como configurar e usar o aplicativo Microsoft Authenticator, consulte [Configurar informações de segurança para usar um aplicativo autenticador](security-info-setup-auth-app.md).

- **Texto de dispositivo móvel.** Insira o número do seu dispositivo móvel e receba um texto com um código que você usará para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como verificar sua identidade com uma mensagem de texto (SMS), consulte [Configurar informações de segurança para usar mensagens de texto (SMS)](security-info-setup-text-msg.md).

- **Chamada de telefone do dispositivo móvel ou trabalho.** Insira seu número de dispositivos móveis e receber um telefonema para a redefinição de senha ou a verificação em duas etapas. Para obter instruções passo a passo sobre como verificar sua identidade com um número de telefone, consulte [configurar informações de segurança para usar chamadas telefônicas](security-info-setup-phone-number.md).

- **Chave de segurança.** Registre sua chave de segurança compatível com a Microsoft e use-a com um PIN para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como verificar sua identidade com uma chave de segurança, confira [Configurar informações de segurança para usar uma chave de segurança](security-info-setup-security-key.md).

- **Endereço de e-mail.** Digite seu endereço de e-mail do trabalho ou da escola para receber um e-mail para redefinir a senha. Essa opção não está disponível para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar seu e-mail, consulte [Configurar informações de segurança para usar o e-mail](security-info-setup-email.md).

    >[!Note]
    >Se algumas dessas opções estiverem ausentes, é mais provável que sua organização não permita esses métodos. Se esse for o caso, você precisará escolher um método disponível ou entrar em contato com o administrador para obter mais ajuda.

## <a name="next-steps"></a>Próximas etapas

- Redefina sua senha se você a perdeu ou esqueceu, do [portal de redefinição de senha](https://passwordreset.microsoftonline.com/) ou siga as etapas no artigo [Redefinir sua senha de trabalho ou escola](active-directory-passwords-update-your-own-password.md).

- Obter solução de problemas e ajuda para problemas de logon na [não pode entrar sua conta da Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artigo.
