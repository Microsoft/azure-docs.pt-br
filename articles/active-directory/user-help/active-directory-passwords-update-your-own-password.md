---
title: Redefinir sua senha usando informações de segurança - Active Directory do Azure | Microsoft Docs
description: Como redefinir sua própria senha se você esquecer, usando suas informações de segurança e a verificação em duas etapas.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 09/10/2020
ms.author: curtand
ms.openlocfilehash: c47d573dab3e72a956f6f0b9d09a1dafadf0225c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91827771"
---
# <a name="reset-your-work-or-school-password-using-security-info"></a>Redefinir sua senha corporativa ou de estudante usando informações de segurança

Caso você tenha esquecido sua senha corporativa ou de estudante, nunca tenha recebido uma da empresa ou sua conta tenha sido bloqueada, você pode usar suas informações de segurança e o dispositivo móvel para redefinir sua senha corporativa ou de estudante. Seu administrador deve ativar esse recurso para que você possa configurar suas informações e [redefinir sua senha](./active-directory-passwords-reset-register.md).

Se você souber sua senha e quiser alterá-la, consulte as seções [alterar as etapas de senha](#how-to-change-your-password) deste artigo.

>[!Important]
>Este artigo destina-se aos usuários que estão tentando redefinir uma senha de conta corporativa ou de estudante esquecida ou desconhecida. Se você for um administrador procurando informações sobre como ativar a redefinição de senha por autoatendimento para seus funcionários ou outros usuários, confira [Implantar a redefinição de senha por autoatendimento do Azure AD e outros artigos](../authentication/howto-sspr-deployment.md).

## <a name="how-to-reset-or-unlock-your-password-for-a-work-or-school-account"></a>Como redefinir ou desbloquear sua senha para uma conta corporativa ou de estudante

Se você não conseguir acessar sua conta do Azure AD (Azure Active Directory), talvez seja porque:

- Sua senha não está funcionando e você deseja redefini-la ou

- Você sabe sua senha, mas sua conta está bloqueada e você precisa desbloqueá-la.

### <a name="to-reset-your-password-and-get-back-into-your-account"></a>Para redefinir sua senha e voltar para sua conta

1. Na tela **Digitar senha**, selecione **Esqueceu minha senha**.

2. Na tela **Volte à sua conta**, digite seu trabalho ou escola **ID do usuário** (por exemplo, seu endereço de e-mail), prove que você não é um robô digitando os caracteres que vê na tela e selecione **Próximo**.

   ![Voltar à sua tela de conta](media/security-info/security-info-back-into-acct.png)

   >[!NOTE]
   >Se o administrador não tiver ativado a possibilidade de redefinir sua própria senha, você verá um link **Entre em contato com o administrador** em vez da tela **Voltar à sua conta**. Esse link permite que você entre em contato com o administrador para redefinir sua senha por meio de um e-mail ou de um portal da web.

3. Escolha um dos métodos a seguir para verificar sua identidade e alterar sua senha. Dependendo de como seu administrador configurou sua organização, talvez seja necessário passar por esse processo uma segunda vez, adicionando informações para uma segunda etapa de verificação.

    ![Volte para sua conta, etapa de verificação #1](media/security-info/security-info-back-into-acct2.png)

    >[!NOTE]
    >Dependendo de como o administrador configurou sua organização, algumas dessas opções de verificação podem não estar disponíveis. Você deve ter configurado anteriormente seu dispositivo móvel para verificação usando pelo menos um desses métodos.<br><br>Além disso, sua nova senha pode precisar atender a determinados requisitos de força. Senhas fortes normalmente têm de 8 a 16 caracteres, incluindo caracteres maiúsculos e minúsculos, pelo menos um número e pelo menos um caractere especial.

- **Redefina sua senha usando um endereço de e-mail.** Envia um e-mail para o endereço de e-mail que você configurou anteriormente na verificação em duas etapas ou nas informações de segurança. Se o administrador ativou a experiência de informações de segurança, você pode encontrar mais informações sobre a configuração de um endereço de e-mail no artigo [Configurar informações de segurança para usar o e-mail (visualização)](security-info-setup-email.md). Se você ainda não estiver usando as informações de segurança, poderá encontrar mais informações sobre como configurar um endereço de e-mail no artigo [Configurar minha conta para verificação em duas etapas](multi-factor-authentication-end-user-first-time.md). 

    1. Selecione **Enviar e-mail para meu e-mail alternativo** e, em seguida, selecione **E-mail**.

    2. Digite o código de verificação do e-mail na caixa e, em seguida, selecione **Próximo**.

    3. Digite e confirme sua nova senha e, em seguida, selecione **Finalizar**.

- **Redefina sua senha usando uma mensagem de texto.** Envia uma mensagem de texto para o número de telefone que você configurou anteriormente nas informações de segurança. Se o seu administrador tiver ativado a experiência de informações de segurança, você poderá encontrar mais informações sobre a configuração de mensagens de texto no artigo [Configurar informações de segurança para usar mensagens de texto (visualização)](security-info-setup-text-msg.md). Se você ainda não estiver usando as informações de segurança, poderá encontrar mais informações sobre a configuração de mensagens de texto no artigo [Configurar minha conta para verificação em duas etapas](multi-factor-authentication-end-user-first-time.md).

    1. Selecione **Texto meu celular**, digite seu número de telefone e, em seguida, selecione **Texto**.

    2. Digite o código de verificação da mensagem de texto na caixa e, em seguida, selecione **Próximo**.

    3. Digite e confirme sua nova senha e, em seguida, selecione **Finalizar**.

- **Redefina sua senha usando um número de telefone.** Faz uma chamada de voz automática para o número de telefone que você configurou anteriormente nas informações de segurança. Se o administrador ativou a experiência de informações de segurança, você pode encontrar mais informações sobre como configurar um número de telefone no artigo [Configurar informações de segurança para usar uma chamada telefônica (visualização)](security-info-setup-phone-number.md). Se você ainda não estiver usando as informações de segurança, poderá encontrar mais informações sobre como configurar um número de telefone no artigo [Configurar minha conta para verificação em duas etapas](multi-factor-authentication-end-user-first-time.md).

    1. Selecione **Ligar para meu celular**, digite seu número de telefone e, em seguida, selecione **Ligar**.

    2. Atenda a ligação e siga as instruções para confirmar sua identidade e, em seguida, selecione **Próximo**.

    3. Digite e confirme sua nova senha e, em seguida, selecione **Finalizar**.

- **Redefina sua senha usando perguntas de segurança.** Mostra a lista de perguntas de segurança que você configurou nas informações de segurança. Se o seu administrador tiver ativado a experiência de informações de segurança, você poderá encontrar mais informações sobre como configurar suas perguntas de segurança no artigo [Configurar informações de segurança para usar perguntas de segurança predefinidas (visualização)](security-info-setup-questions.md). Se você ainda não estiver usando as informações de segurança, poderá encontrar mais informações sobre como configurar perguntas de segurança no artigo [Configurar minha conta para verificação em duas etapas](multi-factor-authentication-end-user-first-time.md).

    1. Selecione **Responda às minhas perguntas de segurança**, responda às perguntas e selecione **Próximo**.

    2. Digite e confirme sua nova senha e, em seguida, selecione **Finalizar**.

- **Redefina sua senha usando uma notificação do seu aplicativo do autenticador.** Envia uma notificação de aprovação para o aplicativo autenticador. Se o administrador ativou a experiência de informações de segurança, você poderá encontrar mais informações sobre a configuração de um aplicativo do autenticador para enviar uma notificação no artigo [Configurar informações de segurança para usar um aplicativo de autenticação (visualização)](security-info-setup-auth-app.md). Se você ainda não estiver usando as informações de segurança, poderá encontrar mais informações sobre como configurar um aplicativo autenticador para enviar uma notificação no artigo [Configurar minha conta para verificação em duas etapas](multi-factor-authentication-end-user-first-time.md).

    1. Selecione **Aprovar uma notificação no meu aplicativo do autenticador** e, em seguida, selecione **Enviar notificação**.

    2. Aprove o login do seu aplicativo autenticador.

    3. Digite e confirme sua nova senha e, em seguida, selecione **Finalizar**.

- **Redefina sua senha usando um código do seu aplicativo do autenticador.** Aceita um código aleatório fornecido pelo seu aplicativo de autenticação. Se o administrador ativou a experiência de informações de segurança, você pode encontrar mais informações sobre a configuração de um aplicativo do autenticador para fornecer um código no artigo [Configurar informações de segurança para usar um aplicativo de autenticação (visualização)](security-info-setup-auth-app.md). Se você ainda não estiver usando as informações de segurança, poderá encontrar mais informações sobre como configurar um aplicativo do autenticador para fornecer um código no artigo [Configurar minha conta para verificação em duas etapas](multi-factor-authentication-end-user-first-time.md).

  1. Selecione **Insira um código do meu aplicativo autenticador** e selecione **Enviar notificação**.

  2. Abra o aplicativo do autenticador, digite o código de verificação da sua conta na caixa e, em seguida, selecione **Próximo**.

  3. Digite e confirme sua nova senha e, em seguida, selecione **Finalizar**.

  4. Depois de receber a mensagem informando que sua senha foi redefinida, você poderá fazer login na sua conta usando sua nova senha.

     Se você ainda não conseguir acessar sua conta, entre em contato com o administrador da organização para obter mais ajuda.

Depois de redefinir sua senha, você pode receber um email de confirmação proveniente de uma conta como "Microsoft em nome de \<*your_organization*> ". Se você receber um e-mail semelhante, mas não redefiniu sua senha recentemente, entre em contato com o administrador da organização imediatamente.

## <a name="how-to-change-your-password"></a>Como alterar sua senha

Se você quiser apenas alterar sua senha, poderá fazê-lo por meio do portal do Office 365, do portal meus aplicativos ou da página de entrada do Windows 10.

### <a name="to-change-your-password-using-the-office-365-portal"></a>Para alterar sua senha usando o portal do Office 365

Use esse método se você normalmente acessa seus aplicativos pelo portal do Office:

1. Faça login na sua [conta do Office 365](https://portal.office.com), usando sua senha existente.

2. Selecione seu perfil no lado superior direito e selecione **Exibir conta**.

3. Selecione **Segurança e privacidade** > **Senha**.

4. Digite sua senha antiga, crie e confirme sua nova senha e, em seguida, selecione **Enviar**.

### <a name="to-change-your-password-from-the-my-apps-portal"></a>Para alterar sua senha do portal meus aplicativos

Use esse método se você normalmente acessa seus aplicativos no portal meus aplicativos:

1. Entre no [portal meus aplicativos](https://myapps.microsoft.com/), usando sua senha existente.

2. Selecione seu perfil no lado superior direito e selecione **Perfil**.

3. Selecione **Alterar senha**.

4. Digite sua senha antiga, crie e confirme sua nova senha e, em seguida, selecione **Enviar**.

### <a name="to-change-your-password-at-windows-sign-in"></a>Para alterar sua senha ao entrar no Windows

Se o administrador ativou a funcionalidade, você pode ver um link para **Redefinir senha** na tela de login do Windows 7, Windows 8, Windows 8.1 ou Windows 10.

1. Selecione o link **Redefinir senha** para iniciar o processo de redefinição de senha sem precisar usar a experiência normal baseada na Web.

2. Confirme a ID de usuário e selecione **Avançar**.

3. Selecione e confirme um método de contato para a verificação. Se necessário, escolha uma segunda opção de verificação diferente da anterior, preenchendo as informações necessárias.

4. Na página **Criar uma nova senha**, digite e confirme sua nova senha e, em seguida, selecione **Próximo**.

    Senhas fortes normalmente têm de 8 a 16 caracteres, incluindo caracteres maiúsculos e minúsculos, pelo menos um número e pelo menos um caractere especial.

5. Depois de receber a mensagem informando que sua senha foi redefinida, você pode selecionar **Concluir**.

    Se você ainda não conseguir acessar sua conta, entre em contato com o administrador da organização para obter mais ajuda.

## <a name="common-problems-and-their-solutions"></a>problemas comuns e suas soluções

Aqui estão alguns casos de erro comuns e suas soluções:

|Problema|Descrição|Solução|
| --- | --- | --- |
|Quando tento alterar minha senha, recebo um erro. |Sua senha tem uma palavra, frase ou padrão que torna sua senha facilmente adivinhada.| Tente novamente usando uma senha mais forte.|
|Depois de inserir minha ID de usuário, vou para uma página que diz "Entre em contato com seu administrador".|A Microsoft determinou que a senha da sua conta de usuário é gerenciada pelo seu administrador em um ambiente local. Como resultado, você não pode redefinir sua senha no link "Não consigo acessar sua conta". |Entre em contato com seu administrador para mais ajuda.|
|Depois de inserir minha ID de usuário, recebo um erro que diz "Sua conta não está habilitada para redefinição de senha".|Seu administrador não configurou sua conta para que você possa redefinir sua própria senha.|Seu administrador não ativou a redefinição de senha da sua organização no link "Não foi possível acessar sua conta" ou não o autorizou a usar o recurso.<br><br> Para redefinir sua senha, você deve selecionar o link "contatar um administrador" para enviar um e-mail ao administrador da sua empresa e informá-lo que deseja redefinir sua senha.|
|Depois de inserir minha ID de usuário, recebo um erro que diz "Não foi possível confirmar sua conta".|O processo de login não conseguiu confirmar as informações da sua conta.|Existem duas razões pelas quais você pode estar vendo esta mensagem.<br><br>1. Seu administrador ativou a redefinição de senha para sua organização, mas você não se registrou para usar o serviço. Para se registrar para redefinição de senha, consulte um dos artigos a seguir, com base em seu método de verificação: [Configurar informações de segurança para usar um aplicativo autenticador (versão prévia)](security-info-setup-auth-app.md), [Configurar informações de segurança para usar uma ligação (versão prévia)](security-info-setup-phone-number.md), [Configurar informações de segurança para usar mensagens de texto (versão prévia)](security-info-setup-text-msg.md), [Configurar informações de segurança para usar o email (versão prévia)](security-info-setup-email.md) ou [Configurar informações de segurança para usar perguntas de segurança (versão prévia)](security-info-setup-questions.md).<br><br>2. Seu administrador não ativou a redefinição de senha para sua organização. Nessa situação, você deve selecionar o "contatar um link de administrador" para enviar um e-mail ao seu administrador, pedindo para redefinir sua senha.|

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as informações de segurança no artigo [Visão geral da informação de segurança (visualização)](./security-info-setup-signin.md).

- Se você estiver tentando voltar a uma conta pessoal como Xbox, hotmail.com ou outlook.com, experimente as sugestões no artigo [Quando não for possível entrar em sua conta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
