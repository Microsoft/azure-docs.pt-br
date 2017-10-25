---
title: "Tutorial: Integração do Azure Active Directory com o Qualtrics | Microsoft Docs"
description: "Saiba como usar o Qualtrics com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 4df889ab-2685-4d15-a163-1ba26567eeda
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/23/2017
ms.author: jeedes
ms.openlocfilehash: 2fcde595a40dafda7549f5bccb582b57585b314e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-qualtrics"></a>Tutorial: Integração do Active Directory do Azure com o Qualtrics
O objetivo deste tutorial é mostrar a integração do Azure com o Qualtrics.  

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura do Qualtrics com SSO (logon único) habilitado

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Qualtrics poderão fazer logon único no aplicativo em seu site de empresa do Qualtrics (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o Qualtrics
2. Configuração do SSO (logon único)
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-qualtrics-tutorial/IC789542.png "Cenário")

## <a name="enabling-the-application-integration-for-qualtrics"></a>Habilitando a integração de aplicativos para o Qualtrics
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Qualtrics.

**Para habilitar a integração de aplicativos com o Qualtrics, execute as seguintes etapas:**

1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-qualtrics-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-qualtrics-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-qualtrics-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-qualtrics-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **Qualtrics**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-qualtrics-tutorial/IC789543.png "Galeria de Aplicativos")
7. No painel de resultados, selecione **Qualtrics** e clique em **Concluir** para adicionar o aplicativo.
   
   ![Qualtrics](./media/active-directory-saas-qualtrics-tutorial/IC789544.png "Qualtrics")
   
## <a name="configure-single-sign-on"></a>Configurar o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Qualtrics com sua conta do AD do Azure usando federação baseada em protocolo SAML.

**Para configurar o logon único, execute as seguintes etapas:**

1. No Portal Clássico do Azure, na página de integração de aplicativos do **Qualtrics**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
   ![Configurar Logon Único](./media/active-directory-saas-qualtrics-tutorial/IC789545.png "Configurar Logon Único")
2. Na página **Como você deseja que os usuários façam logon no Qualtrics**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar Logon Único](./media/active-directory-saas-qualtrics-tutorial/IC789546.png "Configurar Logon Único")
3. Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de Entrada do Qualtrics**, digite a URL (por exemplo "*https://ssotest2ut1.qualtrics.com*") e clique em **Avançar**.
   
   ![Configurar URL do Aplicativo](./media/active-directory-saas-qualtrics-tutorial/IC789547.png "Configurar URL do Aplicativo")
4. Na página **Configurar logon único no Qualtrics**, clique em **Baixar metadados** e salve o arquivo de metadados no computador.
   
   ![Configurar Logon Único](./media/active-directory-saas-qualtrics-tutorial/IC789548.png "Configurar Logon Único")
5. Envie o metadatafile para a equipe de suporte do Qualtrics.
   
   >[!NOTE]
   >A configuração de SSO deve ser executada pela equipe de suporte do Qualtrics. Assim que a configuração for concluída, você receberá uma notificação.
   > 
   > 
6. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
   ![Configurar Logon Único](./media/active-directory-saas-qualtrics-tutorial/IC789549.png "Configurar Logon Único")
   
## <a name="configure-user-provisioning"></a>Configurar provisionamento do usuário

Não há nenhum item de ação para a configuração de provisionamento de usuário para o Qualtrics. Quando um usuário atribuído tenta fazer logon no Qualtrics usando o painel de acesso, o Qualtrics verifica se o usuário existe.  

Se ainda não houver uma conta de usuário disponível, ela será automaticamente criada pelo Qualtrics.

## <a name="assign-users"></a>Atribuir usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

**Para atribuir usuários ao Qualtrics, execute as seguintes etapas:**

1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos do **Qualtrics**, clique em **Atribuir usuários**.
   
   ![Atribuir Usuários](./media/active-directory-saas-qualtrics-tutorial/IC789550.png "Atribuir Usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-qualtrics-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de SSO, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

