---
title: Entrar em suas contas usando o aplicativo Microsoft Authenticator – Azure Active Directory | Microsoft Docs
description: Use o aplicativo Microsoft Authenticator para entrar em sua conta corporativa ou de estudante ou em suas contas pessoais Microsoft e não Microsoft usando a verificação de dois fatores ou a entrada com telefone.
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
ms.openlocfilehash: 2d7ceb854f5f6f8d0bfb2d589f42fe6d022ec743
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382467"
---
# <a name="sign-in-to-your-accounts-using-the-microsoft-authenticator-app"></a>Entrar em suas contas usando o aplicativo Microsoft Authenticator

O aplicativo Microsoft Authenticator ajuda você a entrar em suas contas quando você usa a verificação de dois fatores. A verificação de dois fatores ajuda você a acessar suas contas com mais segurança, especialmente durante a exibição de informações confidenciais. Como as senhas podem ser esquecidas, roubadas ou comprometidas, a verificação de dois fatores é uma etapa adicional de segurança que ajuda a proteger sua conta, dificultando que ela seja invadida por outras pessoas.

Você pode usar o aplicativo Microsoft Authenticator de várias maneiras, incluindo:

- Fornecendo um prompt para um segundo método de verificação depois de entrar usando seu nome de usuário e senha.

- Fornecendo a entrada sem exigir uma senha, usando seu nome de usuário e seu dispositivo móvel com impressão digital, rosto ou PIN.

  >[!Important]
  >Esse método de entrada pelo telefone só funciona com contas de estudante, corporativas ou pessoais da Microsoft. Contas não Microsoft exigem que você use o processo de verificação padrão de dois fatores.

## <a name="prerequisites"></a>Pré-requisitos

Antes de usar o aplicativo Microsoft Authenticator, é necessário:

 1. Baixar e instalar o aplicativo Microsoft Authenticator. Caso ainda não tenha feito isso, confira [Baixar e instalar o aplicativo](user-help-auth-app-download-install.md).

 2. Adicione suas contas corporativas ou de estudante, pessoais e de terceiros ao aplicativo Microsoft Authenticator. Para obter etapas detalhadas, confira [Adicionar sua conta corporativa ou de estudante](user-help-auth-app-add-work-school-account.md), [Adicionar suas contas pessoais](user-help-auth-app-add-personal-ms-account.md) e [Adicionar suas contas não Microsoft](user-help-auth-app-add-non-ms-account.md).

## <a name="turn-on-and-use-phone-sign-in-for-your-work-or-school-account"></a>Ativar e usar a entrada com telefone para sua conta corporativa ou de estudante

A entrada com telefone é um tipo de verificação em duas etapas. Você ainda precisará verificar sua identidade fornecendo algo que sabe e que tem, mas a entrada com telefone permite ignorar a inserção da senha de sua conta e executa toda a verificação de identidade em seu dispositivo móvel.

Antes de ativar a entrada com telefone, é necessário ativar a verificação de dois fatores. Para obter mais informações sobre como ativar a verificação de dois fatores para uma conta, confira [Adicionar sua conta corporativa ou de estudante](user-help-auth-app-add-work-school-account.md) e [Adicionar suas contas pessoais](user-help-auth-app-add-personal-ms-account.md).

A entrada com telefone só está disponível em dispositivos iOS e Android que executam o Android 6.0 ou posterior.

### <a name="turn-on-phone-sign-in"></a>Ativar a entrada com telefone

- Abra o aplicativo Microsoft Authenticator, acesse sua conta corporativa ou de estudante e ative a entrada com telefone:

    - **Se este ícone é exibido ![ícone mostrando que você realizou a configuração](media/user-help-auth-app-sign-in/icon.png).** Se esse ícone é exibido ao lado do nome de sua conta corporativa ou de estudante, isso significa que você já configurou a entrada com telefone para a conta. Talvez você precise adicionar notificações por push à sua conta, de modo a ser notificado sobre solicitações de autenticação fora do aplicativo.

    - **Se você usa o aplicativo para a verificação de dois fatores.** Se você já usa o aplicativo e a verificação de dois fatores, escolha a seta suspensa ao lado do nome da conta e, em seguida, selecione **Habilitar entrada com telefone**.

    - **Se você não consegue encontrar sua conta corporativa ou de estudante.** Se você não consegue encontrar sua conta corporativa ou de estudante na tela **Contas** do aplicativo, isso significa que você ainda não a adicionou ao aplicativo. Adicione sua conta corporativa ou de estudante seguindo as etapas do artigo [Adicionar sua conta corporativa ou de estudante](user-help-auth-app-add-work-school-account.md).

Depois de ativar a entrada com telefone, entre usando apenas o aplicativo Microsoft Authenticator.

1. Entre sua conta corporativa ou de estudante.

    Depois que você digitar seu nome de usuário, uma tela **Aprovar entrada** será exibida mostrando um número de dois dígitos e solicitando a entrada por meio do aplicativo Microsoft Authenticator. Se você não quiser usar esse método de login, selecione **Use sua senha em vez disso** e faça login usando sua senha.

    ![Caixa Aprovar entrada no computador](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in.png)

2. Abra a notificação ou o aplicativo Microsoft Authenticator em seu dispositivo e, em seguida, toque no número que corresponde ao número exibido na tela **Aprovar entrada** do computador.

    ![Caixa Aprovar entrada no dispositivo](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in-numbers.png)

3. Escolher **aprovar** se reconhece a tentativa de logon. Caso contrário, escolha **Deny**.

4. Use o PIN do seu telefone ou sua chave biométrica para concluir a autenticação.

## <a name="turn-on-and-use-phone-sign-in-for-your-personal-microsoft-accounts"></a>Ativar e usar a entrada com telefone para suas contas pessoais Microsoft

Você pode ativar a entrada de telefone para sua conta pessoal da Microsoft, como a conta que você usa para entrar no Outlook.com, Xbox ou Skype.

>[!NOTE]
>Para ajudar a proteger sua conta, o aplicativo Microsoft Authenticator exige um PIN ou um bloqueio biométrico no dispositivo. Se você mantiver seu smartphone desbloqueado, o aplicativo exigirá que você configure uma trava de segurança antes de ativar o login por telefone.

### <a name="turn-on-phone-sign-in"></a>Ativar a entrada com telefone 

- Abra o aplicativo Microsoft Authenticator, acesse sua conta pessoal Microsoft e ative a entrada com telefone:

    - **Se este ícone é exibido ![ícone mostrando que você realizou a configuração](media/user-help-auth-app-sign-in/icon.png).** Se esse ícone é exibido ao lado do nome de sua conta, isso significa que você já configurou a entrada com telefone para a conta. Talvez você precise adicionar notificações por push à sua conta, de modo a ser notificado sobre solicitações de autenticação fora do aplicativo.

    - **Se você usa o aplicativo para a verificação de dois fatores.** Se você já usa o aplicativo e a verificação de dois fatores, escolha a seta suspensa ao lado do nome da conta e, em seguida, selecione **Habilitar entrada com telefone**.

    - **Se você não consegue encontrar sua conta.** Se você não consegue encontrar sua conta na tela **Contas** do aplicativo, isso significa que você ainda não a adicionou ao aplicativo. Adicione sua conta pessoal Microsoft seguindo as etapas do artigo [Adicionar sua conta pessoal Microsoft](user-help-auth-app-add-personal-ms-account.md#add-your-personal-microsoft-account).

### <a name="sign-in-to-your-account-using-phone-sign-in"></a>Entrar em sua conta usando a entrada com telefone

1. Vá para a página de login da conta pessoal da Microsoft e, em vez de digitar sua senha, selecione o link **Usar um aplicativo Microsoft Authenticator em vez disso**.

    A Microsoft envia uma notificação para seu telefone.

2. Aprove a notificação.

## <a name="sign-in-using-two-factor-verification-for-your-account"></a>Entrar usando a verificação de dois fatores para sua conta

O método de verificação padrão de dois fatores exige que você insira seu nome de usuário e a senha no dispositivo ao qual está fazendo logon e, em seguida, escolha se o aplicativo Microsoft Authenticator enviará uma notificação ou se você prefere copiar o código de verificação associado na tela **Contas** do aplicativo Microsoft Authenticator. Você pode ativar a verificação de dois fatores para sua conta como parte do processo de adicionar a conta ao aplicativo Microsoft Authenticator.

>[!Note]
>Caso sua conta corporativa ou de estudante ou sua conta pessoal não seja exibida na tela **Contas** do aplicativo Microsoft Authenticator, isso significa que você ainda não adicionou a conta ao aplicativo Microsoft Authenticator. Para adicionar sua conta, confira [Adicionar sua conta corporativa ou de estudante](user-help-auth-app-add-work-school-account.md) ou [Adicionar suas contas pessoais](user-help-auth-app-add-personal-ms-account.md).

Para obter as etapas necessárias para entrar em sua conta corporativa ou de estudante ou sua conta pessoal usando os vários métodos de verificação de dois fatores, confira [Entrar usando a verificação em duas etapas ou informações de segurança](user-help-sign-in.md).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

| Pergunta | Solução |
| -------- | -------- |
| Como a entrada com meu telefone é mais segura do que digitar uma senha? | Hoje em dia a maioria das pessoas entra em sites ou aplicativos usando um nome de usuário e senha. Infelizmente, as senhas podem ser perdidas, roubadas ou adivinhadas por hackers.<br><br>Depois de configurar o aplicativo Microsoft Authenticator, ele cria uma chave em seu telefone para desbloquear sua conta, protegida pelo PIN ou pelo bloqueio biométrico de seu telefone. Essa chave é usada, em seguida, para provar sua identidade ao entrar.<br><br>**Importante**<br>Seus dados são usados apenas para proteger sua chave localmente. Nunca é enviado ou armazenado na nuvem. |
| A entrada pelo telefone substitui a verificação em duas etapas? Devo desativá-la? | A entrada com telefone é um tipo de verificação em duas etapas na qual as duas etapas ocorrem no dispositivo móvel. Você deve manter a verificação em duas etapas ativada para ajudar a fornecer segurança adicional para a sua conta. |
| Se eu mantiver a verificação em duas etapas ativada para minha conta, preciso aprovar duas notificações? | Nº Fazer login na sua conta da Microsoft usando seu telefone também é considerado uma verificação em duas etapas, portanto, não é necessária a segunda aprovação. |
| E se eu perder meu telefone ou não tiver? Como fazer acessar minha conta? | Você sempre pode selecionar o link Usar uma senha em vez disso na página de login para voltar a usar sua senha. No entanto, se você usar a verificação em duas etapas, ainda precisará usar um segundo método para confirmar sua identidade.<br><br>**Importante**<br>Recomendamos que você verifique se que você tem mais de um método de verificação atualizado, associado à sua conta.<br><br>Você pode gerenciar seus métodos de verificação para contas pessoais na [página Configurações de segurança](https://account.live.com/proofs/manage). Para contas do trabalho ou da escola, você pode acessar a página [Verificação adicional de segurança](https://aka.ms/MFASetup) da sua organização ou a página **Manter sua conta segura**, se o administrador tiver ativado informações de segurança. Para obter mais informações sobre informações de segurança, consulte [visão geral de informações de segurança (versão prévia)](user-help-security-info-overview.md).<br><br>Se você não conseguir gerenciar seus métodos de verificação, deverá entrar em contato com seu administrador. |
| Como fazer parar de usar esse recurso e voltar a usar minha senha? | Para contas pessoais, selecione o link **Usar uma senha em vez disso** durante a entrada. Sua escolha mais recente é lembrada e oferecida por padrão na próxima vez que você fizer login. Se você quiser voltar a usar a entrada pelo telefone, selecione o link **Usar um aplicativo em vez disso** durante a entrada.<br><br>Para contas corporativas ou de estudante, você deve cancelar o registro do dispositivo na página **configurações** do aplicativo Microsoft Authenticator ou desabilitar o dispositivo na área de **atividade dispositivos &** do seu perfil. Para obter mais informações sobre como desabilitar seu dispositivo do seu perfil, consulte [atualizar suas informações de perfil e conta no portal meus aplicativos](my-apps-portal-end-user-update-profile.md#view-your-organization-related-profile-information). |
| Por que não posso usar mais de uma conta corporativa ou de estudante para entrar no telefone? | Um telefone deve estar registrado em uma única conta de trabalho ou escola. Se você quiser habilitar a entrada pelo telefone para uma conta corporativa ou de estudante, primeiro cancele o registro da conta antiga na página **Configurações**. |
| Posso entrar no meu computador usando meu telefone? | Para o seu computador, é recomendável entrar usando o Windows Hello no Windows 10. O Windows Hello permite que você use seu rosto, impressão digital ou PIN para fazer login. |

## <a name="next-steps"></a>Próximas etapas

- Se você estiver tendo problemas para obter seu código de verificação para seu conta Microsoft pessoal, consulte a seção Solucionando problemas de **código de verificação** do artigo [conta Microsoft informações de segurança & códigos de verificação](https://support.microsoft.com/en-us/help/12428/microsoft-account-security-info-verification-codes) .

- Se você tiver mais dúvidas gerais sobre o aplicativo, confira [Perguntas frequentes sobre o Microsoft Authenticator](user-help-auth-app-faq.md)

- Se você quiser mais informações sobre a verificação em duas etapas, consulte [Configurar minha conta para a verificação em duas etapas](multi-factor-authentication-end-user-first-time.md)

- Se você quiser obter mais informações sobre informações de segurança, consulte [visão geral de informações de segurança (versão prévia)](user-help-security-info-overview.md)
