---
title: Como usar senhas de aplicativo no Azure MFA? | Microsoft Docs
description: "Esta página ajudará os usuários a entender o que são senhas de aplicativo e para que elas são usadas em relação ao Azure MFA."
services: multi-factor-authentication
documentationcenter: 
author: barlanmsft
manager: femila
editor: yossib
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: 4f343b4d4b5cb3c8be9605e9ab43200072b193e1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="what-are-app-passwords-in-azure-multi-factor-authentication"></a>O que são as senhas de aplicativo no Azure Multi-Factor Authentication?
Determinados aplicativos que não usam navegador, como o cliente de email nativo da Apple, que usa o Exchange Active Sync, atualmente, não oferecem suporte à autenticação multifator. O Multi-Factor Authentication é habilitado por usuário. Isso significa que se um usuário tiver sido habilitado para autenticação multifator e tentar usar aplicativos sem navegador, ele não conseguirá usá-lo. Uma senha de aplicativo permite que isso ocorra.

Quando tiver uma senha de aplicativo, você poderá usá-la no lugar da senha original com esses aplicativos que não usam navegador. Isso ocorre porque, ao aderir à verificação em duas etapas, você está dizendo à Microsoft que, se uma pessoa não puder executar a segunda verificação, ela não poderá se conectar à sua conta com a sua senha. O cliente de email nativo da Apple no seu telefone não pode se conectar, pois ele não pode solicitar a verificação em duas etapas. A solução para isso é criar uma senha de aplicativo mais segura que você não use no dia a dia, apenas para os aplicativos que não dão suporte para verificação em duas etapas. Use a senha do aplicativo para que a autenticação multifator seja ignorada e ele continue funcionando.

> [!NOTE]
> Os clientes do Office 2013 (incluindo Outlook) são compatíveis com novos protocolos de autenticação e podem ser usados com a verificação em duas etapas.  Isso significa que, uma vez habilitadas, as senhas de aplicativo não são necessárias para usar com os clientes do Office 2013.  Para saber mais, veja [Anúncio da visualização pública da autenticação moderna do Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).


## <a name="how-to-use-app-passwords"></a>Como usar senhas de aplicativo
Veja a seguir do que se lembrar sobre como usar senhas de aplicativo.

* Você não cria suas próprias senhas de aplicativo. Em vez disso, elas são geradas automaticamente. Como você só precisa digitar a senha de aplicativo uma vez por aplicativo, é mais seguro usar uma senha mais complexa, gerada automaticamente, em vez de criar uma que você possa se lembrar.
* Atualmente, há um limite de 40 senhas por usuário. Se você tentar criar uma depois de ter atingido o limite, será solicitado que você exclua uma de suas senhas de aplicativo existentes antes de criar uma nova.
* Você deve usar uma senha de aplicativo por dispositivo, não por aplicativo. Por exemplo, você pode criar uma senha de aplicativo para seu laptop e usá-la para todos os aplicativos nesse laptop. Em seguida, crie uma segunda senha de aplicativo a ser usada para todos os seus aplicativos na área de trabalho.
* Você receberá uma senha de aplicativo na primeira vez que se inscrever na verificação em duas etapas.  Se precisar de mais, é possível criá-las.



## <a name="creating-and-deleting-app-passwords"></a>Criação e exclusão de senhas de aplicativo
Durante a conexão inicial, você recebe uma senha de aplicativo que pode usar.  Além disso, você também pode criar e excluir senhas de aplicativo posteriormente.  Como fazer isso depende de como você usa a autenticação multifator. Responda as perguntas a seguir para saber mais sobre como gerenciar as suas senhas de aplicativo:

1. Você usa a verificação em duas etapas em sua conta pessoal da Microsoft? Se sim, consulte o artigo [Senhas de aplicativo e verificação em duas etapas](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-two-step-verification) para obter ajuda. Se não, prossiga para a pergunta dois.

2. Certo, você usa a verificação de duas etapas em sua conta corporativa ou de estudante. Você a utiliza para entrar em aplicativos do Office 365? Se Sim, consulte o artigo [Criar uma senha de aplicativo para o Office 365](https://support.office.com/article/Create-an-app-password-for-Office-365-3e7c860f-bda4-4441-a618-b53953ee1183) para obter ajuda. Se não, prossiga para a pergunta três.

3. Você usa a verificação em duas etapas com o Microsoft Azure? Se sim, vá para a seção [Gerenciar senhas de aplicativo no portal do Azure](#manage-app-passwords-in-the-Azure-portal) deste artigo. Se não, prossiga para a pergunta quatro.

4. Você não sabe onde utiliza a verificação em duas etapas? Vá para a seção [Gerenciar senhas de aplicativo no portal do MyApps](#manage-app-passwords-with-the-myapps-portal) deste artigo.


## <a name="manage-app-passwords-in-the-azure-portal"></a>Gerenciar senhas de aplicativo no portal do Azure
Se você usa a autenticação em duas etapas com o Azure, talvez seja conveniente criar senhas de aplicativo por meio do portal do Azure.

### <a name="to-create-app-passwords-in-the-azure-portal"></a>Para criar senhas de aplicativo no portal do Azure
1. Entre no portal clássico do Azure.
2. Na parte superior, clique com o botão direito do mouse no seu nome de usuário e selecione Verificação de Segurança Adicional.
3. Na parte superior da página de prova, selecione as senhas de aplicativo
4. Clique em **Criar**.
5. Insira um nome para a senha de aplicativo e clique em **Avançar**
6. Copie a senha de aplicativo na área de transferência e cole-a no seu aplicativo.

   ![Nuvem](./media/multi-factor-authentication-end-user-app-passwords/app2.png)


### <a name="to-delete-app-passwords-in-the-azure-portal"></a>Para excluir senhas de aplicativo no portal do Azure
1. Entre no portal clássico do Azure.
2. Na parte superior, clique com o botão direito do mouse no seu nome de usuário e selecione Verificação de Segurança Adicional.
3. Na parte superior, ao lado de verificação de segurança adicional, clique em **senhas de aplicativo.**
4. Ao lado da senha de aplicativo que deseja excluir, selecione **Excluir**.
5. Confirme a exclusão clicando em **sim**.
6. Quando a senha do aplicativo for excluída, clique em **Fechar**.


## <a name="manage-app-passwords-with-the-myapps-portal"></a>Gerenciar senhas de aplicativo no portal do MyApps.
Se não tiver certeza de como você usa a autenticação multifator, será possível criar e excluir senhas de aplicativo por meio do portal do Myapps.

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>Para criar uma senha de aplicativo usando o portal do Myapps
1. Entre em [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Clique no seu nome na parte superior direita e selecione **Perfil**.
3. Escolha **Verificação de Segurança Adicional**.
   ![Selecione Verificação de Segurança Adicional – captura de tela](./media/multi-factor-authentication-end-user-manage/myapps1.png)

4. Selecione **senhas de aplicativo**.
   ![Selecione senhas de aplicativo – captura de tela](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. Clique em **Criar**.
6. Insira um nome para a senha de aplicativo e clique em **Avançar**.
7. Copie a senha de aplicativo na área de transferência e cole-a no seu aplicativo.
   ![Criar uma senha de aplicativo](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>Para excluir uma senha de aplicativo usando o portal do Myapps
1. Entre em [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Na parte superior, selecione Perfil.
3. Escolha **Verificação de Segurança Adicional**.

   ![Selecione Verificação de Segurança Adicional – captura de tela](./media/multi-factor-authentication-end-user-manage/myapps1.png)

4. Selecione **senhas de aplicativo**.

   ![Selecione senhas de aplicativo – captura de tela](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. Do lado da senha do aplicativo que deseja remover, selecione **Excluir**.

   ![Excluir uma senha de aplicativo](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)

6. Confirme que deseja excluir essa senha clicando em **Sim**.
7. Quando a senha do aplicativo for excluída, clique em **Fechar**.

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar suas configurações de verificação em duas etapas](multi-factor-authentication-end-user-manage-settings.md)

- Experimente o [aplicativo Autenticador Microsoft](microsoft-authenticator-app-how-to.md) para verificar suas conexões com notificações de aplicativo, em vez de receber mensagens ou ligações.
