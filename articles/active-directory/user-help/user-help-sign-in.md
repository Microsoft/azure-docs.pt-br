---
title: Entrar com a verificação em duas etapas ou informações de segurança-Azure Active Directory | Microsoft Docs
description: Saiba mais sobre como fazer login usando os vários métodos de verificação de identidade em informações de segurança.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: lizross
ms.custom: user-help
ms.collection: M365-identity-device-management
ms.openlocfilehash: b20ea1131ceda0527ed35d1a1082d05f25da6bac
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382321"
---
# <a name="sign-in-using-two-step-verification-or-security-info"></a>Faça login usando a verificação em duas etapas ou informações de segurança

Depois de configurar a verificação em duas etapas ou as informações de segurança, você poderá entrar em sua conta usando o método de autenticação especificado.

> [!Note]
> Se você ainda estiver usando a experiência da verificação em duas etapas, precisará configurar seus métodos de autenticação seguindo as instruções no artigo [Configurar minha conta para verificação em duas etapas](multi-factor-authentication-end-user-first-time.md).
>
> Se o administrador ativou a experiência de informações de segurança, você precisará definir seus métodos de autenticação usando estes artigos passo a passo:<ul><li>[Configurar informações de segurança para usar um aplicativo de autenticação](security-info-setup-auth-app.md)</li><li>[Configure informações de segurança para usar mensagens de texto](security-info-setup-text-msg.md)</li><li>[Configure informações de segurança para usar uma chamada telefônica](security-info-setup-phone-number.md)</li><li>[Configurar informações de segurança para usar uma chave de segurança](security-info-setup-security-key.md)</li></ul>

## <a name="sign-in-using-an-authenticator-app-notification-on-your-mobile-device"></a>Faça login usando uma notificação de aplicativo do autenticador no seu dispositivo móvel

1. Entre em sua conta com seu nome de usuário e a senha.

2. Selecione **Aprovar** na notificação de aprovação enviada para seu dispositivo móvel.

## <a name="sign-in-using-an-authenticator-app-code-on-your-mobile-device"></a>Faça login usando um código de aplicativo do autenticador no seu dispositivo móvel

1. Entre em sua conta com seu nome de usuário e a senha.

2. Abra seu aplicativo autenticador e digite o código gerado aleatoriamente para sua conta na caixa **Inserir código**.

## <a name="sign-in-using-your-phone-number"></a>Conecte-se usando seu número de telefone

1. Entre em sua conta com seu nome de usuário e a senha.

2. Atenda seu telefone e siga as instruções.

## <a name="sign-in-using-a-text-message"></a>Entre usando uma mensagem de texto

1. Entre em sua conta com seu nome de usuário e a senha.

2. Abra a mensagem de texto e digite o código da sua mensagem de texto na caixa **Digitar código**.

## <a name="sign-in-using-a-security-key-at-the-lock-screen"></a>Entrar usando uma chave de segurança na tela de bloqueio

1. Depois de registrar sua chave de segurança, selecione a imagem da chave de segurança na tela de bloqueio do Windows 10.

2. Insira sua chave de segurança na porta USB do dispositivo e entre no Windows usando o PIN de chave de segurança.

    ![Entrada de chave de segurança na tela de bloqueio do Windows 10](./media/security-info/security-info-windows-10-lock-screen-security-key.png)

## <a name="sign-in-using-a-security-key-and-the-microsoft-edge-browser"></a>Entre usando uma chave de segurança e o navegador Microsoft Edge

1. Depois de registrar sua chave de segurança, abra o navegador Microsoft Edge.

2. Quando solicitado a entrar, insira sua chave de segurança na porta USB do dispositivo e entre no Windows usando o PIN de chave de segurança.

    ![Entrada de chave de segurança usando o navegador Microsoft Edge](./media/security-info/security-info-edge-security-key.png)

    >[!NOTE]
    >Para obter informações sobre como entrar usando o aplicativo Microsoft Authenticator, consulte o artigo, [entrar em suas contas usando o aplicativo Microsoft Authenticator](user-help-auth-app-sign-in.md).

## <a name="sign-in-using-another-verification-method"></a>Conecte-se usando outro método de verificação

Se, por alguma razão, você não conseguir usar seu método de entrada primário, poderá usar outro método de verificação configurado anteriormente.

1. Entre em sua conta normalmente e, em seguida, escolha o link **entrar de outra maneira** na página de **verificação em duas etapas** .

    ![Alterar o método de verificação de entrada](media/security-info/two-factor-auth-signin-another-way.png)

    >[!Note]
    >Se você não vir o link **entrar de outra maneira** , isso significa que você não configurou nenhum outro método de verificação e que precisará entrar em contato com o administrador para obter ajuda para entrar em sua conta. Depois que o administrador ajuda você a entrar, certifique-se de adicionar métodos de verificação adicionais. Para obter mais informações sobre como adicionar métodos de verificação, consulte o artigo [gerenciar as configurações de verificação em duas etapas](multi-factor-authentication-end-user-manage-settings.md).
    >
    >Se você vir o link **entrar de outra maneira** , mas ainda não vir nenhum outro método de verificação, será necessário entrar em contato com o administrador para obter ajuda para entrar na sua conta.

2. Escolha seu método de verificação alternativo e continuar o processo de verificação em duas etapas.

3. Depois que você está em sua conta, você pode atualizar seus métodos de verificação (se necessário). Para obter mais informações sobre como adicionar ou mudar métodos, consulte o artigo [gerenciar as configurações de verificação em duas etapas](multi-factor-authentication-end-user-manage-settings.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as informações de segurança no artigo [Visão geral da informação de segurança (visualização)](user-help-security-info-overview.md).

- Saiba mais sobre a verificação em duas etapas no artigo [Visão geral da verificação em duas etapas](user-help-two-step-verification-overview.md).

- Redefina sua senha se você a perdeu ou esqueceu, do [portal de redefinição de senha](https://passwordreset.microsoftonline.com/)

- Obtenha dicas de solução de problemas e ajude a problemas de login no artigo [Não é possível fazer login em sua conta da Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
