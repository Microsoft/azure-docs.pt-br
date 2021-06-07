---
title: 'Início Rápido: Adicionar usuários convidados no portal do Azure – Azure AD'
description: Use este guia de início rápido para saber como os administradores do Azure AD podem adicionar usuários convidados B2B no portal do Azure e veja um passo a passo do fluxo de trabalho de convite B2B.
services: active-directory
author: msmimart
ms.author: mimart
manager: celestedg
ms.reviewer: mal
ms.date: 08/05/2020
ms.topic: quickstart
ms.service: active-directory
ms.subservice: B2B
ms.custom:
- it-pro
- seo-update-azuread-jan
- mode-portal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7326a35d07715eae75f70f2f33763f82946c589c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529845"
---
# <a name="quickstart-add-guest-users-to-your-directory-in-the-azure-portal"></a>Início Rápido: adicionar usuários convidados ao diretório no portal do Azure

É possível convidar qualquer pessoa para colaborar com sua organização. Basta adicioná-la ao seu diretório como usuário convidado. Em seguida, você pode enviar um email de convite que contém um link de resgate ou enviar um link direto para um aplicativo que você deseja compartilhar. Os usuários convidados podem entrar com as próprias identidades empresariais, estudantis ou sociais. Juntamente com este guia de início rápido, você pode saber mais sobre como adicionar usuários convidados [no portal do Azure](add-users-administrator.md) por meio do [PowerShell](b2b-quickstart-invite-powershell.md) ou [em massa](tutorial-bulk-invite.md).

Neste início rápido, você adicionará um usuário convidado ao diretório do Azure AD usando o portal do Azure, enviará um convite e acompanhará o processo de resgate de convite dos usuários convidados.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir o cenário deste tutorial, você precisa de:

 - Uma função que permita criar usuários no diretório do locatário, como a função de administrador global ou qualquer uma das funções de administração limitada do diretório.
 - Uma conta de email válida que pode ser adicionada ao diretório do locatário e usada para receber o email de convite de teste.

## <a name="add-a-new-guest-user-in-azure-ad"></a>Adicionar um novo usuário convidado no Azure AD

1. Entre no [Portal do Azure](https://portal.azure.com/) como administrador do Microsoft Azure AD.
2. No painel esquerdo, selecione **Azure Active Directory**.
3.  Em **Gerenciar**, selecione **Usuários**.

    ![Captura de tela mostrando onde selecionar a opção Usuários](media/quickstart-add-users-portal/quickstart-users-portal-user.png)

4.  Selecione **Novo usuário convidado**.

    ![Captura de tela mostrando onde selecionar a opção Novo usuário convidado](media/quickstart-add-users-portal/quickstart-users-portal-user-3.png)

5. Na página **Novo usuário**, selecione **Convidar usuário** e adicione as informações do usuário convidado. 

   - **Name.** O nome e o sobrenome do usuário convidado.
   - **Endereço de email (obrigatório)** . O endereço de email do usuário convidado.
   - **Mensagem pessoal (opcional)** Inclua uma mensagem pessoal de boas-vindas para o usuário convidado.
   - **Grupos**: você pode adicionar o usuário convidado a um ou mais grupos existentes, ou pode fazer isso posteriormente.
   - **Função do diretório**: se precisar de permissões administrativas do Azure AD para o usuário, você poderá adicioná-las a uma função do Azure AD. 

6. Selecione **Convidar** para enviar automaticamente o convite ao usuário convidado. Uma notificação será exibida no canto superior direito com a mensagem **Usuário convidado com êxito**. 
7.  Depois de enviar o convite, a conta de usuário é automaticamente adicionada ao diretório como convidado.

## <a name="assign-an-app-to-the-guest-user"></a>Atribuir um aplicativo ao usuário convidado
Adicione o aplicativo do Salesforce ao locatário de teste e atribua o usuário convidado de teste ao aplicativo.
1.  Entre no portal do Azure como administrador do Azure AD.
2.  No painel esquerdo, selecione **Aplicativos Empresariais**.
3.  Selecione **Novo aplicativo**.
4. Em **Adicionar por meio da galeria**, pesquise **Salesforce** e, em seguida, selecione-o.

    ![Captura de tela mostrando a caixa de pesquisa Adicionar por meio da galeria](media/quickstart-add-users-portal/quickstart-users-portal-select-salesforce.png)
5. Selecione **Adicionar**.
6. Em **Gerenciar**, selecione **Logon único**. Em **Modo de Logon Único**, selecione **Logon baseado em Senha** e clique em **Salvar**.
7. Em **Gerenciar**, selecione **Usuários e grupos** > **Adicionar usuário** > **Usuários e grupos**.
8. Use a caixa de pesquisa para pesquisar o usuário de teste (se necessário) e selecione o usuário de teste na lista. Em seguida, clique em **Selecionar**.
9. Selecione **Atribuir**. 

## <a name="accept-the-invitation"></a>Aceitar o convite
Agora, entre como usuário convidado para ver o convite.
1.  Entre na conta de email do usuário convidado de teste.
2.  Na caixa de entrada, encontre o email de "Você foi convidado".

    ![Captura de tela mostrando o email do convite B2B](media/quickstart-add-users-portal/quickstart-users-portal-email-small.png)

3.  No corpo do email, selecione **Começar**. Uma página **Analisar as permissões** será aberta no navegador. 

    ![Captura de tela mostrando a página Analisar as permissões](media/quickstart-add-users-portal/quickstart-users-portal-accept.png)

4. Selecione **Aceitar**. O Painel de Acesso é aberto e lista os aplicativos que o usuário convidado pode acessar.

## <a name="clean-up-resources"></a>Limpar os recursos
Exclua o usuário convidado de teste e o aplicativo de teste quando não forem mais necessários.
1.  Entre no portal do Azure como administrador do Azure AD.
2.  No painel esquerdo, selecione **Azure Active Directory**.
3.  Em **Gerenciar**, selecione **Aplicativos empresariais**.
4.  Abra o aplicativo **Salesforce** e, em seguida, selecione **Excluir**.
5.  No painel esquerdo, selecione **Azure Active Directory**.
6.  Em **Gerenciar**, selecione **Usuários**.
7.  Selecione o usuário de teste e, em seguida, selecione **Excluir usuário**.

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você criou um usuário convidado no portal do Azure e enviou um convite para compartilhar aplicativos. Em seguida, você exibiu o processo de resgate da perspectiva do usuário convidado e conferiu se o aplicativo apareceu no Painel de Acesso do usuário convidado. Para saber mais sobre como adicionar usuários convidados para colaboração, confira [Adicionar usuários de colaboração do Azure Active Directory B2B no portal do Azure](add-users-administrator.md).
