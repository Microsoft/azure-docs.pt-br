---
title: Registrar informações de autenticação para redefinir sua própria senha -Microsoft Azure AD
description: Registre suas informações de método de verificação para redefinição de senha de autoatendimento do Microsoft Azure AD, para que você possa redefinir sua própria senha sem a ajuda do administrador.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 05/28/2020
ms.author: curtand
ms.openlocfilehash: e460f3fd383286b1e61a979b87a9dfa09f272313
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88799019"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>Registre suas informações do método de verificação para redefinir sua própria senha

Caso você tenha esquecido sua senha corporativa ou de estudante, nunca tenha recebido uma da empresa ou sua conta tenha sido bloqueada, você pode usar suas informações de segurança e o dispositivo móvel para redefinir sua senha corporativa ou de estudante.

Seu administrador deve ativar esse recurso para que você possa registrar suas informações e redefinir sua própria senha. Se você não vir a opção **Esqueci minha senha**, significa que seu administrador não ativou o recurso para sua organização. Se você acredita que isso esteja incorreto, entre em contato com o suporte técnico para obter ajuda.

>[!Important]
>Este artigo destina-se a usuários que estão tentando usar a inscrição para redefinição de senha de autoatendimento. Isso significa que você poderá redefinir sua própria senha corporativa ou de estudante (como alain@contoso.com ), sem exigir a ajuda do administrador. Se você for um administrador procurando informações sobre como ativar a redefinição de senha de autoatendimento para seus funcionários ou outros usuários, consulte [Implantar a redefinição de senha de autoatendimento do Microsoft Azure Active Directory e outros artigos](../authentication/howto-sspr-deployment.md).

## <a name="set-up-your-password-reset-verification-method"></a>Configurar o método de verificação de redefinição de senha

1. Abra o navegador da Web em seu dispositivo e vá para a [página de informações de segurança](https://account.activedirectory.windowsazure.com/PasswordReset/Register.aspx?regref=ssprsetup).

2. Dependendo de como o administrador configurou sua organização, uma ou mais das opções a seguir estarão disponíveis para você configurar como seu método de verificação de segurança. Se várias opções estiverem disponíveis, é altamente recomendável que você use mais de uma como seu método de verificação de segurança, caso um dos seus métodos se torne indisponível.

    - **Aplicativo de autenticação.** Opte por usar o aplicativo Microsoft Authenticator ou outro aplicativo autenticador como seu método de verificação de segurança. Para obter mais informações sobre como configurar o aplicativo, consulte [Configurar o aplicativo Microsoft Authenticator como seu método de verificação](security-info-setup-auth-app.md).

    - **Mensagens de texto.** Escolha enviar mensagens de texto para seu dispositivo móvel. Para obter mais informações sobre como configurar mensagens de texto, consulte [Configurar mensagens de texto como seu método de verificação](security-info-setup-text-msg.md).

    - **Chamadas telefônicas.** Opte por receber uma chamada telefônica para o número de telefone registrado. Para obter mais informações sobre a configuração de chamadas telefônicas, consulte [Configurar um número de telefone como seu método de verificação](security-info-setup-phone-number.md).

    - **Chave de segurança.** Escolha usar uma chave de segurança compatível com a Microsoft. Para obter mais informações, consulte [Configurar uma chave de segurança como seu método de verificação](security-info-setup-security-key.md).

    - **Endereço de e-mail.** Escolha usar um endereço de email alternativo que possa ser usado sem a necessidade de usar a sua senha esquecida ou ausente. Isso só funciona para redefinição de senha, não como um método de verificação de segurança. Para obter mais informações sobre a configuração de endereço de email, consulte [Configurar um endereço de email como seu método de verificação](security-info-setup-email.md).

    - **Perguntas de segurança.** Opte por configurar e responder a perguntas de segurança predefinidas configuradas pelo administrador. Isso só funciona para redefinição de senha, não como um método de verificação de segurança. Para obter mais informações sobre perguntas de segurança, consulte [Configurar perguntas de segurança como seu método de verificação](security-info-setup-questions.md).

3. Depois de selecionar e configurar seus métodos, escolha **Concluir** para concluir o processo.

    > [!Note]
    > As informações adicionadas ao seu número de telefone ou endereço de email não são compartilhadas com o diretório global da sua organização. As únicas pessoas que podem ver essas informações são você e seus administradores. Só você pode ver as respostas para suas perguntas de segurança.

## <a name="common-problems-and-their-solutions"></a>problemas comuns e suas soluções

 Aqui estão alguns casos de erro comuns e suas soluções:

| Mensagem de erro |  Solução possível |
| --- | --- | --- |
| Contate o administrador.<br>Detectamos que a senha da sua conta de usuário não é gerenciada pela Microsoft. Portanto, não podemos redefinir sua senha automaticamente.<br>Entre em contato com sua equipe de TI para obter assistência.| Se você receber essa mensagem de erro depois de digitar sua ID de usuário, isso significará que sua organização gerencia internamente a senha e não quer que você Redefina sua senha no link **Não é possível acessar sua conta**. Para redefinir a senha nessa situação, você deve entrar em contato com o suporte técnico da sua organização ou com o administrador para obter ajuda. |
| Sua conta não está habilitada para redefinição de senha.<br>A equipe de TI não configurou sua conta para usar esse serviço.<br>Se desejar, podemos pode contatar um administrador na sua organização para redefinir a senha para você. | Se você receber essa mensagem de erro depois de digitar a ID de usuário, significa que a organização não ativou o recurso de redefinição de senha ou você não tem permissão para usá-lo. Para redefinir sua senha, selecione o link **Contate um administrador**. Depois que você clicar no link, um email será enviado para o administrador ou assistência técnica da sua organização, informando que você deseja redefinir sua senha. |
| Não foi possível verificar sua conta.<br>Se desejar, podemos pode contatar um administrador na sua organização para redefinir a senha para você. | Se você receber essa mensagem de erro depois de digitar sua ID de usuário, significa que sua organização ativou a redefinição de senha e que você pode usá-la, mas que você ainda não se registrou para o serviço. Nessa situação, você deve entrar em contato com o suporte técnico da sua organização ou com o administrador para redefinir a senha. Para obter informações sobre como se registrar para a redefinição de senha depois de voltar ao seu dispositivo, consulte o processo acima neste artigo. |

## <a name="next-steps"></a>Próximas etapas

- [Alterar a senha usando a redefinição de senha de autoatendimento](active-directory-passwords-update-your-own-password.md)

- [Página de informações de segurança de segurança](https://mysignins.microsoft.com/security-info)

- [Portal de redefinição de senha:](https://passwordreset.microsoftonline.com/)

- [Quando você não consegue acessar a sua conta da Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)