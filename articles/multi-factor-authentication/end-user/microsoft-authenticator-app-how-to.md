---
title: Aplicativo Microsoft Authenticator para telefones celulares | Microsoft Docs
description: "Saiba como atualizar para a versão mais recente do Azure Authenticator."
services: multi-factor-authentication
documentationcenter: 
author: barlanmsft
manager: femila
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: barlan
ms.reviewer: librown
ms.custom: H1Hack27Feb2017, end-user
ms.openlocfilehash: e1e979319e53d222be33032acd0a79697dd09ab6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-microsoft-authenticator-app"></a>Introdução ao aplicativo Microsoft Authenticator
O aplicativo Microsoft Authenticator fornece um nível adicional de segurança em sua conta corporativa ou de estudante (por exemplo, bsimon@contoso.com) ou conta da Microsoft (por exemplo, bsimon@outlook.com).

O aplicativo funciona de uma das duas maneiras:

* **Notificação**. O aplicativo pode ajudar a evitar o acesso não autorizado a contas e interromper transações fraudulentas enviando uma notificação para seu smartphone ou tablet. Basta ver a notificação e se ela for legítima, selecione **Verificar**. Caso contrário, você pode selecionar **Negar**.
* **Código de verificação**. O aplicativo pode ser usado como um token de software para gerar um código de verificação OAuth. Depois de inserir o nome de usuário e senha, insira o código fornecido pelo aplicativo na tela de entrada. O código de verificação oferece uma segunda forma de autenticação.

O aplicativo Microsoft Authenticator substitui o aplicativo Azure Authenticator. O aplicativo Azure Authenticator ainda está funcionando, mas se você decidir ir para o novo aplicativo Microsoft Authenticator, este artigo poderá ajudar.  

## <a name="opt-in-for-two-step-verification"></a>Aceitar a verificação em duas etapas

O aplicativo Microsoft Authenticator não funciona por si só. Configure cada uma das suas contas para solicitarem a você um segundo método de verificação após você entrar com seu nome de usuário e senha.

Para uma conta corporativa ou de estudante, geralmente você não pode escolher esse recurso. Em vez disso, um administrador de segurança aceita em seu nome e o notifica para registrar os métodos de verificação para sua conta. Se esta situação se aplica a você, saiba mais em [A Autenticação Multifator do Azure significa para mim](multi-factor-authentication-end-user.md).

Para uma conta pessoal, você precisa configurar a verificação em duas etapas por conta própria. Se você tem uma conta da Microsoft, essas etapas estão disponíveis em [Sobre a verificação em duas etapas](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

Você também pode usar o Microsoft Authenticator com contas não Microsoft. Eles podem chamar o recurso de algo diferente de verificação em duas etapas, mas você poderá encontrá-lo nas configurações de segurança ou de conexão.

## <a name="install-the-app"></a>Instalar o aplicativo
O aplicativo Microsoft Authenticator está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [iOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## <a name="add-accounts-to-the-app"></a>Adicionar contas ao aplicativo
Para cada conta que você deseja adicionar ao aplicativo Microsoft Authenticator, use um destes procedimentos:

### <a name="add-a-personal-microsoft-account-to-the-app"></a>Adicionar uma conta pessoal da Microsoft ao aplicativo

Para ter uma conta pessoal da Microsoft (aquela que você usa para entrar no Outlook.com, Xbox, Skype etc.), basta entrar em sua conta no aplicativo Microsoft Authenticator.

### <a name="add-a-work-or-school-account-to-the-app-using-the-qr-code-scanner"></a>Adicionar uma conta corporativa ou de estudante ao aplicativo usando o scanner de código QR
1. Vá para a tela de configurações da verificação de segurança.  Para obter mais informações sobre como acessar essa tela, consulte [Alterando as configurações de segurança](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page).
2. Marque a caixa ao lado de **Aplicativo autenticador** e selecione **Configurar**.

    ![Botão Configurar na tela de configurações da verificação de segurança](./media/authenticator-app-how-to/azureauthe.png)

    Isso abre uma tela com um código QR.

    ![Tela que fornece o código QR](./media/authenticator-app-how-to/barcode2.png)
3. Abra o aplicativo Microsoft Authenticator. Na tela de **contas**, selecione **+**, em seguida, especifique que você deseja adicionar uma conta corporativa ou de estudante.
4. Use a câmera para verificar o código QR e selecione **Concluído** para fechar a tela do código QR.

    Se a câmera não estiver funcionando corretamente, [insira o código QR e a URL manualmente](#add-an-account-to-the-app-manually).

5. Quando o aplicativo mostrar o nome de sua conta com um código de seis dígitos abaixo dela, você terá concluído.

    ![Tela de contas](./media/authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>Adicionar manualmente uma conta ao aplicativo
1. Vá para a tela de configurações da verificação de segurança.  Para obter mais informações sobre como acessar essa tela, consulte [Alterando as configurações de segurança](multi-factor-authentication-end-user-manage-settings.md).
2. Selecione **Configurar**.

    ![Botão Configurar na tela de configurações da verificação de segurança](./media/authenticator-app-how-to/azureauthe.png)

    Isso abre uma tela com um código QR.  Observe o código e a URL.

    ![Tela que fornece o código QR e a URL](./media/authenticator-app-how-to/barcode2.png)
3. Abra o aplicativo Microsoft Authenticator. Na tela de **contas**, selecione **+**, em seguida, especifique que você deseja adicionar uma conta corporativa ou de estudante.

4. No scanner, selecione **inserir o código manualmente**.

    ![Tela para verificar um código QR](./media/multi-factor-authentication-end-user-first-time/scan2.png)
5. Insira o código e a URL nas caixas apropriadas no aplicativo e selecione **Concluir**.

    ![Tela para inserir código e a URL](./media/authenticator-app-how-to/manual.png)

6. Quando o aplicativo mostrar o nome de sua conta com um código de seis dígitos abaixo dela, você terá concluído.

    ![Tela de contas](./media/authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-using-touch-id"></a>Adicionar uma conta ao aplicativo usando o Touch ID
O aplicativo Microsoft Authenticator no iOS dá suporte ao Touch ID.  A Autenticação Multifator do Azure permite que as organizações exijam um PIN para os dispositivos. Com o Touch ID, os usuários do iOS não precisam inserir um PIN. Em vez disso, eles podem digitalizar sua impressão digital e selecionar **Aprovar**.

A configuração do Touch ID com o Microsoft Authenticator é simples. Você conclui um desafio de verificação normal com um PIN. Se seu dispositivo der suporte ao o Touch ID, o Microsoft Authenticator irá configurá-lo automaticamente para essa conta.

![Verificação da configuração do Touch ID](./media/authenticator-app-how-to/touchid1.png)

Desse ponto em diante, quando você precisar verificar sua entrada, selecionará a notificação por push recebida e digitalizará sua impressão digital em vez de inserir seu PIN.

![Notificação por push](./media/authenticator-app-how-to/touchid2.png)

## <a name="use-the-app-when-you-sign-in"></a>Usar o aplicativo ao entrar

Depois que sua conta for adicionada ao aplicativo, você precisará fazer uma verificação de teste para verificar se tudo foi configurado corretamente. Depois disso, você terminou! Você não precisa fazer mais nada até a próxima vez que entrar.

Se você optar por usar códigos de verificação no aplicativo, começará a vê-los na home page. Eles são alterados a cada 30 segundos para que você sempre tenha um novo código quando precisar de um. Mas você não precisa fazer nada com eles até que você entre e seja solicitado que você insira um código de verificação.  
