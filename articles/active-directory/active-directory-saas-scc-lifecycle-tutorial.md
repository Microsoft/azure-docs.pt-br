---
title: "Tutorial: integração do Azure Active Directory com o SCC LifeCycle | Microsoft Docs"
description: "Saiba como usar o SCC LifeCycle com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 9748bf38-ffc3-4d51-a1ae-207ce57104fa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/23/2017
ms.author: jeedes
ms.openlocfilehash: 9a30bcca720ff135d0180d73f46e78403e9bca43
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-scc-lifecycle"></a>Tutorial: Integração do Active Directory do Azure com o SCC LifeCycle
O objetivo deste tutorial é mostrar a integração do Azure com o SCC LifeCycle.  

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura do SCC LifeCycle com SSO (logon único) habilitado

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao SCC LifeCycle poderão fazer logon único no aplicativo em seu site de empresa do SCC LifeCycle (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos com o SCC LifeCycle
2. Configuração do SSO (logon único)
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-scc-lifecycle-tutorial/IC794120.png "Cenário")

## <a name="enable-the-application-integration-for-scc-lifecycle"></a>Habilitar a integração de aplicativos com o SCC LifeCycle
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o SCC LifeCycle.

**Para habilitar a integração de aplicativos com o SCC LifeCycle, execute as seguintes etapas:**

1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-scc-lifecycle-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos](./media/active-directory-saas-scc-lifecycle-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Adicionar aplicativo](./media/active-directory-saas-scc-lifecycle-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-scc-lifecycle-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **SCC LifeCycle**.
   
    ![Galeria de Aplicativos](./media/active-directory-saas-scc-lifecycle-tutorial/IC794121.png "Galeria de Aplicativos")
7. No painel de resultados, selecione **SCC LifeCycle** e clique em **Concluir** para adicionar o aplicativo.
   
    ![SCC LifeCycle](./media/active-directory-saas-scc-lifecycle-tutorial/IC795082.png "SCC LifeCycle")
   
## <a name="configure-single-sign-on"></a>Configurar o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no SCC LifeCycle com sua conta do AD do Azure usando federação baseada no protocolo SAML.

**Para configurar o logon único, execute as seguintes etapas:**

1. No Portal Clássico do Azure, na página de integração de aplicativos do **SCC LifeCycle**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único](./media/active-directory-saas-scc-lifecycle-tutorial/IC794122.png "Configurar Logon Único")
2. Na página **Como você deseja que os usuários façam logon no SCC LifeCycle**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-scc-lifecycle-tutorial/IC794123.png "Configurar Logon Único")
3. Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de Entrada**, digite a URL usada por seus usuários para fazer logon no aplicativo SCC LifeCycle usando o padrão "*https://bs1.scc.com/lc7/welcome/customer/PICTtest.aspx*" e clique em **Avançar**.
   
    ![Configurar URL do Aplicativo](./media/active-directory-saas-scc-lifecycle-tutorial/IC794124.png "Configurar URL do Aplicativo")
4. Na página **Configurar logon único no SCC LifeCycle**, clique em **Baixar metadados** e salve o arquivo de metadados localmente no computador.
   
   ![Configurar Logon Único](./media/active-directory-saas-scc-lifecycle-tutorial/IC795083.png "Configurar Logon Único")
5. Encaminhe esse arquivo de Metadados à equipe de Suporte do SCC LifeCycle.
   
   >[!NOTE]
   >O logon único deve ser habilitado pela equipe de suporte do SCC LifeCycle.
   > 
   > 

6. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
    ![Configurar Logon Único](./media/active-directory-saas-scc-lifecycle-tutorial/IC794125.png "Configurar Logon Único")
   
## <a name="configure-user-provisioning"></a>Configurar provisionamento do usuário

Para permitir que os usuários do AD do Azure façam logon no SCC LifeCycle, eles deverão ser provisionados no SCC LifeCycle. Não há nenhum item de ação para a configuração de provisionamento de usuário para o SCC LifeCycle.

Quando um usuário atribuído tentar fazer logon no SCC LifeCycle, uma conta do SCC LifeCycle será automaticamente criada, se necessário.

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do SCC LifeCycle ou as APIs fornecidas pelo SCC LifeCycle para provisionar as contas de usuário do AAD.
> 
> 

## <a name="assign-users"></a>Atribuir usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

**Para atribuir usuários ao SCC LifeCycle, execute as seguintes etapas:**

1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos do **SCC LifeCycle**, clique em **Atribuir usuários**.
   
    ![Atribuir Usuários](./media/active-directory-saas-scc-lifecycle-tutorial/IC794126.png "Atribuir Usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
    ![Sim](./media/active-directory-saas-scc-lifecycle-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de SSO, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

