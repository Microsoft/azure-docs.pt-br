---
title: Acessar e usar aplicativos no portal meus aplicativos – Azure Active Directory | Microsoft Docs
description: Saiba como acessar o portal meus aplicativos.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 901067e198616dba390e0e1162ec26a0b11768f1
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383145"
---
# <a name="access-and-use-apps-on-the-my-apps-portal"></a>Acessar e usar aplicativos no portal meus aplicativos

É possível usar sua conta corporativa ou de estudante com o portal **Meus Aplicativos** baseado na Web para exibir e iniciar muitos aplicativos baseados em nuvem da sua organização, para atualizar algumas informações do perfil e da conta, para ver suas informações de **Grupos** e para realizar **revisões de acesso** para seus aplicativos e grupos. Se você não tiver acesso ao portal **Meus Aplicativos**, será necessário contatar a assistência técnica para obter permissão.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Este conteúdo destina-se aos usuários. Se você for administrador, poderá encontrar mais informações sobre como configurar e gerenciar seus aplicativos baseados em nuvem na [Documentação de Gerenciamento do Aplicativo](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="supported-browsers"></a>Navegadores com suporte

Você pode acessar o portal **meus aplicativos** de qualquer um dos seguintes navegadores da Web:

- Google Chrome

- Mozilla Firefox, versão 26,0 ou posterior

- Microsoft Edge

- Internet Explorer, versão 11 (suporte limitado)

## <a name="download-and-install-the-my-apps-secure-sign-in-extension"></a>Baixar e instalar a extensão de entrada segura de meus aplicativos

Baixe e instale a extensão de entrada segura de meus aplicativos, se for solicitado. Essa extensão ajuda a iniciar qualquer um dos aplicativos de nuvem da sua organização que exigem que você use um processo de logon único. Se sua organização já tiver configurado para logon único, a extensão será instalada automaticamente e você poderá ignorar esta seção.

Essa extensão ajuda você a:

- Entre diretamente em aplicativos da página de entrada.

- Inicie todos os aplicativos usando o recurso **pesquisa rápida** .

- Consulte os últimos aplicativos usados na seção **usado recentemente** .

- Use URLs internas da empresa enquanto estiver remoto com o [proxy de aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started).

### <a name="to-download-and-install-the-extension"></a>Para baixar e instalar a extensão

Baixe e instale a extensão, com base no navegador que você está usando:

- **Google Chrome.** No repositório da Web do Chrome, vá para o recurso de [extensão de entrada segura meus aplicativos](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl) e, em seguida, selecione **Adicionar ao Chrome**.

- **Mozilla Firefox** Na página **Complementos do Firefox** , vá para o recurso de [extensão de entrada segura meus aplicativos](https://addons.mozilla.org/firefox/addon/access-panel-extension/) e, em seguida, selecione **Adicionar ao Firefox**.

- **Microsoft Edge** Na Microsoft Store, vá para o recurso de [extensão de entrada segura meus aplicativos](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) e, em seguida, selecione **obter**.

Um ícone é adicionado à direita da barra de **endereços** , permitindo que você entre e personalize a extensão.

#### <a name="to-change-your-my-apps-portal-using-the-extension"></a>Para alterar o portal meus aplicativos usando a extensão
Você pode escolher quantos aplicativos exibir na seção **usado recentemente** e decidir se deseja permitir que as URLs internas da sua organização redirecionem.

1. Selecione o ícone de](media/my-apps-portal/my-apps-portal-extension-icon.png) extensão do ![ícone de extensão de **entrada segura de meus aplicativos** no lado direito da barra de **endereços** e selecione **entrar para começar**.

2. Clique **com o botão** direito do ![mouse no](media/my-apps-portal/my-apps-portal-extension-settings-icon.png)ícone configurações **e selecione configurações**.

3. Na caixa **configurações** , selecione o número de aplicativos recentes que você deseja ver no portal e se deseja permitir que as URLs internas da sua organização redirecionem para que você possa usá-las remotamente.

    ![Página Configurações da extensão, mostrando as personalizações disponíveis](media/my-apps-portal/my-apps-portal-extension-settings-page.png)

## <a name="access-and-use-the-my-apps-portal-by-device"></a>Acessar e usar o portal meus aplicativos por dispositivo
Você pode acessar e usar o portal meus aplicativos em seu computador, em um navegador gerenciado pelo Intune ou em um dispositivo móvel iOS ou Android.

![Página de aplicativos no portal meus aplicativos](media/my-apps-portal/my-apps-portal-apps-page.png)

### <a name="access-and-use-the-my-apps-portal-on-your-computer"></a>Acessar e usar o portal meus aplicativos em seu computador
Se você tiver permissão para acessar e usar os aplicativos baseados em nuvem da sua organização, poderá acessá-los por meio do portal **meus aplicativos** .

1. Entre sua conta corporativa ou de estudante.

2. Abra o navegador da Web e vá https://myapps.microsoft.com para ou use o link fornecido pela sua organização. Por exemplo, você pode ser direcionado para uma página personalizada para sua organização, como https://myapps.microsoft.com/contoso.com.

    A página **aplicativos** é exibida, mostrando todos os aplicativos baseados em nuvem pertencentes à sua organização e disponíveis para uso.

3. Na página **aplicativos** , selecione o aplicativo que você deseja começar a usar.

    Uma nova página é aberta para o aplicativo, onde você pode entrar (se necessário) ou começar a usar o aplicativo.

### <a name="access-and-use-the-my-apps-portal-on-an-intune-managed-browser"></a>Acessar e usar o portal meus aplicativos em um navegador gerenciado pelo Intune

Exiba e use os aplicativos da sua organização de um Intune Managed Browser em seus dispositivos iOS e Android.

1. Em seu dispositivo móvel, baixe e instale o aplicativo Intune Managed Browser da [Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) e o [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser).

2. Abra o aplicativo Intune Managed browser, vá para https://myapps.microsoft.com ou use o link fornecido pela sua organização. Por exemplo, você pode ser direcionado para uma página personalizada para sua organização, como https://myapps.microsoft.com/contoso.com.

    A página **aplicativos** é exibida, mostrando todos os aplicativos baseados em nuvem pertencentes à sua organização e disponíveis para uso.

3. Na página **aplicativos** , selecione o aplicativo que você deseja começar a usar.

    Uma nova página é aberta para o aplicativo, onde você pode entrar (se necessário) ou começar a usar o aplicativo.

### <a name="access-and-use-the-my-apps-portal-on-an-ios-device"></a>Acessar e usar o portal meus aplicativos em um dispositivo iOS

Exiba e use o portal **meus aplicativos** de um dispositivo iPhone ou iPad, executando o IOS versão 7 ou posterior. Você também pode instalar o [aplicativo móvel meus aplicativos](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8) para acessar os aplicativos da sua organização em seus dispositivos IOS. 

1. Em seu dispositivo móvel, inicie um aplicativo de navegador da Web, como o Safari.

2. Vá para https://myapps.microsoft.com ou use o link fornecido pela sua organização. Por exemplo, você pode ser direcionado para uma página personalizada para sua organização, como https://myapps.microsoft.com/contoso.com.

    A página **aplicativos** é exibida, mostrando todos os aplicativos baseados em nuvem pertencentes à sua organização e disponíveis para uso.

3. Na página **aplicativos** , selecione o aplicativo que você deseja começar a usar.

    Uma nova página é aberta para o aplicativo, onde você pode entrar (se necessário) ou começar a usar o aplicativo.

### <a name="access-and-use-the-my-apps-portal-on-an-android-device"></a>Acessar e usar o portal meus aplicativos em um dispositivo Android

Exibir e usar o portal **meus aplicativos** em um dispositivo Android.

1. Em seu dispositivo móvel, inicie um aplicativo de navegador da Web, como o Google Chrome.

2. Vá para https://myapps.microsoft.com ou use o link fornecido pela sua organização. Por exemplo, você pode ser direcionado para uma página personalizada para sua organização, como https://myapps.microsoft.com/contoso.com.

    A página **aplicativos** é exibida, mostrando todos os aplicativos baseados em nuvem pertencentes à sua organização e disponíveis para uso.

3. Na página **aplicativos** , selecione o aplicativo que você deseja começar a usar.

    Uma nova página é aberta para o aplicativo, onde você pode entrar (se necessário) ou começar a usar o aplicativo.

## <a name="add-a-new-app-to-the-my-apps-portal"></a>Adicionar um novo aplicativo ao portal meus aplicativos

Se o administrador tiver concedido a você permissão, você poderá adicionar um novo aplicativo à página **aplicativos** .

1. Na página **aplicativos** , selecione **Adicionar aplicativo**.

    A página **adicionar aplicativos** é exibida.

    ![Página Adicionar aplicativos, no portal meus aplicativos](media/my-apps-portal/my-apps-portal-add-apps-page.png)

2. Selecione o aplicativo que você deseja adicionar na lista fornecida e, em seguida, selecione **Adicionar**.

3. O aplicativo é adicionado à lista na página de **aplicativos** .

    Alguns aplicativos podem exigir aprovação do administrador antes de serem adicionados. Nessa situação, o aplicativo não é adicionado à página de **aplicativos** até que o administrador o aprove.

## <a name="next-steps"></a>Próximas etapas

Depois de acessar a página de **aplicativos** , você pode:

- [Alterar as informações do seu perfil](my-apps-portal-end-user-update-profile.md)

- [Exibir e atualizar suas informações relacionadas a grupos](my-apps-portal-end-user-groups.md)

- [Executar suas próprias revisões de acesso](my-apps-portal-end-user-access-reviews.md)
