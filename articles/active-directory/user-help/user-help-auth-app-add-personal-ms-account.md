---
title: Adicione suas contas Microsoft pessoais ao aplicativo Microsoft Authenticator – Azure Active Directory | Microsoft Docs
description: Como fazer para adicionar suas contas Microsoft pessoais, tais como para o Outlook.com ou o Xbox LIVE, ao aplicativo Microsoft Authenticator para verificação de dois fatores.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: 502407bbf1cdb47fc8105ed0220babcd9f292faa
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382703"
---
# <a name="add-your-personal-microsoft-accounts"></a>Adicionar suas contas Microsoft pessoais

Adicione suas contas Microsoft pessoais, tais como para o Outlook.com ou o Xbox LIVE, ao aplicativo Microsoft Authenticator para verificação de dois fatores e o método de entrada por celular sem senha.

- **Método de verificação padrão de dois fatores.** Digite seu nome de usuário e senha no dispositivo no qual você está fazendo logon e, em seguida, escolha se o aplicativo Microsoft Authenticator envia uma notificação ou se você prefere copiar o código de verificação associado da tela **Contas** do aplicativo Microsoft Authenticator.

- **Método de entrada sem senha.** Digite seu nome de usuário no dispositivo no qual você está fazendo logon para sua conta Microsoft pessoal, depois use seu dispositivo móvel para verificar sua identidade usando sua impressão digital, rosto ou PIN. Para esse método, você não precisa inserir sua senha.

>[!Important]
>Antes de poder adicionar sua conta, você deverá baixar e instalar o aplicativo Microsoft Authenticator. Se você ainda não tiver feito isso, siga as etapas no artigo [Baixar e instalar o aplicativo](user-help-auth-app-download-install.md).

## <a name="add-your-personal-microsoft-account"></a>Adicionar sua conta Microsoft pessoal

Você pode adicionar sua conta Microsoft pessoal ativando primeiro a verificação de dois fatores e, em seguida, adicionando a conta ao aplicativo.

>[!Note]
>Se você planeja usar apenas a entrada sem senha por celular para sua conta Microsoft pessoal, você não precisa ativar a verificação de dois fatores. No entanto, é recomendável que você a ative para oferecer segurança adicional à conta.

### <a name="turn-on-two-factor-verification"></a>Ativar a verificação de dois fatores

1. No seu computador, vá para a página [Noções básicas sobre segurança](https://account.microsoft.com/security) e entre usando sua conta Microsoft pessoal. Por exemplo, alain@outlook.com.

2. Na parte inferior da página **Noções básicas de segurança**, escolha o link **mais opções de segurança**.

    ![Noções básicas de segurança com o link "mais opções de segurança" realçado](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. Vá para a seção **verificação em duas etapas** e opte por **Ativar** o recurso. Você também poderá desativá-lo aqui se não quiser mais usá-lo com sua conta pessoal.

### <a name="add-your-microsoft-account-to-the-app"></a>Adicionar a conta Microsoft ao aplicativo

1. Abra o aplicativo Microsoft Authenticator no dispositivo móvel.

2. Selecione **Adicionar conta** do ícone **Personalizar e controlar** no canto superior direito.

    ![Página de contas, com o ícone Personalizar e controlar realçado](./media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

3. Na página **Adicionar conta**, escolha **Conta pessoal**.

4. Entre em sua conta pessoal, usando o endereço de email apropriado (por exemplo, alain@outlook.com) e, em seguida, escolha **Avançar**.

    >[!Note]
    >Se não tiver uma conta Microsoft pessoal, você poderá criar uma aqui.

5. Insira sua senha e, em seguida, escolha **Entrar**.

    Sua conta pessoal é adicionada ao aplicativo Microsoft Authenticator.

## <a name="next-steps"></a>Próximas etapas

- Depois de adicionar as contas ao aplicativo, você poderá entrar usando o aplicativo Authenticator em seu dispositivo. Para obter mais informações, confira [Entrar usando o aplicativo](user-help-auth-app-sign-in.md).

- Se você estiver tendo problemas para obter seu código de verificação para seu conta Microsoft pessoal, consulte a seção Solucionando problemas de **código de verificação** do artigo [conta Microsoft informações de segurança & códigos de verificação](https://support.microsoft.com/en-us/help/12428/microsoft-account-security-info-verification-codes) .

- Para dispositivos que executam o iOS, você também pode fazer backup das credenciais da sua conta e das configurações de aplicativos relacionadas, tais como a ordem de suas contas, na nuvem. Para obter mais informações, confira [Fazer backup e recuperar informações com o aplicativo Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
