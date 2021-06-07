---
title: Conexão usando o aplicativo Microsoft Authenticator - Azure AD
description: Use o aplicativo Microsoft Authenticator para entrar em sua conta corporativa ou de estudante ou em suas contas pessoais Microsoft e não Microsoft usando a verificação de dois fatores ou a entrada com telefone.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 03/12/2021
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 0fd0aa19d52adb4bf06c9d21d4182e27497a531b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103471765"
---
# <a name="sign-in-to-your-accounts-using-the-microsoft-authenticator-app"></a>Entrar em suas contas usando o aplicativo Microsoft Authenticator

O aplicativo Microsoft Authenticator ajuda a entrar em suas contas quando você usa a verificação de dois fatores. A verificação de dois fatores ajuda você a acessar suas contas com mais segurança, especialmente durante a exibição de informações confidenciais. Como as senhas podem ser esquecidas, roubadas ou comprometidas, a verificação de dois fatores é uma etapa adicional de segurança que ajuda a proteger sua conta, dificultando que ela seja invadida por outras pessoas.

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

Abra o aplicativo Microsoft Authenticator, acesse sua conta corporativa ou de estudante e ative a entrada com telefone.

- **Ao tocar no bloco da conta**, você verá uma exibição de tela inteira da conta. A opção **Entrada com telefone habilitada** significa que você já está configurado para entrar sem sua senha. Toque na opção **Habilitar entrada com telefone** para ativar a entrada com telefone.
- **Se você já estiver usando o aplicativo para verificação de dois fatores**, poderá tocar no bloco da conta para ver uma exibição de tela inteira da conta. Em seguida, toque na opção **Habilitar entrada com telefone** para ativar a entrada com telefone.
- **Se não conseguir encontrar sua conta corporativa ou de estudante** na tela **Contas** do aplicativo, é porque você ainda não a adicionou ao aplicativo. Adicione sua conta corporativa ou de estudante seguindo as etapas na [ajuda adicionar sua conta corporativa ou de estudante](user-help-auth-app-add-work-school-account.md).

> [!NOTE]
> A Microsoft não dá suporte a uma combinação de registro de dispositivo e autenticação baseada em certificado no autenticador no iOS. Em vez disso, o usuário deve registrar o dispositivo manualmente por meio de configurações do autenticador antes de entrar.

Depois de ativar a entrada pelo telefone, você pode entrar usando apenas o aplicativo Microsoft Authenticator. Aqui está como:

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

Abra o aplicativo Microsoft Authenticator, acesse sua conta corporativa ou de estudante e ative a entrada com telefone.

- **Ao tocar no bloco da conta**, você verá uma exibição de tela inteira da conta. A opção **Entrada com telefone habilitada** significa que você já está configurado para entrar sem sua senha. Toque na opção **Habilitar entrada com telefone** para ativar a entrada com telefone.
- **Se você já estiver usando o aplicativo para verificação de dois fatores**, poderá tocar no bloco da conta para ver uma exibição de tela inteira da conta. Em seguida, toque na opção **Habilitar entrada com telefone** para ativar a entrada com telefone.
- **Se não conseguir encontrar sua conta** na tela **Contas** do aplicativo, é porque você ainda não a adicionou ao aplicativo. Adicione sua conta pessoal Microsoft seguindo as etapas descritas no artigo [Adicionar sua conta pessoal Microsoft](user-help-auth-app-add-personal-ms-account.md).

### <a name="sign-in-to-your-account-using-phone-sign-in"></a>Entrar em sua conta usando a entrada com telefone

1. Vá para a página de login da conta pessoal da Microsoft e, em vez de digitar sua senha, selecione o link **Usar um aplicativo Microsoft Authenticator em vez disso**.

    A Microsoft envia uma notificação para seu telefone.

2. Aprove a notificação.

## <a name="sign-in-using-two-factor-verification-for-your-account"></a>Entrar usando a verificação de dois fatores para sua conta

O método de verificação padrão de dois fatores exige que você insira seu nome de usuário e senha no dispositivo no qual está entrando e, em seguida, escolha se o aplicativo de Microsoft Authenticator recebe uma notificação ou se você deseja copiar o código de verificação do aplicativo autenticador. Em um dispositivo Android, os códigos de verificação podem ser encontrados na tela **contas** . Em um dispositivo iOS, esses códigos de verificação podem ser encontrados na tela **contas** ou na exibição de tela inteira de uma conta, dependendo do tipo de conta. Você ativa a verificação de dois fatores para sua conta ao adicionar a conta ao aplicativo Microsoft Authenticator.

>[!Note]
>Caso sua conta corporativa ou de estudante ou sua conta pessoal não seja exibida na tela **Contas** do aplicativo Microsoft Authenticator, isso significa que você ainda não adicionou a conta ao aplicativo Microsoft Authenticator. Para adicionar sua conta, confira [Adicionar sua conta corporativa ou de estudante](user-help-auth-app-add-work-school-account.md) ou [Adicionar suas contas pessoais](user-help-auth-app-add-personal-ms-account.md).

Para obter as etapas necessárias para entrar em sua conta corporativa ou de estudante ou sua conta pessoal usando os vários métodos de verificação de dois fatores, confira [Entrar usando a verificação em duas etapas ou informações de segurança](user-help-sign-in.md).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

| Pergunta | Solução |
| -------- | -------- |
| Como a entrada com meu telefone é mais segura do que digitar uma senha? | Hoje em dia a maioria das pessoas entra em sites ou aplicativos usando um nome de usuário e senha. Infelizmente, as senhas podem ser perdidas, roubadas ou adivinhadas por hackers.<br><br>Depois de configurar o aplicativo Microsoft Authenticator, ele cria uma chave em seu telefone para desbloquear sua conta, protegida pelo PIN ou pelo bloqueio biométrico de seu telefone. Essa chave é usada, em seguida, para provar sua identidade ao entrar.<br><br>**Importante**<br>Seus dados são usados apenas para proteger sua chave localmente. Nunca é enviado ou armazenado na nuvem. |
| A entrada com telefone substitui a verificação em duas etapas? Devo desativá-la? | A entrada com telefone é um tipo de verificação em duas etapas na qual as duas etapas ocorrem no dispositivo móvel. Você deve manter a verificação em duas etapas ativada para ajudar a fornecer segurança adicional para a sua conta. |
| Se eu mantiver a verificação em duas etapas ativada para a minha conta, tenho que aprovar duas notificações? | Não. Fazer login na sua conta da Microsoft usando seu telefone também é considerado uma verificação em duas etapas, portanto, não é necessária a segunda aprovação. |
| E se eu perder meu telefone ou ele não estiver comigo? Como posso acessar minha conta? | Você sempre pode selecionar o link Usar uma senha em vez disso na página de login para voltar a usar sua senha. No entanto, se você usar a verificação em duas etapas, ainda precisará usar um segundo método para confirmar sua identidade.<br><br>**Importante**<br>Recomendamos que você verifique se que você tem mais de um método de verificação atualizado, associado à sua conta.<br><br>Você pode gerenciar seus métodos de verificação para contas pessoais na [página Configurações de segurança](https://account.live.com/proofs/manage). Para contas do trabalho ou da escola, você pode acessar a página [Verificação adicional de segurança](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1) da sua organização ou a página **Manter sua conta segura**, se o administrador tiver ativado informações de segurança. Para saber mais sobre as informações de segurança, consulte [Visão geral sobre as informações de segurança (versão preliminar)](./security-info-setup-signin.md).<br><br>Se você não conseguir gerenciar seus métodos de verificação, deverá entrar em contato com seu administrador. |
| Como eu faço para parar de usar esse recurso e voltar a usar minha senha? | Para contas pessoais, selecione o link **Usar uma senha em vez disso** durante a entrada. Sua escolha mais recente é lembrada e oferecida por padrão na próxima vez que você fizer login. Se você quiser voltar a usar a entrada pelo telefone, selecione o link **Usar um aplicativo em vez disso** durante a entrada.<br><br>Para contas corporativas ou de estudante, cancele o registro do dispositivo na página **Configurações** do aplicativo Microsoft Authenticator ou desabilite o dispositivo na área **Dispositivos e atividade** do seu perfil. Para saber mais informações sobre como desabilitar o dispositivo do seu perfil, veja [Atualização das informações de perfil e de conta no portal Meus Aplicativos](./my-account-portal-devices-page.md#disable-a-device). |
| Por que não consigo usar mais de uma conta corporativa ou de estudante para entrar pelo telefone? | Um telefone deve estar registrado em uma única conta de trabalho ou escola. Se quiser habilitar a entrada com telefone através de uma conta corporativa ou de estudante diferente, primeiro cancele o registro da conta através deste dispositivo na página **Configurações**. |
| Posso entrar no meu computador usando o meu telefone? | Para o seu computador, é recomendável entrar usando o Windows Hello no Windows 10. O Windows Hello permite que você use seu rosto, impressão digital ou PIN para fazer login. |

## <a name="next-steps"></a>Próximas etapas

- Se estiver com problemas para receber o código de verificação da sua conta Microsoft pessoal, veja a seção **Solução de problemas de código de verificação** do artigo [Informações de segurança e códigos de verificação da conta Microsoft](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes).

- Se você tiver mais dúvidas gerais sobre o aplicativo, confira [Perguntas frequentes sobre o Microsoft Authenticator](user-help-auth-app-faq.md)

- Se você quiser mais informações sobre a verificação em duas etapas, consulte [Configurar minha conta para a verificação em duas etapas](multi-factor-authentication-end-user-first-time.md)

- Se quiser saber mais informações sobre as informações de segurança, veja [Visão geral sobre as informações de segurança (versão preliminar)](./security-info-setup-signin.md)