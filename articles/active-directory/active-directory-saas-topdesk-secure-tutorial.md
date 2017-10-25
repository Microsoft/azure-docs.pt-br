---
title: "Tutorial: integração do Azure Active Directory com o TOPdesk - Secure | Microsoft Docs"
description: "Saiba como usar o TOPdesk - Secure com o Active Directory do Azure para habilitar logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 8e149d2d-7849-48ec-9993-31f4ade5fdb4
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
ms.openlocfilehash: 28f0542dbe87bb34c83a7852db7c3a9fef055ce9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Tutorial: Integração do Active Directory do Azure ao TOPdesk - Secure
O objetivo deste tutorial é mostrar a integração do Azure com o TOPdesk - Secure.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura habilitada para logon único do TOPdesk - Secure

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao TOPdesk - Secure poderão fazer logon único no aplicativo em seu site de empresa do TOPdesk - Secure (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos com o TOPdesk - Secure
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-topdesk-secure-tutorial/IC790596.png "Cenário")

## <a name="enabling-the-application-integration-for-topdesk---secure"></a>Habilitando a integração de aplicativos com o TOPdesk - Secure
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o TOPdesk - Secure.

### <a name="to-enable-the-application-integration-for-topdesk---secure-perform-the-following-steps"></a>Para habilitar a integração de aplicativos com o TOPdesk - Secure, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-topdesk-secure-tutorial/IC700993.png "Active Directory")

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos](./media/active-directory-saas-topdesk-secure-tutorial/IC700994.png "Aplicativos")

4. Clique em **Adicionar** na parte inferior da página.
   
    ![Adicionar aplicativo](./media/active-directory-saas-topdesk-secure-tutorial/IC749321.png "Adicionar aplicativo")

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-topdesk-secure-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6. Na **caixa de pesquisa**, digite **TOPdesk - Secure**.
   
    ![Galeria de Aplicativos](./media/active-directory-saas-topdesk-secure-tutorial/IC790597.png "Galeria de Aplicativos")

7. No painel de resultados, selecione **TOPdesk - Secure** e clique em **Concluir** para adicionar o aplicativo.
   
    ![TOPdesk - Secure](./media/active-directory-saas-topdesk-secure-tutorial/IC791933.png "TOPdesk - Secure")

## <a name="configuring-single-sign-on"></a>Configurando o logon único
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no TOPdesk - Secure com sua conta do AD do Azure usando federação baseada em protocolo SAML.  
Configurar o logon único para o TOPdesk - Secure requer que você carregue um arquivo de ícone de logotipo. Para obter o arquivo do ícone, entre em contato com a equipe de suporte do TOPdesk.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. Faça logon em seu site de empresa do **TOPdesk - Secure** como administrador.
2. No menu **TOPdesk**, clique em **Configurações**.
   
    ![Configurações](./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "Configurações")

3. Clique em **Configurações de Logon**.
   
    ![Configurações de logon](./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "Configurações de logon")

4. Expanda o menu **Configurações de Logon** e clique em **Geral**.
   
    ![Geral](./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "Geral")

5. Na seção **Seguro** da seção de configuração de **Logon do SAML**, realize as seguintes etapas:
   
    ![Configurações técnicas](./media/active-directory-saas-topdesk-secure-tutorial/IC790855.png "Configurações técnicas")
   
    a. Clique em **Baixar** para baixar o arquivo de metadados públicos e salve-o localmente no computador.
   
    b. Abra o arquivo de metadados e localize o nó **AssertionConsumerService** .
    
    ![Serviço de Declaração do Consumidor](./media/active-directory-saas-topdesk-secure-tutorial/IC790856.png "Serviço de Declaração do Consumidor")
   
    c. Copie o valor de **AssertionConsumerService** .  
      
    > [!NOTE]
    > Você precisará do valor na seção **Configurar URL do Aplicativo** mais adiante neste tutorial.
    > 
    > 

6. Em outra janela do navegador da Web, faça logon no **portal clássico do Azure** como administrador.

7. Na página de integração de aplicativos do **TOPdesk – Secure**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único](./media/active-directory-saas-topdesk-secure-tutorial/IC790602.png "Configurar Logon Único")

8. Na página **Como você deseja que os usuários façam logon no TOPdesk - Secure**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-topdesk-secure-tutorial/IC790603.png "Configurar Logon Único")

9. Na página **Configurar URL do Aplicativo** , execute as seguintes etapas:
   
    ![Configurar URL do Aplicativo](./media/active-directory-saas-topdesk-secure-tutorial/IC790604.png "Configurar URL do Aplicativo")
   
    a. Na caixa de texto **URL de Logon do TOPdesk - Secure**, digite a URL usada pelos usuários para entrar no aplicativo TOPdesk - Secure (por exemplo, "*https://qssolutions.topdesk.net*").
   
    b. Na caixa de texto **TOPdesk – URL de Resposta Pública**, cole **TOPdesk - URL AssertionConsumerService Protegida** (por exemplo, "*https://qssolutions.topdesk.net/tas/public/login/saml*")
   
    c. Clique em **Avançar**.

10. Na página **Configurar logon único no TOPdesk - Secure**, para baixar o arquivo de metadados, clique em **Baixar metadados** e salve o arquivo localmente no computador.
    
    ![Configurar Logon Único](./media/active-directory-saas-topdesk-secure-tutorial/IC790605.png "Configurar Logon Único")

11. Execute as seguintes etapas para criar um arquivo de certificado:
    
    ![Certificado](./media/active-directory-saas-topdesk-secure-tutorial/IC790606.png "Certificado")
    
    a. Abra o arquivo de metadados baixado.
    b. Expanda o nó **RoleDescriptor** que contém um **xsi:type** de **fed:ApplicationServiceType**.
    c. Copie o valor do nó **X509Certificate** .
    d. Salve o valor copiado de **X509Certificate** localmente no computador em um arquivo.

12. Em seu site de empresa do TOPdesk - Secure, no menu **TOPdesk**, clique em **Configurações**.
    
    ![Configurações](./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "Configurações")

13. Clique em **Configurações de Logon**.
    
    ![Configurações de logon](./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "Configurações de logon")

14. Expanda o menu **Configurações de Logon** e clique em **Geral**.
    
    ![Geral](./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "Geral")

15. Na seção **Público**, clique em **Adicionar**.
    
    ![Adicionar](./media/active-directory-saas-topdesk-secure-tutorial/IC790607.png "Adicionar")

16. Na página do diálogo **Assistente de configuração do SAML** , realize as seguintes etapas:
    
    ![Assistente de configuração SAML](./media/active-directory-saas-topdesk-secure-tutorial/IC790608.png "Assistente de configuração SAML")
    
    a. Para carregar o arquivo de metadados baixado, em **Metadados de Federação**, clique em **Procurar**.

    b. Para carregar o arquivo de certificado, em **Certificado (RSA)**, clique em **Procurar**.

    c. Para carregar o arquivo de logotipo que você recebeu da equipe de suporte do TOPdesk, em **Ícone do logotipo**, clique em **Procurar**.

    d. Na caixa de texto **Atributo de nome de usuário**, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    e. Na caixa de texto **Nome de exibição** , digite um nome para a sua configuração.

    f. Clique em **Salvar**.

17. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
    
    ![Configurar Logon Único](./media/active-directory-saas-topdesk-secure-tutorial/IC790609.png "Configurar Logon Único")

## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários
Para permitir que os usuários do AD do Azure façam logon no TOPdesk - Secure, eles deverão ser provisionados no TOPdesk - Secure.  
No caso do TOPdesk - Secure, o provisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o provisionamento de usuários, execute as seguintes etapas:
1. Faça logon em seu site de empresa do **TOPdesk - Secure** como administrador.
2. No menu na parte superior, clique em **TOPdesk \> Novo \> Arquivos de Suporte \> Operador**.
   
    ![Operador](./media/active-directory-saas-topdesk-secure-tutorial/IC790610.png "Operador")

3. No diálogo **Novo Operador** , realize as seguintes etapas:
   
    ![Novo operador](./media/active-directory-saas-topdesk-secure-tutorial/IC790611.png "Novo operador")
   
    a. Clique na guia Geral.
   
    b. Na caixa de texto **Sobrenome** na seção **Geral**, digite o sobrenome referente a uma conta válida do Azure Active Directory que você deseja provisionar.
   
    c. Selecione um **Site** para a conta na seção **Local**.
   
    d. Na caixa de texto **Nome de Logon** da seção **Logon no TOPdesk**, digite um nome de logon para o usuário.
   
    e. Clique em **Salvar**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do TOPdesk - Secure ou APIs fornecidas pelo TOPdesk - Secure para provisionar as contas de usuário do AAD.
> 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder aos usuários do AD do Azure que você deseja que usem seu aplicativo acesso a ele.

### <a name="to-assign-users-to-topdesk---secure-perform-the-following-steps"></a>Para atribuir usuários ao TOPdesk - Secure, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos **TOPdesk – Secure**, clique em **Atribuir usuários**.
   
    ![Atribuir Usuários](./media/active-directory-saas-topdesk-secure-tutorial/IC790612.png "Atribuir Usuários")

3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
    ![Sim](./media/active-directory-saas-topdesk-secure-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

