---
title: Adicione sua conta corporativa ou de estudante ao aplicativo Microsoft Authenticator – Azure Active Directory | Microsoft Docs
description: Como adicionar sua conta corporativa ou de estudante ao aplicativo Microsoft Authenticator para verificação de dois fatores.
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
ms.openlocfilehash: a73500d17a0dd5d55e60700f7c0b6dbe92a3f96b
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382519"
---
# <a name="add-your-work-or-school-account"></a>Adicionar a conta corporativa ou de estudante

Se a organização usa a verificação de dois fatores, você pode configurar sua conta corporativa ou de estudante para usar o aplicativo Microsoft Authenticator como um dos métodos de verificação.

>[!Important]
>Antes de poder adicionar sua conta, você deverá baixar e instalar o aplicativo Microsoft Authenticator. Se você ainda não tiver feito isso, siga as etapas no artigo [Baixar e instalar o aplicativo](user-help-auth-app-download-install.md).

## <a name="add-your-work-or-school-account"></a>Adicionar a conta corporativa ou de estudante

1. Em seu computador, vá para a página [Verificação de segurança adicional](https://aka.ms/mfasetup).

    >[!Note]
    >Caso não veja a página **Verificação de segurança adicional**, é possível que o administrador tenha ativado a experiência de informações de segurança (versão prévia). Se esse for o caso, você deverá seguir as instruções da seção [Configurar informações de segurança para usar um aplicativo Authenticator](security-info-setup-auth-app.md). Se não for o caso, você precisará entrar em contato com o suporte técnico da sua organização para obter assistência. Para obter mais informações sobre informações de segurança, consulte [visão geral de informações de segurança (versão prévia)](user-help-security-info-overview.md).

2. Marque a caixa ao lado de **aplicativo do autenticador** e selecione **Configurar**.

    A página **Configurar aplicativo móvel** é exibida.

    ![Tela que fornece o código QR](./media/user-help-auth-app-download-install/auth-app-barcode.png)

3. Abra o aplicativo Microsoft Authenticator, selecione **adicionar conta** da **personalizar e controle** ícone no canto superior direito e, em seguida, selecione **trabalho ou conta de escola**.

4. Use a câmera do dispositivo para digitalizar o código QR da tela **Configurar aplicativo móvel** em seu computador e, em seguida, escolha **Concluído**.

    >[!Note]
    >Se a câmera não conseguir capturar o código QR, você poderá adicionar manualmente as informações da conta ao aplicativo Microsoft Authenticator para verificação de dois fatores. Confira [Adicionar sua conta manualmente](user-help-auth-app-add-account-manual.md) para obter mais informações e saber como fazê-lo.

5. Examine a tela **Contas** do aplicativo em seu dispositivo, para garantir que sua conta está correta e que há um código de verificação de seis dígitos associado. Para segurança adicional, o código de verificação muda a cada 30 segundos, evitando que alguém use o mesmo código várias vezes.

    ![Tela de contas](./media/user-help-auth-app-download-install/auth-app-accounts.png)

## <a name="next-steps"></a>Próximas etapas

- Depois de adicionar as contas ao aplicativo, você poderá entrar usando o aplicativo Authenticator em seu dispositivo. Para obter mais informações, confira [Entrar usando o aplicativo](user-help-auth-app-sign-in.md).

- Para dispositivos que executam o iOS, você também pode fazer backup das credenciais da sua conta e das configurações de aplicativos relacionadas, tais como a ordem de suas contas, na nuvem. Para obter mais informações, confira [Fazer backup e recuperar informações com o aplicativo Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
