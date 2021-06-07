---
title: Adicionar uma conta corporativa ou de estudante ao aplicativo Microsoft Authenticator - Azure Active Directory
description: Adicione sua conta corporativa ou de estudante ao aplicativo Microsoft Authenticator para verificar sua identidade ao usar a verificação de dois fatores.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 04c9bc429d9663f7ac36b6ba8f40abf225eb71c6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359091"
---
# <a name="add-your-work-or-school-account-to-the-microsoft-authenticator-app"></a>Adicionar sua conta corporativa ou de estudante ao aplicativo Microsoft Authenticator

Se a organização usa a verificação de dois fatores, você pode configurar sua conta corporativa ou de estudante para usar o aplicativo Microsoft Authenticator como um dos métodos de verificação.

>[!Important]
>Para poder adicionar sua conta, você deverá baixar e instalar o aplicativo Microsoft Authenticator. Se você ainda não tiver feito isso, siga as etapas no artigo [Baixar e instalar o aplicativo](user-help-auth-app-download-install.md).

## <a name="add-your-work-or-school-account"></a>Adicionar a conta corporativa ou de estudante

Você pode adicionar sua conta corporativa ou de estudante ao aplicativo Microsoft Authenticator seguindo um destes procedimentos:

- Entre com sua conta corporativa ou de estudante credenciais (versão prévia)
- Digitalizar um código QR

### <a name="sign-in-with-your-credentials"></a>Entre usando as suas credenciais

>[!Note]
>Esse recurso é utilizável somente por usuários cujos administradores tenham habilitado a entrada pelo telefone usando o aplicativo autenticador para eles.

Para adicionar uma conta entrando em sua conta corporativa ou de estudante usando suas credenciais:

1. Abra o aplicativo Microsoft Authenticator e selecione para o **+** botão e toque em **adicionar conta corporativa ou de estudante**. Selecione **Entrar**.

1. Insira suas credenciais de conta corporativa ou de estudante. Se você tiver uma passagem de acesso temporária (toque), poderá usá-la para entrar. Neste ponto, você poderia ser impedido de prosseguir por uma das seguintes condições:

   - Se você não tiver métodos de autenticação suficientes em sua conta para obter um token de autenticação forte, não poderá continuar a adicionar uma conta.

   - Se você receber a mensagem `You might be signing in from a location that is restricted by your admin` , você será bloqueado e precisará de um administrador para desbloqueá-lo em [informações de segurança](https://mysignins.microsoft.com/security-info).

   - Se você não estiver bloqueado para a entrada pelo telefone usando o aplicativo autenticador pelo administrador, poderá passar pelo registro do dispositivo para configurar a entrada por telefone sem senha e a MFA (autenticação multifator do Azure).

1. Neste ponto, você pode ser solicitado a verificar um código QR fornecido pela sua organização para configurar uma conta de autenticação multifator local no aplicativo. Você será obrigado a fazer isso somente se sua organização usar o servidor MFA local.

1. Em seu dispositivo, toque na conta e verifique na exibição de tela inteira que sua conta está correta e que há um código de verificação de seis dígitos associado. Para segurança adicional, o código de verificação muda a cada 30 segundos, evitando que alguém use o mesmo código várias vezes.

## <a name="sign-in-with-a-qr-code"></a>Entrar com um código QR

Para adicionar uma conta examinando um código QR, faça o seguinte:

1. Em seu computador, vá para a página **Verificação de segurança adicional**.

   >[!Note]
   >Caso não veja a página **Verificação de segurança adicional**, é possível que o administrador tenha ativado a experiência de informações de segurança (versão prévia). Se esse for o caso, você deverá seguir as instruções da seção [Configurar informações de segurança para usar um aplicativo Authenticator](security-info-setup-auth-app.md). Se não for o caso, você precisará entrar em contato com o suporte técnico da sua organização para obter assistência. Para obter mais informações sobre informações de segurança, consulte [configurar suas informações de segurança de um prompt de entrada](security-info-setup-signin.md).

1. Marque a caixa de seleção ao lado de aplicativo autenticador e, em seguida, selecione **Configurar**. A página **Configure o aplicativo móvel** é exibida.

   ![Tela que fornece um código QR](./media/user-help-auth-app-add-work-school-account/auth-app-barcode.png)

1. Abra o aplicativo Microsoft Authenticator, selecione o ícone de adição, ![ Selecione o ícone de adição em dispositivos IOS ou Android ](media/user-help-auth-app-add-work-school-account/plus-icon.png) e selecione **adicionar conta** e, em seguida, selecione **conta corporativa ou de estudante,** seguida por **digitalizar um código QR**.
   Se você não tiver uma conta configurada no aplicativo autenticador, verá um botão azul grande que diz **adicionar conta**.

Se você não for solicitado a usar a câmera para digitalizar um código QR, nas configurações do seu telefone, verifique se o aplicativo autenticador tem acesso à câmera do telefone.

## <a name="next-steps"></a>Próximas etapas

- Depois de adicionar as contas ao aplicativo, você poderá entrar usando o aplicativo Authenticator em seu dispositivo. Para obter mais informações, confira [Entrar usando o aplicativo](user-help-auth-app-sign-in.md).

- Para dispositivos que executam o iOS, você também pode fazer backup das credenciais da sua conta e das configurações de aplicativos relacionadas, tais como a ordem de suas contas, na nuvem. Para obter mais informações, confira [Fazer backup e recuperar informações com o aplicativo Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
