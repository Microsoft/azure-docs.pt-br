---
title: Adicione uma conta Microsoft pessoal ao aplicativo Microsoft Authenticator - Azure AD
description: Adicione contas Microsoft pessoais, tais como para o Outlook.com ou o Xbox LIVE, ao aplicativo Microsoft Authenticator para verificar sua identidade usando a verificação de dois fatores.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 04/08/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: ff83a9a4e4bfd4c27dd3f8d3f212e489c3772eb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "83741622"
---
# <a name="add-personal-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Adicione contas Microsoft pessoais ao aplicativo Microsoft Authenticator

Adicione suas contas Microsoft pessoais, tais como para o Outlook.com ou o Xbox LIVE, ao aplicativo Microsoft Authenticator para usar tanto a verificação padrão de dois fatores quanto a entrada por celular sem senha.

- **Método de verificação padrão de dois fatores.** Digite seu nome de usuário e senha no dispositivo no qual está se conectando e, em seguida, selecione se o aplicativo Microsoft Authenticator enviará uma notificação ou se você prefere copiar o código de verificação associado da tela **Contas** do Microsoft Authenticator.

- **Método de entrada sem senha.** Digite seu nome de usuário no dispositivo com o qual está se conectando usando sua conta Microsoft pessoal, depois utilize o dispositivo móvel para verificar sua identidade usando sua impressão digital, rosto ou PIN. Para esse método, você não precisa inserir a senha.

>[!Important]
>Para poder adicionar sua conta, você deverá baixar e instalar o aplicativo Microsoft Authenticator. Se você ainda não tiver feito isso, siga as etapas no artigo [Baixar e instalar o aplicativo](user-help-auth-app-download-install.md).

Você pode adicionar sua conta Microsoft pessoal ativando primeiro a verificação de dois fatores e, em seguida, adicionando a conta ao aplicativo. Você não precisa ativar a verificação de dois fatores para usar somente a entrada por telefone sem senha para sua conta, mas é altamente recomendável ativá-la para ter uma segurança adicional para a conta.

## <a name="turn-on-two-factor-verification"></a>Ativar a verificação de dois fatores

1. No seu computador, vá para a página [Noções básicas sobre segurança](https://account.microsoft.com/security) e entre usando sua conta Microsoft pessoal. Por exemplo, alain@outlook.com.

2. Na parte inferior da página **Noções básicas de segurança**, escolha o link **mais opções de segurança**.

    ![Noções básicas de segurança com o link "mais opções de segurança" realçado](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. Vá para a seção **verificação em duas etapas** e opte por **Ativar** o recurso. Você também poderá desativá-lo aqui se não quiser mais usá-lo com sua conta pessoal.

## <a name="add-your-microsoft-account-to-the-app"></a>Adicionar a conta Microsoft ao aplicativo

1. Abra o aplicativo Microsoft Authenticator no dispositivo móvel.

1. No Android, selecione **Adicionar conta** a partir do ícone **Personalizar e controlar** no canto superior direito.

    ![Páginas de seleção de conta do Android](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

    No iOS, selecione o ícone de sinal de adição no canto superior direito.

    ![Versão do iOS da experiência de seleção de conta](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon-ios.png)

1. Na página **Adicionar conta**, escolha **Conta pessoal**.

1. Selecione **Entrar com a conta da Microsoft** para adicionar sua conta. Um código QR pode ser usado quando disponível, mas é possível adicionar a conta entrando com nome de usuário e senha.

    ![Selecione uma conta Microsoft ou digitalize um código QR quando disponível](media/user-help-auth-app-add-personal-ms-account/add-account-android.png)

1. Entre em sua conta pessoal, usando o endereço de e-mail apropriado (por exemplo, alain@outlook.com) e, em seguida, selecione **Avançar**.

    >[!Note]
    >Se não tiver uma conta Microsoft pessoal, [você pode criar uma](https://account.microsoft.com/account/Account?refd=www.bing.com&ru=https%3A%2F%2Faccount.microsoft.com%2F%3Frefd%3Dwww.bing.com&destrt=home-index).

1. Insira sua senha e, em seguida, selecione **Entrar**. Sua conta pessoal é adicionada ao aplicativo Microsoft Authenticator.

## <a name="next-steps"></a>Próximas etapas

- Depois de adicionar as contas ao aplicativo, você poderá entrar usando o aplicativo Authenticator em seu dispositivo. Para obter mais informações, confira [Entrar usando o aplicativo](user-help-auth-app-sign-in.md).

- Se estiver tendo problemas para obter o código de verificação de sua conta pessoal da Microsoft, consulte a seção **Solucionar problemas de código de verificação** do artigo [Informações & códigos de verificação de segurança das contas da Microsoft](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes).

- Para dispositivos que executam o iOS, você também pode fazer backup das credenciais da sua conta e das configurações de aplicativos relacionadas, tais como a ordem de suas contas, na nuvem. Para obter mais informações, confira [Fazer backup e recuperar informações com o aplicativo Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
